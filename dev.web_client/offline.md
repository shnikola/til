# Offline

## Service Workers _Chrome, FF_

Service Worker se vrti u zasebnom threadu i nema pristup DOM-u. Workere koji su trenutnu upogonjeni u browseru možeš vidjeti na `chrome://inspect /#service-workers`.

Ako nije pokrenut na `localhost` domeni, service worker zahtjeva HTTPS.

Service workera prvo registriraj s `navigator.serviceWorker.register('/sw.js')` nakon što je stranica loadana. Možeš to napraviti na svaki page load, jer će se browser pobrinuti da se isti service service worker registrira samo jednom.

Lokacija skripte određuje scope service workera - skripti u rootu (`/sw.js`) će se slati eventovi za sve urlove, a skripti u `/example/sw.js` će se slati samo eventovi za urlove koji počinju s `/example/`.

Kada browser registrira workera, šalje mu `install` event. Unutar workera definiraj instalacijske korake s:
`self.addEventListener('install', event => event.waitUntil(...))`.
* `self` je alias za `window`, koristi se u workeru koji zapravo nema prozor
* `event.waitUntil` prima promise i njime provjerava je li instalacija bila uspješna. Ako nije, service worker se neće koristiti.

Unutar instalacije najčešće se cachiraju fileovi potrebni za prikaz stranice, npr: `caches.open('cache-key').then(c => c.addAll(['/', '/style.css', '/script.js']))`. Ako se ijedan od fileova ne uspije cachirati, promise će failati i service worker se neće koristiti. Tako smo sigurni da imamo sve resource koji nam trebaju.

Nakon što je instaliran, service worker prima `fetch` evente koje možeš presresti s `self.addEventListener('fetch', event => event.respondWith(...)`.
* `event.respondWith` prima promise koji rezultira responsom ili network errorom.

Unutar fetcha možemo potražiti odgovor u cacheu s `caches.match(event.request)`, a u slučaju da ga ne nađemo, napraviti request i cachirati ga.

Ako u nekom trenutku deployaš novu service worker skriptu, browser će to detektirati pri loadanju stranice. Pokrenut će novi service worker i pozvati `install` event, ali dok god su tabovi sa starim workerom otvoreni, novi worker će biti u `waiting` stanju. Jednom kad se sve stare stranice zatvore, stari worker će se ugasiti i novi worker će preuzeti kontrolu, pri čemu će mu se poslati `activate` event.

U aktivaciji najčešće želiš napraviti promjene nekompatibilne sa starim workerom, npr. mijenjanje cache keyeva. Ne smiješ ih napraviti u instalaciji, jer je stari worker tu još aktivan.

## Progressive Web Apps

https://infrequently.org/2016/09/what-exactly-makes-something-a-progressive-web-app/

Za mobile Chrome, iskočit će "Add To Homescreen" prompt. Nužni kriteriji da Chrome to prikaže su:
* učitavaju se preko HTTPSa
* Web App Manifest s metadatom o aplikaciji (name, start url, ikona...)
* mogu se učitati i offline (makar samo imali custom offline page)

Još je dobro imati:
* mobile-friendly design
* početno učitavanje u manje od 5 sekundi. Kad je Service Worker loadan, učitavanje u manje od 2 sekunde, neovisno o stanju mreže.
* podržavanje različitih uređaja i browsera
* fluidne animacije

`GoogleChrome/lighthouse` je dobar tool za provjeru svih tih kriterija.

## Offline Storage

Za URL addressable resurse, koristi `Cache API` (dio service workera).

Za sve ostalo, koristi `IndexedDB`. Ako želiš wrapper za promise, koristi neki library kao `localForage` ili `idb-keyval`.

## Detekcija

`navigator.onLine` vraća je li browser online ili ne.
Za evente koristi `online` i `offline` na `window` objektu.

Samo pripazi jer ovdje `online` znači da browser ima konekciju, ali ne i da konekcija ima pristup internetu. Tj. ako je `offline`, sigurno je offline, ali ako je `online` i dalje može failati.

# Literatura

* https://developers.google.com/web/fundamentals/getting-started/primers/service-workers
