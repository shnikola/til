# Performance

## Basic measurement
* https://www.webpagetest.org
* https://developers.google.com/speed/pagespeed/insights/
* YSlow extension


## How browsers work internally
http://www.html5rocks.com/en/tutorials/internals/howbrowserswork/
Svaki browser koristi rendering engine (Trident, Gecko, Webkit, Blink). Renderiranje stranice izvodi se u nekoliko koraka:
  1. Parsiranje HTMLa i izgradnja DOM stabla (elementi i atributi)
  2. Izgradnja Render stabla (vizualni elementi redom kojim će biti prikazani i izračunavanje stylea)
  3. Layout (određivanje pozicija i veličina)
  3. Painting (prolazak kroz render tree i iscrtavanje na UI)
Savjeti za izbjegavanje re-layouta i re-painta:
  * Dohevaćanje stila elementa (npr. `.width`) natjerat će browser da flusha, nemoj to raditi često.
  * Bolje je napraviti mnogo izmjena na nevidljivom elementu pa ga onda prikazati.
  * Bolje je promijeniti `class` elementa nego puno inline styleova.
  * Animiraj elemete koji su `absolute` ili `fixed`.


## Optimiziranje imagea
http://blog.pamelafox.org/2014/01/improving-front-page-performance.html
1. `tinypng` za optimiziranje PNGova, `SVGO` za SVG
2. data-uri za manje slike koje se samo jednom pojavljuju
3. Spriteovi/font za ikonice
4. `Picturefill` za retina/responzivne slike
5. Delayed load za veće slike, videove i iframeove.


## Izbjegavaj document.write
https://developers.google.com/web/updates/2016/08/removing-document-write
Nemoj koristiti `document.write` jer blokira parsiranje HTML-a. Ako je DOM stablo već izgrađeno, morat će ga iznova graditi!
Pogotovo nemoj koristiti `document.write` koji dodaje `<script>` element, jer onda browser mora i na njega čekati. Chrome od verzije `54` odbija loadanje takvih skripti.
Ako baš moraš dinamično loadati skriptu, umjesto `document.write` koristi `document.createElement('script')`.


## Resource Hints
* `<link rel="preconnect" href="//fonts.googleapis.com">` unaprijed odrađuje DNS lookup, TCP i SSL konekciju na dani host. Korisno ako imaš više blokirajućih resourca s različitih hostova, odradiš im sav connection overhead istovremeno. _FF, Chrome_
* `<link rel="preload" href="image.png">` unaprijed učitava resource. Korisno za resource koji nisu automatski preloadani, npr. background-image u CSSu ili nešto iz skripte. Ako trebaš fontove, dodaj `crossorigin`. _Chrome_
* `<link rel="prerender" href="//utorkom.com/2">` unaprijed učitava *cijelu stranicu* za koju mislimo da će korisnik ubrzo posjetiti. Budi *ultra oprezan* ako ovo koristiš, jer troši puno bandwitha, i stvara lažne posjete u analitici. _Chrome, IE11+_


## <script>
Kada browser dođe do `<script>`, prestaje s parsiranjem HTML-a dok se skripa ne učita i izvrši (zbog mogućeg `document.write`).
Da izbjegneš to:
* Stavi `<script>` tagove na dno `<body>` elementa, pa će se učitati zadnje. Potencijalno sporo za velike dokumente.
* `<script async>` ne blokira i izvodi skripte asinkrono čim se skinu. _IE10+_
* `<script defer>` ne blokira i izvodi skripte kad je cijeli HTML parsiran, prije `DOMContentLoaded`. _IE10+_
* Pripazi: `async` ne izvodi skripte po redu, pa ga nemoj koristiti ako ovise jedna o drugoj (npr. `jquery`).

critical path - sve potrebno da browser napravi prvi paint. Želimo ga učiniti što manjim.
critical css - minimalni css koji je potreban za prikazati


## Web Fonts Performance
https://developers.google.com/web/fundamentals/performance/optimizing-content-efficiency/webfont-optimization
Ako želiš podržavati sve browsere, moraš servirati 4 font formata:
* `WOFF 2.0` novijim browserima koji ga podržavaju
* `WOFF` većini browsera
* `TTF` za stare Android browsere
* `EOT` za `< IE 9` browsere
Pobrini se da koristiš gzip kompresiju za `TTF` i `EOT` formate. `WOFF` formati je imaju built-in.

Kada koristiš `@font-face`:
* unutar `src` koristi `format()` kao hint. U tom slučaju browser neće morati skinuti font da bi odredio podržava li ga.
* u slučaju velikog broja glyphova (npr. azijskih jezika), podijeli font na subsetove i koristi `unicode-range`, pa će browser skinuti font samo za gliphove koji se nalaze na stranici.

Fontovi se učitavaju tek kad se izgradi render tree (koji ovisi o DOM i CSSOM trees). Tek nakon što se loada CSS, počet će se loadati potrebni fontovi. Dok se font ne loada neki browseri (_Safari_) neće uopće prikazati tekst (*Flash Of Invisible Text*), a neki će čekati 3 sekunde prije nego prikažu fallback font (_Chrome, Firefox_).

Kako bi to izbjegao:
* `font-display: swap` instantno koristi fallback dok se font ne loada. _Chrome flag_
* `font-display: optional` u slučaju da user ima limitirani bandwith, koristi fallback i uopće ne loadaj font. Koristi ovo svugdje jednom kad bude u browserima. _Chrome flag_
* koristi `Font Loading API` (u razvoju) ili `Font Face Observer` da ručno loadaš i dodaš `.font-loaded` na body kad je spreman. Korisno u slučaju da ne upravljamo `@font-face`om, npr. dohvaćamo CSS s Google Fontsa.

Da minimiziraš *Flash Of Unstyled Text* zbog fallback fonta, prilagodi `font-size` fallbacka da se tekst što manje miče.
Za dodatnu optimizaciju, prvo loadaj samo `normal` weight (browser će simulirati ostale debljine), a tek onda ostale.


## Tim Kadlec: Once more, with feeling
https://www.youtube.com/watch?v=S8B7oYsjBtM
* Reakciju bržu od `100ms` mozak osjeća kao instantnu. Nakon `1s` korisnik gubi pažnju.
* Zbog DNS, TCP, i SSLa, prvi request će trajati minimalno `500ms`. Zato probaj izbjegavati requeste gdje možeš, i pobrini se da sve što dohvaćaš ima neku vrijednost (jer svakako ima i cijenu).
* Koristi resource hitnove (preload, prerender) i optimiziraj critical path loading.
* Ali prije svega, fokusiraj se na korisnikov doživljaj situacije (npr. kako su dodali zrcala u liftove).
* Umjesto spinnera, koristi animaciju prijelaza (kao safari new tab) ili skeleton screen (kao facebook) - bitno je korisniku dati feedback tijekom čekanja. Što neobičnije, to bolje, jer će mu skrenuti pozornost s čekanja.
* S druge strane, za teške stvari za koje se mozak naučio da su teške (kao prijava poreza), dodaj delay da ne zbuniš korisnika.


## Measure Performance With RAIL Model
https://developers.google.com/web/fundamentals/performance/rail
Fokusiraj se na korisnika. Cilj aplikacije nije da bude brza na određenom uređaju, nego da korisnici budu zadovoljni.
* *Response:* odgovaraj na korisnikov input (button click, checkbox, animation start) u manje od `100ms`. Duže od toga i osjetit će se lag. Za akcije kojima treba duže od `500ms`, uvijek daj feedback.
* *Animation:* Za 60fps, imaš `16ms` po frameu. Ali browser ima svog posla, pa računaj da tijekom animacije imaš `8ms` za svoj kod. Skupe izračune obavi prije početka animacije.
* *Idle:* Koristi idle time za obavljanje odgođenog (deferred) posla, npr. preloadaj što manje podataka kako bi se aplikacija brzo loadala, a koristi idle time za loadanje ostatka. Odgođeni posao obavljaj u komadima od `50ms`, kako bi u slučaju da korisnik postane aktivan dovoljno brzo mogli biti responzivni.
* *Load:* Loadaj site unutar `1 sekunde`. U suprotnom, gubiš korisnikovu pažnju.


## Adapting to the Mobile Web Present (2016)
https://www.youtube.com/watch?v=K1SFnrf4jZo
Mobiteli neće nikad imati iste performanse kao i desktop:
* moraju ograničavati CPU usage jer nemaju dobro hlađenje (lik je popravio benchmarke tako što je stavio mobitel na led)
* čitanje iz flash memorije je zbog paralelizma ograničeno prostorom (brzinom usporedivo sa starim diskovima)
* mobilne mreže su puno sporije (4G korisnici većinu vremena nemaju 4G brzinu).

Prosječan mobitel u svijetu postaje sve sporiji: sve više ljudi može priuštiti mobitel, a uređaj koji kupe neće biti najnoviji iPhone.
50% mobilnih korisnika će odustati ako se stranica učitava duže od 3 sekunde.
Prosječno učitavanje stranice na mobilnom uređaju: 19 sekundi.

Kako ispravno testirati stranice za mobilne uređaje:
* Testiraj na uređajima jefitnijim od 200$ koje većina korisnika ima
* Ne vjeruj laptopu, koristi `chrome://inspect`, `Lighthouse` extension i `webpagetest.org`
* Koristi Service Workere za local cache.

## Infinite Scroller
https://developers.google.com/web/updates/2016/07/infinite-scroller
* *No Background*: Runway nema background pa browser ne mora spremati teksture s tisuće pixela visine.
* *Dom recycling*: Držanje svih itema u DOMu je preveliko opterećenje za browser. Zato čuvamo samo dio vidljiv korisniku, a elemente koji odlaze iz vidljivog dijela recikliramo i koristimo kao nove iteme. Veličina i pozicija scrollbara se simulira sa *sentinelom*, elementom od 1px koji rasteže runway.
* *Tombstones*: prilikom čekanja da se novi itemi loadaju, dodajemo fake elemente (tombstones) da ne bude naglog skoka na grupu s novim elementima.
* *Layout*: Svaki item je apsolutno pozicioniran kako recycling ne bi zahtjevao ponovno računanje layouta.
