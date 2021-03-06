# Browser

## window

`window` predstavlja prozor koji sadrži DOM document. Svaki browser tab ima svoj `window`. Svaki iframe ima svoj `window`. `window` je globalni objekt pa se svi propertiji unutar njega mogu pozivati s `window.document` ili samo `document`.

`window.document` predstavlja DOM document u kojem se nalaze elementi.
`document.defaultView` daje `window` dokumenta.
`document.documentElement` daje root `<html>` element.

`window.location` daje podatke o trenutnom URLu prozora.
`window.history` daje podatke o session historiju taba ili framea.
`window.navigator` daje podatke o user agentu (platforma, browser, geolokacija, baterija...)
`window.screen` daje podatke o fizičkom ekranu (veličina, orijentacija, pixel depth...)

## Viewport i pixeli

http://www.quirksmode.org/mobile/viewports.html

*Device pixels* su fizički pikseli na tvom ekranu. Ima ih koliko `screen.width` i `screen.height` kažu.
*CSS pixels* su virtualni pikseli koji izgrađuju elemente. Pri zoom levelu `100%` jedan CSS pixel jednak je jednom device pixelu. Ako `div` ima širinu `120px` i zoomiramo 2x, on će zauzimati duplo više fizičkih piksela, ali i dalje `120` CSS pixela.
Device pixeli nas uglavnom ne zanimaju, CSS pixeli su oni s kojima radimo cijelo vrijeme.

*Viewport* je "parent" `<html>` elementa. Veličinom je jednak browser windowu. `<html>` po defaultu zauzima `100%` širine viewporta. Kad napraviš zoom-in, smanjuje se, jer se manje CSS pixela prikazuje. Kad napraviš zoom-out, povećava se.

`screen.width` - veličina ekrana korisnikovog uređaja, u device pixelima. Nije korisno, osim za analitiku.
`screen.availWidth` - veličina ekrana uređaja bez UI taskbarova, u device pixelima.

`window.innerWidth` - veličina browser windowa (sa scrollbarom) u CSS pixelima. _IE9+_
`document.documentElement.clientWidth` - veličina viewporta (bez scrollbara) u CSS pixelima.
`document.documentElement.offsetWidth` - veličina `<html>` elementa u CSS pixelima.

Mouse event properties:
`event.pageX` - koordinate relativne `<html>` elementu u CSS pixelima. To nam najčešće treba. _IE9+_
`event.clientX` - koordinate relativne viewportu u CSS pixelima. To nam nekad treba.
`event.screenX` - koordinate relativne ekranu u device pixelima. To nam nikad ne treba.

Media query properties:
`max-width` - koristi veličinu viewporta (`documentElement.clientWidth`) u CSS pixelima.
`max-device-width` - koristi veličinu ekrana (`screen.width`) u device pixelima.

Kad mobile browser otvara stranicu, pretpostavlja da želimo vidjeti čitavu u desktop veličini,
pa postavlja širinu viewporta na `960px`. I onda sve izgleda sitno i odzumirano.
`<meta name="viewport" content="width=320">` postavlja tu širinu na 320px.
`<meta name="viewport" content="width=device-width">` još bolje, postavlja da prati širinu uređaja.
`<meta name="viewport" content="initial-scale=1">` postavlja početni scale na 1:1 - nema zoomiranja.
`<meta name="viewport" content="maximum-scale=1">` disabla zoom. Nemoj koristiti.

## DOM Basics

Metode za traženje mogu se pozvati nad `document` (za sve) ili `el` (za djecu elementa):
`document.getElementById('menu')` dohvaća element po ID-ju.
`document.getElementsByTagName('p')` dohvaća *live* collection elemenata po tagu.
`document.getElementsByClassName('article')` dohvaća *live* collection elemenata po class. _IE9+_
*Live* collection znači da će se svi novi elementi dodani u DOM tree automatski pojaviti u arrayu. Dovoljno je napraviti jedan poziv i spremiti rezultate.

`document.querySelector(".name a")` vraća prvi element matchan danim selektorom. _IE8+_
`document.querySelectorAll(".name a")` vraća sve elemente matchane danim selektorom. _IE8+_

`el.parentNode` za parent element. `el.nextSibling` i `el.previousSibling` za iteraciju po djeci svog parenta.
`el.childNodes` *live* collection djece. Uključuje text nodeove (čak i sa samo whitespaceom) i comment nodove.
`el.children` *live* collection djece, vraća samo elemente (bez teksta i komentara) _IE9+_
`el.closest(".name)` nalazi najbliži parent (uključujući `el`) koji odgovara zadanom queriju.

`el.innerHTML` vraća string HTMLa sve djece elementa.
`el.textContent` vraća spojen tekst sve djece elementa (bez tagova). _IE9+_

`document.createElement('div')` stvara novi element, ali ga ne dodaje u DOM tree.
`document.createTextNode('blah')` stvara novi text node. Escapea sav HTML, pa je safe za dodavanje nepoznatog teksta.
`el.appendChild(node)` dodaje node kao zadnje dijete elemeneta.
`el.insertBefore(node, el.firstChild)` dodaje node prije danog childa.

`el.removeChild(child)` uklanja child iz DOM stabla. Funkcija vraća uklonjeni node koji ostaje u memoriji ako ga spremiš u `var`.
`el.replaceChild(newChild, oldChild)` mijenja dijete s novim. Vraća uklonjeni node.
`el.textContent=` briše svu djecu i zamjenjuje ih jednim text nodeom. _IE9+_
`el.innerHTML=` briše svu djecu, parsira HTML i izgrađuje djecu iz njega. Oprez: podložno Cross Site Scriptingu.
`el.insertAdjacentHTML(position, text)` parsira HTML i dodaje ih na određenu poziciju. `beforebegin`: neposredno prije elementa, `afterbegin`: na početak elementa, `beforeend`: na kraj elementa, `afterend`: nakon elementa.

`el.cloneNode()` stvara kopiju nodea, a `el.cloneNode(true)` stvara kopiju nodea i sve njegove djece. Klonirani node će imati iste atribute, ali ne i propertije i listenere dodane s `addEventListener`. Klonirani node nije dio dokumenta dok se ne doda s `appendChild` ili sličnim.

`document.importNode(template.content, true)` klonira novi node iz `<template>` elementa.

## DOM Ready i Loaded

Da bi manipulirao elementima, DOM stablo mora biti izgrađeno. To se da očitati iz `document.readyState` propertija.
  * ako scriptu staviš na kraj `<body>` taga, DOM će u trenutku izvršavanja sigurno biti spreman.
  * inače, za _IE9+_ koristi `if (document.readyState === "complete")` i `document.addEventListener("DOMContentLoaded")`.
  * za starije browsere, koristi JQuery `$(function() {...})` (izbjegavaj `$(document).ready()` jer je deprecated).

Za provjeru da su stranica i svi njeni resursi (`img`, skripte, css) loadani, koristi `window.addEventListener("load")`.

## DOM Classes, Attributes and Properties

`el.className` string razmacima odvojenih klasa elementa.
`el.classList` *live* array stringova s klasama elementa. Puno bolje. _IE10+_
`el.classList.contains('blue')` provjerava ima li element klasu. _IE10+_
`el.classList.add('blue')`, `remove`, `toggle` dodaje i uklanja klasu. _IE10+_

*Attribute* je dio HTMLa, koristi ga ako želiš mijenjati HTML. Vrijednost može biti samo *string*.
*Property* je dio DOM objekta, koristi ga ako želiš mijenjati objekt.
Propertiji uvijek dobivaju početnu vrijednost od atributa.
  * Neki su povezani, pa se promjena propertija očituje i u atributu (npr. `id`)
  * Neki propertiji imaju dodatna ograničenja (npr. `input.type` ne dopušta nestandardne vrijednosti, a `setAttribute('type')` dopušta)
  * Neki su potpuno odvojeni nakon postavljanja (`input.value` i `input.checked` ne mijenjaju atribut. `input.defaultValue` je zapravo povezan s `value` atributom)

`el.getAttribute()`, `el.setAttribute()` pristupa *atributu*.
`el.id`, `el.id=` pristupa *propertiju*.

Preferiraj koristiti propertije, pogotovo s boolean vrijednostima. Atributi za boolean moraju koristiti `setAttribute('disabled', false)` i `removeAttribute('disabled')`, a property samo `disabled = true` i `disabled = false`

*Custom Data Attributes* dostupni su preko `el.dataset` propertija. Propertiji koriste camelcase koji se za HTML prevodi u dash-style, npr. `el.dataset.dateOfBirth` mapira se u `data-date-of-birth`. _IE 11+_

## DOM Events

Kada se dogodi event (npr `click`) prvo se šalje od root elementa (*capturing*) do najdubljeg djeteta na kojem se dogodio (*target*), tamo se trigerira i onda šalje nazad prema rootu (*bubbling*). Najčešće stavljamo listenere na tu drugu fazu.

`el.addEventListener('click', callback)` dodaje callback na event koji će se izvršiti pri bubblingu.
`el.addEventListener('click', callback, true)` dodaje callback koji će se izvršiti pri captureu. Korisno za evente koji ne bubblaju, npr. `focus` i `blur`.
`el.removeEventListener('click', callback)` uklanja callback funkciju. Trebaš je imati spremljenu u varijabli. Ako želiš obrisati sve listenere, najlakše je klonirati element, jer se listeneri ne kloniraju.
`el.dispatchEvent(event)` šalje event na element, gdje prolazi kroz normalni capturing i bubbling i triggera listenere.

Unutar callback funkcije:
`event.target` najdublji element od kojeg je event počeo.
`event.currentTarget` element na kojem smo trenutno u bubblanju. Na njega je ovaj listener dodan.
`event.stopPropagation()` prekida propagaciju eventa (capturing i bubbling).
`event.stopImmediatePropagation()` prekida izvršavanje ostalih listenera na elementu.
`event.preventDefault()` spriječava izvršenje defaultne akcije (npr. da se tekst unese u input ili da se checkbox toggla), ali se propagacija nastavlja.

Sve ovo navedeno je _IE9+_. Starije verzije _IE_ koriste sasvim drugi API, `attachEvent`, `event.srcElement` itd.

## DOM Changes

Za praćenje bilo kakvih promjena na DOM-u, koristi se MutationObserver.

`observer = new MutationObserver(callback)` stvara novog observera, callback funkcija će se pozvati za svaku detektiranu promjenu.

`observer.observe(el, options)` postavlja što se prati: `childList` (dodavanje djece), `attributes` (promjenu vrijednosti atributa), `characterData` (promjenu teksta).

## CSSOM

`document.styleSheets` vraća listu svih stylesheetova u dokumentu. Svaki stylesheet ima property `href` ako je remote i `media` ako je definiran atributom. Može se disablati s `document.styleSheets[i].disabled = true`.

Stylesheet se može dohvatiti i direktno od `link` ili `style` elementa s `el.sheet`.

Svaki stylesheet ima `cssRules`, live listu postavljenih CSS selektora i stilova. Možeš dodavati nova pravila s `stylesheet.insertRule("#blanc { color: white }", 0)` i `stylesheet.deleteRule(0)`.

CSS propertiji elementa mogu se dohvatiti pomoću `el.style` propertija. Za postavljanje jednog propertija koristi `el.style.color = "red"`. Za podešavanje više stilova, `el.style.cssText = "color: red; padding: 0;"`.

`CSS.supports("display", "flex")` provjerava je li par property-value podržan u brosweru. _Chrome, FF_

`window.matchMedia("(orientation: portrait)").matches` provjerava je li media query trenutno ispunjen. `.addListener` za dodavanje callbacka koji će biti pozvan svaki put kad se orijentacija promijeni.

## DOM Dimensions and Positioning

Veličina elementa:
`el.offsetWidth` - veličina koju element zauzima u layoutu, uključuje i border.
`el.getBoundingClientRect().width` - isto kao i `offsetWidth`, ali uzima u obzir transformacije tipa `scale()` _IE9+_
`el.clientWidth` - veličina vidljivog sadržaja, uključuje padding, ali bez bordera i scrollbara.
`el.scrollWidth` - veličina cijelog sadržaja, uključuje padding i nevidljivi dio do kojeg se može scrollati.

Pozicija elementa:
`el.getBoundingClientRect().left` (`top`, `right`, `bottom`) vraćaju relativnu poziciju od rubova viewporta.
`el.getBoundingClientRect().top + window.pageYOffset` daje apsolutnu poziciju unutar `body` elementa.
`el.offsetLeft` (`offsetTop`) udaljenost od parenta (za `position: absolute`, od parenta prema kojem je pozicioniran)
`el.clientLeft` (`clientTop`) udaljenost sadržaja od lijevog ruba elementa, tj. širina lijevog bordera.

`window.addEventListener('resize', ...)` osluškuje resize prozora. Pošto se poziva jako puno puta u kratkom vremenu, koristi throttling za složene callbackove

## DOM Scrolling

`window.scrollY` (alias `window.pageYOffset`) za koliko pixela je cijeli dokument odscrollan vertikalno. _IE9+_, za starije koristi `document.documentElement.scrollTop`.
`el.scrollTop` (`scrollLeft`) za koliko pixela je element odscrollan.

`window.scroll(x, y)` scrolla dokument do danih koordinata.
`window.scrollBy(x, y)` scrolla dokument za danu količinu piksela.
`el.scrollTop = ` scrolla element do danih koordinata.
`el.scrollIntoView()` scrolla dokument dok element nije cijeli vidljiv.
`el.scrollIntoView({behavior: 'smooth', block: 'center'})` scrolla smoothly dok element nije centriran.

`window.addEventListener('scroll', ...)` osluškuje scroll cijelog dokumenta. Često uz `scroll`, želiš slušati i `resize`. Kao i kod njega, treba throttlati callback.

## Errors

Kada se dogodi JS runtime error, trigerira se `error` event na `window` objektu. Možeš se zakačiti na taj globalni event s `window.onerror =` ili `window.addEventListener('error', ...)`.

Kada se resource (`<img>` ili `<script>`) ne uspije loadati, trigerira se `error` event na elementu koji je započeo load. Zbog kompatibilnosti, preporuča se korištenja `onerror` handlera umjesto `addEventListener`.

## Shadow DOM _Chrome, Safari_

Shadow DOM je način za izoliranje DOM stabla određenog elementa. CSS unutar shadow DOM-a je neovisan od ostatka stranice, što je vrlo korisno u slučaju da želiš stvoriti custom web komponentu.
* `shadowEl = el.attachShadow({mode: 'open'})` dodaje shadow DOM elementu. `shadowEl` nodeu možeš dodavati HTML pomoću `innerHTML` ili `appendChild`
* `<node name="x">` koristi se ako želiš omogućiti korisniku tvoje komponente da unese svoj HTML u Shadow DOM.
* CSS u Shadowu definiraj unutar `<style>` tagova. Koristi `:host` za stiliziranje parent elementa, npr. `:host([disabled])`

## window.location

Svi propertiji u `window.location` se mogu promijeniti, što se odmah reflektira u browseru (npr. radi se novi request).
Promjena lokacije je dozvoljena *samo ako* skripta koja je izvršava ima isti `origin` (protokol i host) kao i lokacija.

`window.location.href` - vraća cijeli URL.
`window.location.protocol` - protokol, s `:` na kraju
`window.location.host` - domena, odnosno `location.hostname` + `:` + `location.port`
`window.location.origin` - protokol + host _Chrome i FF_
`window.location.pathname` - path, s `/` na početku
`window.location.search` - querystring s `?` na početku
`window.location.hash` - fragment s `#` na početku

`window.location.assign("...")` ili `window.location = ` učitava novi resource.
`window.location.replace("...")` učitava novi resource, ali trenutni URL ne stavlja u `history` (neće ga biti kad odeš back)
`window.location.reload()` reloada trenutni resource. `reload(true)` reloada sa servera, inače može koristiti browser cache.

## window.history

`window.history.length` vraća koliko je URLa u session historiju.
`window.history.back()` kao da je user kliknuo Back.
`window.history.forward()` kao da je user kliknuo Forward.
`window.history.go(-2)` ide 2 koraka unazad (ili unaprijed za pozitivan broj).

`window.history.pushState(state, title, url)` dodaje state na vrh session history stacka. Svaki put kad korisnik navigira u novi state, dogodi se `popstate` event sa trenutnim `state`om. `url` će zamijeniti trenutni url u address baru, ali se *neće reloadati*. `title` se trenutno ne koristi.
`window.history.replaceState(state, title, url)` radi isto kao `pushState`, samo zamjenjuje trenutni `state` danim.
`window.history.state` vraća trenutni `state` bez pozivanja eventa.

`window.history.scrollRestoration` ponašanje scrolla. `auto` daje browseru da odluči, `manual` postavlja na vrh stranice. _Chrome i FF_

## Windows & Iframes

`window.open(url)` otvara url u novom browsing contextu, kao `target=_blank`.

`window.open(url, name)` otvara url u postojećem browsing contextu (prozor, tab ili iframe) s danim imenom. Ako context pod tim imenom ne postoji, stvorit će novi, najčešće u novom tabu.

`window.open(url, name, options)` definira postavke prozora.
`width`, `height`, `top`, `left` otvaraju prozor kao popup.
`titlebar`, `menubar`, `location`. `personalbar` za trake s naslovom, menijem, adresom i bookmarcima.

Moderni browseri imaju ugrađenu zaštitu protiv popupa, i dopuštaju `window.open` samo ako je nastao zbog korisničke interkacije (klika ili tipke).

`child = window.open(...)` vraća window objekt novostvorenog prozora.
`window.opener` iz childa vraća window koji ga je otvorio. Ovaj property imaju i tabovi koji su se otvorili preko `<a target=_blank>`.

`child.focus()` vraća prozor u fokus ako je minimiziran ili u pozadini.

Za iframeove i parente:
`window.frames` vraća nested window objekte u trenutnom prozoru.
`window.parent` i `window.top` vraćaju parent i root window u kojem si embeddan. Ako nisi embeddan, vraćaju tvoj `window`.
`iframe.contentWindow` vraća window unutar `<iframe>` elementa.

Ako window objekti imaju **isti origin** (host, port, i protokol), mogu raditi što hoće jedan s drugim: pristupati varijablama i funcijama u `window` objektu, mijenjati propertije itd.

Ako windowi objekti imaju **istu second-level domenu** (`a.site.com` i `b.site.com`), postavi `document.domain = 'site.com'` na oboje i možeš ih koristiti kao da imaju isti origin.

U svim ostalim slučajevima, pristup ti je ograničen na `blur()`, `focus()`, `close()`, `location.replace()`, i `postMessage()`. Iframe koji ima `sandbox` atribut će također biti ovako ograničen, čak i ako su na istom originu.

## window.postMessage()

`postMessage` omogućuje komunikaciju između dvaju prozora koji mogu biti i cross-origin. Dovoljno je da prozor koji šalje poruku ima referencu na `window` objekt primatelja, npr. od `window.open()`, `window.parent` ili `iframe.contentWindow`.

`child.postMessage({a: 1}, "http://receiver.com")` šalje objekt `child` prozoru. Drugi argument je origin kojeg primatelj mora imati da bi poruka bila poslana. To služi kao osiguranje da se poruka ne pošalje na zlonamjerni site na koji je `child` prozor redirectan. Ako ti je u redu slati bilo kome, možeš koristiti `child.postMessage({a: 1}, "*")`.

`window.addEventListener("message", e => ...)` u `child` prozoru prima sve poruke poslane pomoću `postMessage`. Event objekt sadrži `data` (poslani objekt), `origin` prozora koji je poslao poruku i `source` (referencu na window objekt pošiljatelja). U primatelju prije obrade poruke uvijek provjeri stiže li s origina kojeg očekuješ: `if (event.origin == "http://sender.com")`.

## Form Events

* `input` kada se vrijednost inputa promjeni.
* `change` starija verzija `input`, poziva se samo kad se element odfokusira nakon promjene. Npr. povlačenje `range` inputa neće pozvati `change` dok korisnik ne otpusti miša. Za kompatibilnost koristi `addEventListener("change input")`.
* `submit` se poziva nad elementom forme prije nego se submita.

## Mouse Events

Mouse eventovi:
* `mousedown` mouse button je pritisnut, `mouseup` mouse button je otpušten
* `click` poziva se nakon `mousedown` i `mouseup`
* `dblclick` poziva se nakon doubleclicka (što je doubleclick određuje OS). U isto vrijeme poziva i `click` dvaput.
* `contextmenu` prije nego se context menu prikaže. Spriječi ga s `preventDefault`.
* `mouseenter`, `mouseleave` pri ulasku i izlasku iz elementa.
* `mouseover`, `mouseout` pri ulasku i izlasku iz elementa. Zove se posebno za svaki child elementa.
* `mousemove` svaki put kad se miš pomakne, znači stalno. Throttlaj ga.

Svaki mouse event ima propertije:
* `e.pageX`, `e.pageY` koordinate unutar `<html>` elementa _IE9+_
* `e.clientX`, `e.clientY` koordinate unutar viewporta.
* `e.button` button koji se promijenio (pritisnut/otpušten) u ovom eventu. `0` left, `1` middle, `2` right
* `e.buttons` buttoni koji su trenutno stisnuti.

## Touch Events

Touch eventovi:
* `touchstart` prst je stavljen na element
* `touchmove` prst se pomiče po elementu
* `touchend` prst je podignut s elementa
* `touchcancel` prst je pobjegao u browser UI ili je nekako drugačije cancelan

Svaki touch event ima sljedeće propertije:
* `touches` lista toucheva koji su trenutno na ekranu
* `targetTouches` lista toucheva koji su na trenutnom elementu
* `changedTouches` lista toucheva koji su se promijenili (podigli/spustili) u ovom eventu.

Touch objekt sastoji se od propertija:
* `t.identifier` id za prst
* `t.pageX`, `t.pageY` koordinate unutar `<html>` elementa
* `t.clientX`, `t.clientY` koordinate unutar viewporta.
* `t.target` element na kojem je touch započeo

Browser će u slučaju touch eventa emulirati i mouse eventove, i to ovim redom:
* `touchstart`, `touchmove` (ako se kreće), `touchend`, `mousemove`, `mousedown`, `mouseup`, `click`
* Ako želiš da se odvojeno handlaju samo touch eventovi, dodaj `e.preventDefault()` u `touchstart`.

Detektirati da li korisnikov uređaj podržava touch je prekomplicirano. Umjesto toga, puno bolje je detektirati da li korisnik koristi touch: s `window.addEventListener('touchstart', onFirstTouch)` postavi globalni flag ili dodaj klasu na `body`, pa ukloni listener s `window.removeEventListener('touchstart', onFirstTouch)`.

Isto vrijedi i za hover. Želiš li prikazati tooltip na hover korisnicima s mišem, a na touch korisnicima bez njega, samo detektiraj koristi li korisnik `window.removeEventListener('mouseover', ...)`.

## Pointer Events _IE 11+, FF, Chrome prefixed_

Pointer eventovi su unifikacija mouse i touch eventova:
* `pointerdown` aktivni button ili kontakt, `pointerup` otpuštanje buttona ili kontakta
* `pointerover`, `pointerout` pri ulasku i izlasku iz elementa. Na hover za pointere koji imaju hover, inače prije `pointerdown`, odnosno nakon `pointerup`.
* `pointerenter`, `pointerleave` isto kao i gornji, samo ne gleda children.
* `pointermove` promijenio poziciju
* `pointercancel` dogodilo se nešto da pointer neće slati više eventova. Cancelaj sve in-progress akcije. Ovo također može značiti da je browser preuzeo kontrolu nad pointerom, npr. za pinch-zoom.

Svaki pointer event ima `pointerType`: `mouse`, `touch`, `pen`, blank ako browser ne zna o čemu se radi.

Može se dogoditi da korisnik klikne na element, ali pusti button izvan elementa, što neće triggerati `mouseup` na elementu. Pointer eventi dopuštaju `el.setPointerCapture(e.pointerId)`, što će na elementu triggerirati sve buduće evente tog pointera.

## Drag and Drop API

Element označi `draggable` atributom i definiraj listener:
`element.addEventListener("dragstart", handler)`

U handleru definiraj podatke koji se dragaju, npr.
`e.dataTransfer.setData("text/plain", e.target.id)`
`e.dataTransfer.setData("text/plain", e.target.innerText)`
`e.dataTransfer.setData("text/html", e.target.outerHTML)`

`ev.dataTransfer.dropEffect = 'copy'` definira kako će se prikazati korisnički kursor. Može biti `copy`, `move`, `link`.

Drop zonu definiraj s `dragover`, `dragenter`, `dragleave` i `drop` listenerima:
`e.target.appendChild(e.dataTransfer.getData("text/html"))`

Svakako dodaj `e.preventDefault()` u drop zone listenere da izbjegneš koliziju s drugim touch i pointer eventima.

## Editable Content

`contenteditable="true"` (`true` je obavezan) atribut na elementu učinit će taj element editabilnim u browseru, čak i ako se radi o `div` ili `label` elementu. Sva djeca elementa će također naslijediti vrijednost atributa.

`contenteditable` je koristan za izradu WYSIWYG editora, ali zbog kompleksnosti problema različita ponašanja nisu definirana niti konzistentna među browserima.

`document.designMode = "on"` učinit će cijelu stranicu editabilnom.

`document.execCommand` omogućava izvršavanje naredbi unutar `contenteditable` elementa, npr:
* `document.execCommand("bold")` bolda selektirani tekst
* `document.execCommand("cut")`, `document.execCommand("paste")` cut i paste selektiranog teksta.
* `document.execCommand("undo")` undoa zadnju izvršenu naredbu.

`document.queryCommandSupported('copy')` vraća `true` ako je naredba podržana.

## Selection API

`selection = window.getSelection()` vraća live Selection objekt s podatcima o komadu teksta kojeg je korisnik selektirao.

`selection.anchorNode` vraća element ili text node u kojem je korisnik započeo selekciju (npr. pritisnuo tipku miša). `selection.focusNode` vraća element ili text node u kojem je korisnik završio selekciju (npr. otpustio tipku miša).

Svaka selekcija ima range objekt, `selection.getRangeAt(0)`. Range može sadržavati cijele nodeove ili njihove dijelove. Tehnički, selekcija može imati više rangeova (više selektiranih dijelova teksta odjednom), ali browseri podržavaju samo jedan.

`selection.toString()` vraća selektirani tekst.

Za selektiranje `input` i `textarea` elemenata koristi `el.select()`.

Za selektiranje proizvoljnog elementa, stvori novi range s `range = document.createRange()` i `range.selectNode(node)`, pa ga selektiraj s `selection.addRange(range)`.

Za deselektiranje: `selection.removeAllRanges()`.

`selectionstart` event se triggerira kad user počne selektirati.
`selectionchange` kada se selekcija promijeni.

## Clipboard API

`navigator.clipboard.writeText(copyText)` kopira text u clipboard.
`navigator.clipboard.write(data)` omogućuje kopiranje drugog tipa sadržaja, npr. Blob za kopiranje slika. `data` je oblika `[new ClipboardItem({ [blob.type]: blob })]`

Programsko dodavanje podataka u clipboard omogućuje pastejacking, pa budi oprezan kada pasteaš u kritična mjesta (npr. u terminal). Čitanje iz clipboarda će tražiti userovo dopuštenje, ali ne vidim razlog da se ikad koristi.

## Fullscreen

`document.fullscreenEnabled` provjerava može li se ići u fullscreen.
`element.requestFullscreen()` će prikazati element u fullscreenu.
`document.fullscreenElement` element koji je trenutno u fullscreenu (ili null).
`document.exitFullscreen()` izlazi iz fullscreena.

## Geolocation

`position = navigator.geolocation.getCurrentPosition()` vraća podatke o trenutnoj lokaciji uređaja.

`position.coords` sadrži podatke o lokaciji:
* `latitude`, `longitude`, `accuracy` (u metrima)
* `altitude` (ako podržava), `altitudeAccuracy` (u metrima)
* `speed` (u m/s), `heading` (azimut u stupnjevima).
`position.timestamp` je vrijeme kada se lokacija dohvatila.

`id = navigator.geolocation.watchPosition(callback, error, options)` poziva se kad se lokacija uređaja promijeni. `navigator.geolocation.clearWatch(id)` za odjavu callbacka.

Opcije koje se mogu poslati pri dohvatu lokacije:
* `enableHighAccuracy: true` zahtjeva najpreciznije moguće rezultate, što može uzrokovati sporije vrijeme odgovora i veću potrošnju baterije.
* `timeout: 300` vrijeme u milisekundama u kojem uređaj mora vratiti lokaciju.
* `maximumAge: 1000` vrijeme u milisekundama koliko uređaj može cachirati trenutnu lokaciju. Defaultno je `0`.

## Device Orientation and Motion

`window.addEventListener('deviceorientation', e => ...)` osluškuje promjenu orijentacije uređaja. Event objekt ima sljedeće propertije:
* `e.alpha` rotacija oko z-osi. `0` za smjer prema sjeveru. Rotiranjem u lijevo povećava se do 360.
* `e.beta` rotacija oko x-osi. `0` ako je paralelan sa zemljom. Nagibom prema nazad povećava se do `180`, nagibom prema naprijed smanjuje se do `-180`. (Kod _FF_ je suprotno)
* `e.gamma` rotacija oko y-osi. `0` ako je paralelan sa zemljom. Nagibom ulijevo smanjuje se do `-90`, nagibom udesno povećava se do `90`.

`window.addEventListener('devicemotion', e => ...)` osluškuje kretanje uređaja. Event objekt ima sljedeće propertije:
* `e.acceleration` akceleracija po osima `x` (zapad-istok), `y` (jug-sjever) i `z` (gore-dolje) u m/s^2. Kompenzira utjecaj gravitacije, pa ga podržavaju samo neki uređaji.
* `e.accelerationIncludingGravity` isto kao gore, ali zbraja akceleraciju i gravitaciju. Nije korisno kao `acceleration`, ali može poslužiti za uređaje koji nemaju giroskop.
* `e.rotationRate` brzina rotacije po kutevima `alpha`, `beta` i `gamma`.
* `e.interval` interval u kojem su prikupljeni podatci, u ms.

Zahtjeva HTTPS za korištenje.

## Media Devices

Omogućuje korištenje ulaznih uređaja poput kamere i mikrofona.

`navigator.mediaDevices.enumerateDevices().then(devices => ...)` vraća array `MediaDeviceInfo` objekata:
* `device.deviceId` za ID uređaja.
* `device.kind` može biti `audioinput`, `audiooutput`, ili `videoinput`.
* `device.label` za naziv uređaja.

`element.setSinkId(deviceId)` se može iskoristiti kako bi se promjenio output destination `<audio>` ili `<video>` elementa.

`navigator.mediaDevices.getUserMedia(constraints).then(stream => ...)` zahtjeva permission usera za korištenje input devicea. Ukoliko je odobren, vraća promise s `MediaStream` objektom.

`constraint` je oblika `{audio: true, video: true}`. Za korištenje prednje kamere `{video: {facingMode: {exact: "user"}}}`.

Možemo izvuči frameove iz video streama pomoću:
`imageCapture = new ImageCapture(mediaStream.getVideoTracks()[0])`
`imageCapture.grabFrame().then(imageBitmap => ...)` vraća ImageBitmap.
`imageCapture.takePhoto().then(blob => ...)` vraća Blob.

`navigator.mediaDevices.addEventListener('devicechange', ...)` za prepoznavanje ako se novi input uređaj spoji na računalo.

## Media Session API

Kada se pušta `audio` ili `video` na mobilnom browseru, u notification panelu prikazat će se mali player.

`navigator.mediaSession.metadata = new MediaMetadata({ title: ...})` dodaje podatke o snimci koja se trenutno pušta u taj notification. Dodaj ovaj kod u callback `play` eventa. Podatci će se sami ukloniti kada snimka završi.

`navigator.mediaSession.setActionHandler('nexttrack', ...)` definira ponašanje kada se klikne na next button u notificationu. Ovaj i drugi buttoni (`previoustrack`, `seekbackward`, `seekforward`) će se prikazati samo ako je definiran action handler.

## Speech Synthesis

`window.speechSynthesis.getVoices()` dohvaća array različitih glasova koji se mogu koristiti. Dohvati ih kad budu dostupni, na event `speechSynthesis.addEventListener("voiceschanged")`

`utterance = new SpeechSynthesisUtterance(text)` koristi se za generiranje govora. Može mu se postaviti `voice` (iz `getVoices()`), `pitch` (`0` do `2`), `rate` (`0.1` do `10`), i `volume` (`0` do `1`).

`window.speechSynthesis.speak(utterance)` za puštanje govora.
`window.speechSynthesis.pause()` i `window.speechSynthesis.cancel()` za zaustavljanje.

## Speech Recognition

`recognition = new SpeechRecognition()` stvara objekt zadužen za prepoznavanje govora.
`recognition.continuous = true` nastavlja prepoznavati i nakon što korisnik prestane govoriti.
`recognition.interimResults = true` daje ranije rezultate koji se mogu promijeniti.
`recognition.lang = 'en-US'` odabire jezik.

`recognition.start()` započinje prepoznavanje, a rezultati stižu kroz `onresult` event. Tamo je `event.results` array sa svim dosadašnjim rezultatima, te `event.resultIndex` koji određuje zadnji rezultat.

## Vibration API

Browser može reći mobitelu da vibrira.

`navigator.vibrate(200)` vibrira 200ms.
`navigator.vibrate([200, 100, 200])` vibrira 200ms, pauza 100ms, vibrira 200ms.
`navigator.vibrate(0)` prekida trenutnu vibraciju.

## Timing API

`window.performance.getEntries()` vraća performance timeline, array `PerformanceEntry` objekata različitih `entryType` atributa koji svi imaju `startTime` i `duration`.

*Resource Timing* mjeri loadanje određenog resourca. Dostupan je u `window.performance.getEntriesByType("resource")`. Svaki objekt sadrži intervale dostupne pod atributima: `startTime` > `redirectStart` > `redirectEnd` > `fetchStart` > `domainLookupStart` > `domainLookupEnd` > `connectStart` > `secureConnectionStart` > `connectEnd` > `requestStart` > `responseStart` > `responseEnd`.

*Navigation Timing* mjeri učitavanje i prikaz cijele stranice. Dostupan je u `window.performance.navigation`. Sadrži iste atribute kao i Resource Timing (za dohvaćanje dokumenta), i još dodatno: `responseEnd` > `domInteractive` > `domContentLoadedEventStart` > `domContentLoadedEventEnd` > `domComplete` > `loadEventStart` > `loadEventEnd`.

*User Timing* služi za mjerenje custom korisničkih akcija. Oznake se postavljaju pomoću `performance.mark("actionStart")` i `performance.mark("actionEnd")`. Vrijeme između dvije oznake se mjeri pomoću `performance.measure('actionmark', 'actionStart', 'actionEnd')`. Mjerenja se dohvaćaju pomoću  `window.performance.getEntriesByType("mark")` ili `("measure")`, a brišu s `clearMarks()` i `cleanMeasures()`.

## Feature Detection

Da detektiraš podržava li browser npr. `fetch`, nemoj koristiti `if (window.fetch)` jer neki od propertija mogu vratiti falsey vrijednost iako su podržani (npr. `window.hidden`). Umjesto toga, koristi `if ('fetch' in window)`.

Za podržavanje starijih browsera, najbolje je pri inicijalizaciji detektirati feature koje koristiš, a browser ih nema, i po potrebi skinuti polyfille.

## Url Parsing

`URL("http://www.example.com")` - parsira URL, podržan _svi osim u IE_

## Chrome Devtools

* `$$(".class")` za dohvaćanje elemenata bez jquerija. Search bar u *Elements* također prima CSS selektore.
* `$0` u konzoli vraća trenutno selektirani element.
* `$_` rezultat zadnje linije.

* `console.log()` za obične; `console.warn()` i `console.err()` za obojane poruke.
* `console.group()` i `console.groupEnd()` za grupiranje poruka.
* `console.log("Name: %s ", name)` za interpolaciju
* `console.time('fetching data')` i `console.timeEnd('fetching data')` za mjerenje trajanja.
* `copy("...")` u konzoli dodaje argument u clipboard.

* `getEventListeners(el)` vraća array svih bindanih evenata na elementu.
* `monitorEvents(el)` u konzolu ispusuje sve evente koji se dogode elementu. `monitorEvents(el, ['click'])` za samo određene evente.

* `document.designMode = 'on'` čini sav tekst na siteu editabilnim.
* klik na device frame pa capture screenshot.
* *Network* tab, *Filmstrip* mode za prikaz kako korisnik vidi loadanje.
* *Sources* tab, uključi *Asnyc* kako bi stack traceovi bili potpuni čak i za callbackove. Ovo troši dosta memorije, pa ga isključi kad ti ne treba.
