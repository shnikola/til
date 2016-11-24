# Servers

TODO: thin, unicorn

## Rack
`Rack` je univerzalni adapter između servera (`unicorn`, `puma`) i frameworka (`rails`, `sinatra`).
* Server parsira request.
* Šalje ga racku kroz varijablu u `call(env)`
* Rack vraća array `['200', {'Content-Type' => 'text/html'}, ['Response Body.']]` sa statusom, headerima i bodyjem.


## Procfile
Rails aplikacija često zahtjeva pokretanje više različith procesa (web, workers, scheduleri). Za lakši management koristi se `Procfile`, koji je tekst file s linijama u formatu `<process type>: <command>`, npr. `web: bundle exec thin start -p $PORT`

Za pokretanje koristi `procodile` (prvi je bio `foreman`, ali ovaj ima više opcija). Koristi `procodile start --dev` lokalno i `procodile status` za popis pokrenutih procesa.

Koristi ga i Heroku, a trenutno pokrenute procese možeš vidjeti s `heroku ps`.


## Puma
https://github.com/puma/puma
Puma je multi-process, multi-threaded HTTP 1.1 server.

Svaki server ima jednog ili više *workera* (procesa). Procesi su međusobno izolirani pa se ne moraš brinuti je li tvoja aplikacija thread-safe. Svaki zauzima RAM-a kao zasebna aplikacija, pa pripazi da ih ne pokreneš previše. Za početak neka ih bude koliko i coreova na stroju.

Svaki worker ima *thread pool* kojem prosljeđuje requeste za dodatan concurrency. Threadovi troše manje RAM-a, ali više CPU-a, te zahtjevaju pažljivije programiranje. Ako koristiš MRI, GIL neće dopustiti da se threadovi izvršavaju istovremeno, ali će aplikacije s puno blokirajućih IO operacija (db upita, externih requestova) i dalje imati korist od multi-threadinga.

Thread pool prima `min` i `max` setting, pa će Puma prilagoditi broj threadova trenutnom prometu. Povećavaj `max` dok god sto smanjuje latencije. U trenutku kada dodavanje dodatnih threadova ne popravlja latenciju, ne trebaš ih više dodavati.

JRuby ne podržava procese, pa smije imati samo jednog workera. Ali JRuby nema GIL, tako da će threadove izvršavati potpuno paralelno.

Pumin defaultni restart (*hot restart*) ostavlja sockete otvorenim, pa se requesti prilikom deploymenta ne gube, nego samo malo pričekaju. Druga opcija je *phased restart* gdje se workeri restartaju jedan po jedan, pa requesti ne osjete nikakav delay. U tom slučaju treba pripaziti jer server može istovremeno vrtiti dvije verzije koda. `Heroku` svoj vlastiti phased restart dynoa, pa nam tamo ne treba.

`preload_app` učitava cijelu aplikaciju u master process iz kojeg se forkaju ostali workeri. To će ubrzati pokretanje workera i omogućiti korištenje `on_worker_boot` bloka. U njega stavi kod koji će se izvršiti kad se proces pokrene, ali prije nego počne prihvaćati requestove. Npr. reconnect na db, redis ili memcached.

Puma ne posjeduje mehanizam za *timeout*. `Heroku` router timeouta sve requeste duže od `30s`, ali ne može to dojaviti Puma workeru koji će nastavati trošiti resource na timeoutani request. Zato uvijek koristi `Rack::Timeout` gem postavljen na dovoljno dugi period, npr. `20s`.

*Spori klijenti* (oni koji sporo šalju i primaju podatke, npr. mobilni 3G korisnici) mogu stvoriti Denial of Service na serverima kada workeri moraju čekati da request završi. Zahvaljujući multi-threadingu, Puma može primiti više sporih klijenata bez da blokira workera.


## Unicorn
Unicorn se kršio s:
`Operation not permitted @ rb_file_chown - /home/jugosluh/shared/log/unicorn.log`
U `unicorn.rb` user nije bio postavljen na "app" s kojim sam deployao iz mine (on je stvarao foldere i sve)


## Ruby i HTTP/2
https://www.nateberkopec.com/2016/01/07/what-http2-means-for-ruby-developers.html
HTTP/2 nije kompatibilan s Rackom: request/response streaming bi trebao biti default, komunikacija dvosmjerna (server ili klijent mogu gurnuti više stvari bez odgovora)
HTTP/2 je inače nešto kao websocketsi, ali za websocketse treba implementirati dosta stvari koje http nudi (redirection, REST, caching)
Prednosti:
- headeri se compressaju (uključujući cookije), uštedi se do 80% - **koristi HTTP/2 compatible proxy (nginx, h2o)**
- mogućnost slanja više responsea u istoj konekciji, domain sharding više nije potreban - **koristi HTTP/2 compatible CDNove**
- jedna konekcija znači jedan TLS handshake
- klijenti mogu izraziti preferenciju koji request da se prvi ispuni (npr. CSS i JS prije imagea)
- pošto je skidanje jednog velikog filea i puno malih sada isto, gubi se potreba za jednim velikim JS i CSS fileom i spriteovima


## Rack::Attack
https://github.com/kickstarter/rack-attack
Ograničava requeste koji dolaze na server, prekidajući ih u middlewareu i vraćajući `419 Too Many Requests`.
Podržava različite uvjete:
* `safelist` requestovi uvijek prolaze
* `blocklist` nikad ne prolaze
* `throttle` prolaze ograničen broj puta u nekom intervalu
* `track` propušta requeste i po potrebi ih logira.
