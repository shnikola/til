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


## DOM Events
Kada se dogodi event (npr `click`) prvo se šalje od root elementa (*capturing*) do najdubljeg djeteta na kojem se dogodio (*target*), tamo se trigerira i onda šalje nazad prema rootu (*bubbling*). Najčešće stavljamo listenere na tu drugu fazu.

`el.addEventListener('click', callback)` dodaje callback na event koji će se izvršiti pri bubblingu.
`el.addEventListener('click', callback, true)` dodaje callback koji će se izvršiti pri captureu. Korisno za evente koji ne bubblaju, npr. `focus` i `blur`.
`el.removeEventListener('click', callback)` uklanja callback funkciju. Trebaš je imati spremljenu u varijabli. Ako želiš obrisati sve listenere, najlakše je klonirati element, jer se listeneri ne kloniraju.
`el.dispatchEvent(event)` šalje event na element, gdje prolazi kroz normalni capturing i bubbling.

Unutar callback funkcije:
`event.target` najdublji element od kojeg je event počeo.
`event.currentTarget` element na kojem smo trenutno u bubblanju. Na njega je ovaj listener dodan.
`event.stopPropagation()` prekida propagaciju eventa (capturing i bubbling).
`event.stopImmediatePropagation()` prekida izvršavanje ostalih listenera na elementu.
`event.preventDefault()` spriječava izvršenje defaultne akcije (npr. da se tekst unese u input ili da se checkbox toggla), ali se propagacija nastavlja.

Sve ovo navedeno je _IE9+_. Starije verzije _IE_ koriste sasvim drugi API, `attachEvent`, `event.srcElement` itd.


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

`window.addEventListener('resize', ...)` hvata resize prozora. Pošto se poziva jako puno puta u kratkom vremenu, koristi throttling za složene callbackove


## DOM Scrolling
`window.pageYOffset` za koliko pixela je cijeli dokument odscrollan vertikalno. _IE9+_, za starije koristi `document.documentElement.scrollTop`.
`el.scrollTop` (`scrollLeft`) za koliko pixela je element odscrollan.

`window.scroll(x, y)` scrolla dokument do danih koordinata.
`window.scrollBy(x, y)` scrolla dokument za danu količinu piksela.
`el.scrollTop = ` scrolla element do danih koordinata.
`el.scrollIntoView()` scrolla dokument dok element nije cijeli vidljiv. _IE 8+_

`window.addEventListener('scroll', ...)` hvata scroll event cijelog dokumenta. Često uz `scroll`, želiš slušati i `resize`. Kao i kod njega, treba throttlati callback.


## DOM Classes, Attributes and Properties
`el.className` string razmacima odvojenih klasa elementa.
`el.classList` *live* array stringova s klasama elementa. Puno bolje. _IE10+_
`el.classList.contains('blue')` provjerava ima li element klasu. _IE10+_
`el.classList.add('blue')`, `remove`, `toggle` dodaje i uklanja klasu. _IE10+_

*Attribute* je dio HTMLa, koristi ga ako želiš mijenjati HTML. Vrijednost može biti samo *string*.
*Property* je dio DOM objekta, koristi ga ako želiš mijenjati objekt.
Propertiji uvijek dobivaju početnu vrijednost od atributa.
  * Neki su povezani, pa se promjena propertija očituje i u atributu (npr. `id`)
  * Neki imaju dodatna ograničenja na propertije (npr. `input.type` ne dopušta nestandardne vrijednosti, a `setAttribute('type')` dopušta)
  * Neki su potpuno odvojeni nakon postavljanja (`input.value` i `input.checked` ne mijenjaju atribut. `input.defaultValue` je zapravo povezan s `value` atributom)

`el.getAttribute()`, `el.setAttribute()` pristupa *atributu*.
`el.id`, `el.id=` pristupa *propertiju*.

Preferiraj koristiti propertije, pogotovo s boolean vrijednostima. Attributi za boolean moraju koristiti `setAttribute('disabled', false)` i `removeAttribute('disabled')`, a property samo `disabled = true` i `disabled = false`



## Stylesheets
`document.styleSheets`

## window.location
Svi propertiji u `window.location` se mogu promijeniti, što se odmah reflektira u browseru (npr. radi se novi request).
Promjena lokacije je dozvoljena *samo ako* skripta koja je izvršava ima isti `origin` (protokol i domenu) kao i lokacija.

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


## window.frames
`window.frames[i]` vraća windowe embeddane u trenutnom prozoru. `window.frames.length` za broj windowa.
`window.parent` ukoliko je prozor embeddan, vraća parenta. Inače vraća samog sebe.
`window.top` ukoliko je prozor embeddan, vraća top parenta. Inače vraća samog sebe.

`window.frameElement` vraća `<iframe>` element parent windowa u kojem je embeddan.
`iframe.contentWindow` (poziva se na `<iframe>` elementu) vraća window koji se nalazi u njemu.

Svi embeddani frameovi imaju pristup svojim parentima, i svi parenti imaju pristup frameovima.
Ali ako frame i parent imaju različiti `origin` (protokol i domenu), moći će ograničeno pristupiti tuđem `window` objektu:
  * `top`, `parent`, `frames`, i `opener`  propertijima.
  * `blur()`, `focus()`, `close()`, `location.replace()` i `postMessage()` funkcijama.


## window.opener
`window.opener` vraća prozor koji ga je otvorio s `window.open()` ili preko linka s `target=_blank`.
*Oprez!* Svaki put kada otvaraš link u novom tabu, taj prozor ima pristup tvom windowu s `window.opener`. Ovo radi čak i ako je novi prozor na različitoj domeni od tvoje! Napadač time može redirectati tvoj prozor na phishing site bez da primjetiš (https://mathiasbynens.github.io/rel-noopener/). Rješenje:
  * koristi `rel=noopener` na linkovima. On osigurava da će `window.opener` biti null. Usto i primjetno poboljšava performanse browsera (novi tab ne mora dijeliti isti thread.)
  * ne koristi `target=_blank` na linkovima koje unose useri.


## window.history
`window.history.length` vraća koliko je URLa u session historiju.
`window.history.back()` kao da je user kliknuo Back.
`window.history.forward()` kao da je user kliknuo Forward.
`window.history.go(-2)` ide 2 koraka unazad (ili unaprijed za pozitivan broj).

`window.history.pushState(state, title, url)` dodaje state na vrh session history stacka. Svaki put kad korisnik navigira u novi state, dogodi se `popstate` event sa trenutnim `state`om. `url` će zamijeniti trenutni url u address baru, ali se *neće reloadati*. `title` se trenutno ne koristi.
`window.history.replaceState(state, title, url)` radi isto kao `pushState`, samo zamjenjuje trenutni `state` danim.
`window.history.state` vraća trenutni `state` bez pozivanja eventa.

`window.history.scrollRestoration` ponašanje scrolla. `auto` daje browseru da odluči, `manual` postavlja na vrh stranice. _Chrome i FF_


## Vibration API
`window.navigator.vibrate(200)` - vibriraj 200ms
`window.navigator.vibrate([200, 100, 200])` - vibriraj 200ms, pauza 100ms, vibriraj 200ms
`window.navigator.vibrate(0)` - prekini trenutnu vibraciju


## Battery Status API
`window.navigator.getBattery()` - vraća promise s BatteryManager objektom
`BatteryManager.charging` - puni li se trenutno. Event: `chargingchange`
`BatteryManager.chargingTime` - vrijeme u sekundama dok se ne napuni. Event: `chargingtimechange`
`BatteryManager.dischargingTime` - vrijeme u sekundama dok se ne isprazni. Event: `dischargingtimechange`
`BatteryManager.level` - napunjenost baterije između 0.0 i 1.0. Event: `levelchange`


## Url Parsing
`URL("http://www.example.com")` - parsira URL, podržan _svi osim u IE_
