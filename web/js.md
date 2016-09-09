# Javascript

## window
`window` predstavlja prozor koji sadrži DOM document. Svaki browser tab ima svoj `window`. Svaki iframe ima svoj `window`. `window` je globalni objekt pa se svi propertiji unutar njega mogu pozivati s `window.document` ili samo `document`.

`window.document` predstavlja DOM document u kojem se nalaze elementi. `document.defaultView` daje `window` dokumenta.
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
