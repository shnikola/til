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

## window.history
`window.history.length` vraća koliko je URLa u session historiju.
`window.history.back()` kao da je user kliknuo Back.
`window.history.forward()` kao da je user kliknuo Forward.
`window.history.go(-2)` ide 2 koraka unazad (ili unaprijed za pozitivan broj).

`window.history.pushState(state, title, url)` dodaje state na vrh session history stacka. Svaki put kad korisnik navigira u novi state, dogodi se `popstate` event sa trenutnim `state`om. `url` će zamijeniti trenutni url u address baru, ali se *neće reloadati*. `title` se trenutno ne koristi.
`window.history.replaceState(state, title, url)` radi isto kao `pushState`, samo zamjenjuje trenutni `state` danim.
`window.history.state` vraća trenutni `state` bez pozivanja eventa.

`window.history.scrollRestoration` ponašanje scrolla. `auto` daje browseru da odluči, `manual` postavlja na vrh stranice. _Chrome i FF_

## window.opener
`window.opener` vraća prozor koji ga je otvorio s `Window.open` ili preko linka s `target=_blank`.
*Oprez!* Svaki put kada otvaraš link u novom tabu, taj prozor ima pristup tvom windowu s `window.opener`. Ovo radi čak i ako je novi prozor na različitoj domeni od tvoje! Napadač time može redirectati tvoj prozor na phishing site bez da primjetiš (https://mathiasbynens.github.io/rel-noopener/). Rješenje:
  * koristi `rel=noopener` na linkovima. On osigurava da će `window.opener` biti null. Usto i primjetno poboljšava performanse browsera (novi tab ne mora dijeliti isti thread.)
  * ne koristi `target=_blank` na linkovima koje unose useri.


## DOM
Svaki element ima nekoliko propertija koji opisuju njegove dimenzije:
`el.offsetWidth` - najveća vanjska mjera, uključuje i border.
`el.clientWidth` - vidljivi dio elementa, uključuje padding, ali bez bordera i scrollbara.
`el.scrollWidth` - veličina sadržaja, uključuje padding i nevidljivi dio do kojeg se može scrollati.


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
`window.pageXOffset` - koliko je dokument odscrollan horizontalno u CSS pikselima.

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
