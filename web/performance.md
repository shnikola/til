# Performance

## Basic measurement
* https://www.webpagetest.org
* YSlow extension
* Google Page Speed


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
4. Picturefill za retina/responzivne slike
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


## Tim Kadlec: Once more, with feeling
https://www.youtube.com/watch?v=S8B7oYsjBtM
* Reakciju bržu od `100ms` mozak osjeća kao instantnu. Nakon `1s` korisnik gubi pažnju.
* Zbog DNS, TCP, i SSLa, prvi request će trajati minimalno `500ms`. Zato probaj izbjegavati requeste gdje možeš, i pobrini se da sve što dohvaćaš ima neku vrijednost (jer svakako ima i cijenu).
* Koristi resource hitnove (preload, prerender) i optimiziraj critical path loading.
* Ali prije svega, fokusiraj se na korisnikov doživljaj situacije (npr. kako su dodali zrcala u liftove).
* Umjesto spinnera, koristi animaciju prijelaza (kao safari new tab) ili skeleton screen (kao facebook) - bitno je korisniku dati feedback tijekom čekanja. Što neobičnije, to bolje, jer će mu skrenuti pozornost s čekanja.
* S druge strane, za teške stvari za koje se mozak naučio da su teške (kao prijava poreza), dodaj delay da ne zbuniš korisnika.


## Infinite Scroller
https://developers.google.com/web/updates/2016/07/infinite-scroller
* *No Background*: Runway nema background pa browser ne mora spremati teksture s tisuće pixela visine.
* *Dom recycling*: Držanje svih itema u DOMu je preveliko opterećenje za browser. Zato čuvamo samo dio vidljiv korisniku, a elemente koji odlaze iz vidljivog dijela recikliramo i koristimo kao nove iteme. Veličina i pozicija scrollbara se simulira sa *sentinelom*, elementom od 1px koji rasteže runway.
* *Tombstones*: prilikom čekanja da se novi itemi loadaju, dodajemo fake elemente (tombstones) da ne bude naglog skoka na grupu s novim elementima.
* *Layout*: Svaki item je apsolutno pozicioniran kako recycling ne bi zahtjevao ponovno računanje layouta.


# Stories

## 30K Page Views for $0.21: A Serverless Story
https://fmlnerd.com/2016/08/16/30k-page-views-for-0-21-a-serverless-story
Site je kalkulator za predviđenu zaradu filmova. Site je statičan i generira se na AWS-u.
* CloudWatch cron job nekoliko puta dnevno trigerira dohvaćanje novih podataka. *Free tier*
* Lambda na trigger dohvaća config s S3 i scrapea podatke, spremajući ih u JSON na S3. *Free tier <1M req/month*
* Iduća Lambda na event kreiranja JSONa dohvaća JSON i stvara od njega JS file i HTML.
* Sav trošak se svodi na S3 (oko *20 centi* mjesečno)


## Reddit 2010 (270M Page Views A Month)
http://highscalability.com/blog/2010/5/17/7-lessons-learned-while-building-reddit-to-270-million-page.html
* `Memcachiraju` sve živo: DB data, session data, renderirani html, memoizirane metode, *rate limiting*, locking, *change password linkove* (ne drže ih u bazi).
* Sve računaju unaprijed i cacheiraju, ništa on demand, npr. sortiranja hot, new, top, old su sva unaprijed izračunata.
* Kada user upvotea, napravi se minimum odmah (u bazu se doda upvote), a ostatak procesiranja se stavi u queue (sortiranje linkova, prepoznavanje spama, računanje nagradi...). Nema potrebe da user čeka za išta od toga. Za queuing koriste `RabbitMQ`.
