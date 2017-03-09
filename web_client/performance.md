# Performance

## Basic measurement

* https://www.webpagetest.org
* https://developers.google.com/speed/pagespeed/insights/
* YSlow extension

## Critical Path Rendering

https://developers.google.com/web/fundamentals/performance/critical-rendering-path/

Proces renderiranja:
1. Browser čita byteove, prevodi ih u charactere (prema zadanom encodingu), razdvaja tekst u tokene (`<html>`, `<body>`, itd.), tokene pretvara u nodeove s propertijima i pravilima, te objekte povezuje u *stablo*
2. *DOM* stablo izgrađuje se od HTMLa i definira odnose nodeova. *CSSOM* stablo izgrađuje se od CSS-a i definira izgled.
3. *Render tree* se izgrađuje pomoću *DOM* i *CSSOM* stabala. On sadrži samo nodeove potrebne da se prikaže stranica. Preskaču se nodeovi poput `<script>` i `<head>` te elementi koji imaju `display: none;`.
4. *Layout* (reflow) određuje poziciju i veličinu nodeova koristeći *render tree*.
5. *Paint* (rasterize) pretvara sve nodeove u piksele na ekranu.

Da bi se stranica iscrtala po prvi put, potrebni su *DOM* i *CSSOM*. Zato su HTML i CSS *render blocking* resources.

Kada se HTML ili CSS promijene, cijeli proces se ponavlja kako bi se odredilo koje piksele treba iznova nacrtati.

Cilj nam je smanjiti vrijeme provedeno u gornjim koracima kako bi se stranica što prije prikazala, i kako bi se animacije fluidnije izvodile.

Svaki `<link href="style.css">` defaultno blokira prvi render kako bi se izbjegao *flash of unstyled content*. Za označiti CSS kao neblokirajući, dodaj `media` atribut (npr. `media="print"` za printanje ili `media="(min-width: 40em)"` - browser će ga skinuti, ali neće zbog njega čekati na render.

Kada browser pri parsiranju HTMLa dođe do `<script>` elementa (bilo inline, bilo remote), pauzira izgradnju DOM stabla dok se skripta ne dohvati i izvrši. Pritom su skripti dostupni samo elementi definirani iznad nje, jer se ostatak DOM stabla još nije izgradio. Dodatno, samo izvršavanje skripte će čekati dok se *CSSOM* stablo ne izgradi, kako bi se izbjegli race conditioni pri definiranju stilova.

Da bi se stranica iscrtala prvi put što prije, potrebno je smanjiti broj ovih kritičnih (render blocking) resursa, i brojem i veličinom.

## Non-blocking scripts

http://www.growingwiththeweb.com/2014/02/async-vs-defer-attributes.html

Postoji nekoliko načina za izbjeći blokiranje `<script>` elementa.

Stavi `<script>` tagove na dno `<body>` elementa, pa će se učitati zadnje. Ovo će biti potencijalno sporo za velike dokumente.

`<script async>` će skidati skriptu dok se HTML parsira i pauzirati parsiranje samo za izvođenje. Skripte se neće nužno izvesti redom kojim su u dokumentu, pa ga nemoj koristiti ako ovise jedna o drugoj (npr. `jquery`). Inače je super. _IE10+_

`<script defer>` će skidati skriptu dok se HTML parsira i izvesti je tek kad je cijeli HTML parsiran, prije `DOMContentLoaded`. _IE10+_

## Rendering performance

Savjeti za izbjegavanje re-layouta i re-painta:
* Dohvaćanje stila elementa (npr. `.width`) natjerat će browser da flusha, nemoj to raditi često.
* Bolje je napraviti mnogo izmjena na nevidljivom elementu pa ga onda prikazati.
* Bolje je promijeniti `class` elementa nego puno inline styleova.
* Animiraj elemete koji su `absolute` ili `fixed`.
* Nemoj se bindati na `scroll` event, već tretiraj dolazak do određene točke u scroll kao event koji se okine jednom.

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

### Preconnect

`<link rel="preconnect" href="//fonts.googleapis.com">` unaprijed odrađuje DNS lookup, TCP i SSL konekciju na dani host. Korisno ako imaš više blokirajućih resourca s različitih hostova, odradiš im sav connection overhead istovremeno, a oni se naknadno bez overheada skinu serijski. _FF, Chrome_

### Preload

`<link rel="preload" href="image.png">` unaprijed učitava resource za trenutnu stranicu. Browser inače preloada sve resurse navedene u HTMLu (js, css, img), ali ovo je korisno za resource koji se učitavaju iz CSSa ili skripte. _Chrome_

Pomoću `as` atributa u `preload` definiraš je li resource `script`, `style`, `image`, `media` ili `document`, pomoću čega će browser odrediti prioritet skidanja.

Neki od korisnih primjena preloada su:
* `<link rel="preload" href="font.woff2" as="font" type="font/woff2" crossorigin>` preloada font bez da mora čekati da se stylesheet skine. Fontovi zahtjevaju `crossorigin` atribut čak i ako su na istom originu.
* `<link rel="preload" as="style" href="style.css" onload="this.rel='stylesheet'">` asinkrono skidanje stylesheeta bez blokiranja rendera koristeći `onload`.

### Prefetch

`<link rel="dns-prefetch" href="http://www.spreadfirefox.com">` unaprijed dohvaća DNS podatke za domenu kako bi umanjio latenciju DNS resolutiona. Moderni browseri inače to rade automatski za linkove i resurse (js, css, img) prisutne na stranici. Dodaj ako želiš ručno prefetchati neku domenu koje nema na trenutnoj stranici, ali očekuješ da će biti na drugim stranicama. _Chrome, FF, IE10+_

`<link rel="prefetch" href="image.png">` navodi resource koji će vjerojatno biti potreban u nekoj od sljedećih stranica. Browser će ga skinuti i cachirati s niskim prioritetom u backgroundu. _Chrome, FF, IE10+_

### Prerender

`<link rel="prerender" href="//utorkom.com/2">` unaprijed učitava *cijelu stranicu* za koju mislimo da će korisnik ubrzo posjetiti. Budi *ultra oprezan* ako ovo koristiš, jer troši puno bandwitha, i stvara lažne posjete u analitici. _Chrome, IE11+_

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

## Javascript Start-up Performance

https://medium.com/dev-channel/javascript-start-up-performance-69200f43b201

Parsiranje i izvršavanje JS skripte može trajati i nekoliko sekundi, dok je stranica neinteraktivna za to vrijeme.

Što manje JS koda šalješ, to će parsiranje biti brže. Ako imaš mnogo javascripta, šalji samo kod relevantan za trenutni path.

Koristi `async` i `defer` pa će browser parsirati skripte u zasebnom threadu. Chrome je to odnedavno uveo i za regularne, blokirajuće skripte.

## optimize-js

https://github.com/nolanlawson/optimize-js

Moderni JS engini pretpostavljaju da se većina funkcija neće odmah izvoditi, pa rade samo pre-parse koji provjerava ispravnost sintakse. Ali za immediately invoked funkcije ovo će zapravo usporiti izvođenje, jer će se raditi pre-parse i onda puni parse.

`optimize-js` pri buildanju dodaje zagrade na sve IIFE funkcije, kako bi se engineu dalo do znanja da prekoči pre-parse korak. Time se dobije speed boost i do 50%.


## Tim Kadlec: Once more, with feeling

https://www.youtube.com/watch?v=S8B7oYsjBtM

Reakciju bržu od `100ms` mozak osjeća kao instantnu. Nakon `1s` korisnik gubi pažnju.

Zbog DNS, TCP, i SSLa, prvi request će trajati minimalno `500ms`. Zato probaj izbjegavati requeste gdje možeš, i pobrini se da sve što dohvaćaš ima neku vrijednost (jer svakako ima i cijenu).

Koristi resource hitnove (preload, prerender) i optimiziraj critical path loading. Ali prije svega, fokusiraj se na korisnikov doživljaj situacije (npr. zato su dodali zrcala u liftove).

Umjesto spinnera, koristi animaciju prijelaza (kao safari new tab) ili skeleton screen (kao facebook) - bitno je korisniku dati feedback tijekom čekanja. Što neobičnije, to bolje, jer će mu skrenuti pozornost s čekanja.

S druge strane, za teške stvari za koje se mozak naučio da su teške (kao prijava poreza), dodaj delay da ne zbuniš korisnika.

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
