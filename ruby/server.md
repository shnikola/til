# Servers

## Rack

`Rack` je univerzalni adapter između servera (`unicorn`, `puma`) i aplikacije (`rails`, `sinatra`).

1. Server parsira request.
2. Šalje ga racku kroz varijablu u `call(env)`
3. Rack vraća array `['200', {'Content-Type' => 'text/html'}, ['Response Body.']]` sa statusom, headerima i bodyjem.

### Rack Hijack

Rack 1.5 nema direktnu podršku za Streaming, Server-Sent Events i Websockete, ali dozvoljava "socket hijacking" koji prepušta developeru direktno pisanje u socket i ručno implementiranje spomenutih protokola.

Full Hijacking API predaje aplikaciji potpunu kontrolu socketa, a sam server ne šalje ništa. `env['rack.hijack'].call` započinje socket hijack, a za pisanje u socket koristi se IO objekt `env['rack.hijack_io']`. Aplikacija je zadužena za ispisivanje headera i zatvaranje socketa.

Partial Hijacking API obavlja slanje headera iz servera, a zatim predaje kontrolu socketa aplikaciji. `headers['rack.hijack'] = proc do |io|` definira ponašanje nakon slanja headera. Body dio responsa `[200, headers, nil]` se zanemaruje.

### Rack Events

https://github.com/rack/rack/blob/master/lib/rack/events.rb

Rack je malo šugav jer svaki middleware mora kopati po čitavom "rack stacku" da bi napravio nešto.

Ovo je prijedlog event-based pristupa gdje se middleware kojeg ne zanima response body zakači na evente poput `on_start`, `on_finish` ili `on_error`.

## Procfile

Rails aplikacija često zahtjeva pokretanje više različith procesa (web, workers, scheduleri). Za lakši management koristi se `Procfile`, koji je tekst file s linijama u formatu `<process type>: <command>`, npr. `web: bundle exec thin start -p $PORT`

Za pokretanje koristi `procodile` (prvi je bio `foreman`, ali ovaj ima više opcija). Koristi `procodile start --dev` lokalno i `procodile status` za popis pokrenutih procesa.

Procfile se koristi i za deployment na Heroku. Trenutno pokrenute procese možeš vidjeti s `heroku ps`.

Korisna opcija je `release: rubocop -Fl -fo --fail-level E` koja spriječava deployment ako aplikacija sadrži syntax error.

## Puma

https://github.com/puma/puma

Puma je multi-process, multi-threaded HTTP 1.1 server.

Svaki server ima jednog ili više *workera* (procesa). Procesi su međusobno izolirani pa se ne moraš brinuti je li tvoja aplikacija thread-safe. Svaki zauzima RAM-a kao zasebna aplikacija, pa pripazi da ih ne pokreneš previše. Za početak neka ih bude koliko i jezgri na stroju.

Svaki worker ima *thread pool* kojem prosljeđuje requeste za dodatan concurrency. Threadovi troše manje RAM-a, ali više CPU-a, te zahtjevaju pažljivije programiranje. Ako koristiš MRI, GIL neće dopustiti da se threadovi izvršavaju istovremeno, ali će aplikacije s puno blokirajućih IO operacija (db upita, externih requestova) i dalje imati korist od multi-threadinga.

Thread pool prima `min` i `max` setting, pa će Puma prilagoditi broj threadova trenutnom prometu. Povećavaj `max` dok god sto smanjuje latencije. U trenutku kada dodavanje dodatnih threadova ne popravlja latenciju, ne trebaš ih više dodavati.

JRuby ne podržava procese, pa smije imati samo jednog workera. Ali JRuby nema GIL, tako da će threadove izvršavati potpuno paralelno.

Pumin defaultni restart (*hot restart*) ostavlja sockete otvorenim, pa se requesti prilikom deploymenta ne gube, nego samo malo pričekaju. Druga opcija je *phased restart* gdje se workeri restartaju jedan po jedan, pa requesti ne osjete nikakav delay. U tom slučaju treba pripaziti jer server može istovremeno vrtiti dvije verzije koda. `Heroku` ima vlastiti phased restart dynoa, pa nam tamo ne treba.

`preload_app` učitava cijelu aplikaciju u master process iz kojeg se forkaju ostali workeri. To će ubrzati pokretanje workera i omogućiti korištenje `on_worker_boot` bloka. U njega stavi kod koji će se izvršiti kad se proces pokrene, ali prije nego počne prihvaćati requestove. Npr. reconnect na db, redis ili memcached.

Puma ne posjeduje mehanizam za *timeout*. `Heroku` router timeouta sve requeste duže od `30s`, ali ne može to dojaviti Puma workeru koji će nastavati trošiti resource na timeoutani request. Zato uvijek koristi `Rack::Timeout` gem postavljen na dovoljno dugi period, npr. `20s`.

*Spori klijenti* (oni koji sporo šalju i primaju podatke, npr. mobilni 3G korisnici) mogu stvoriti Denial of Service na serverima kada workeri moraju čekati da request završi. Zahvaljujući multi-threadingu, Puma može primiti više sporih klijenata bez da blokira workera.

## Unicorn

Unicorn je multi-process HTTP server. Master process forka workere kojima se prosljeđuju requesti.

Pošto svaki request zahtjeva slobodan proces, Unicorn radi ok s klijentima koji imaju male latencije i velik bandwith. Ali u slučaju da aplikacija očekuje velike requestove (npr. image upload) ili šalje velike odgovore sporim klijentima, svaki worker će biti zauzet dugo vremena. Kada su svi workeri zauzeti, raste vrijeme čekanja u redu svakog requesta. Zato se kao bolja opcija preporučuju Puma ili Rainbows.

## EventMachine

EventMachine omogućuje event-based handlanje IO naredbi u stilu nodeJSa. Event loop se izvodi u jednom threadu koristeći Linux `select()`, pa s istim resursima može handlati puno više konekcija od standardnog multithread servera koji zahtjeva po thread za svaku konekciju. Koristan u slučaju da imaš jako veliki throughput.

## message_bus

https://github.com/SamSaffron/message_bus

MessageBus omogućuje server-server i server-klijent asinkronu komunikaciju porukama. Koristi long polling sa streamingom kako bi poslao više poruka s istom konekcijom, a fallbacka na long polling i polling gdje server ne podržava streaming.

`MessageBus.publish("/channel", "message")` šalje poruku na kanal.
`MessageBus.publish("/channel", "hello", user_ids: [1,2,3]`) šalje poruku samo navedenim userima.
`MessageBus.subscribe("/channel") do |msg|` definira ponašanje kad poruka stigne na kanal. Handler se vrti u zasebnom threadu dediciranom za primanje poruka.

Komunikacija je replayable: u bilo kojem trenutku možeš zatražiti backlog poruka (perzistirane u Redisu ili Postgresu).

MessageBus radi unutar serverskog procesa, pa nije potrebno otvarati poseban port i server za slanje poruka.

## HTTP/2

HTTP/2 nije u potpunosti kompatibilan s Rackom, ali radi se na tome.

Ako koristiš proxy koji podržava HTTP/2 (nginx, h2o), klijenti će moći koristiti kompresiju headera.

Ako koristi CDN kompatibilan s HTTP2, klijenti će moći dohvaćati više asseta u istoj konekciji, pa domain sharding i bundling u spriteove više nisu potrebni.

