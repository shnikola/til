# Production

## Scaling

nginx ispred puma servera, servira static assete i handla spore klijente. Session se drži u cookiju

Korisno je imati viewove neovisne o aplikaciji (user stanje da se dohvaća ajaxom) kako bi se mogao što veći dio viewa cacheirati.

Instaliraj 2+ memcached servera i koristi Dalli.

Simptomi problema skalabilnosti, za read: sporo učitavanje stranica, korisnici dobijaju 503, database visoki CPU i read IO. Za write: dabase write IO visok, aplikacija se "lock upa", replike kasne sa stanjem

Caching je brz i jeftin, koristi ga ako će jedan upit uštediti nekoliko database upita.

## Heroku

Dodaj heroku remote s `heroku git:remote -r heroku-staging -a app-name`.
Pushaj s `heroku git push heroku-staging staging:master`

Za više accountova koristi `heroku plugins:install heroku-accounts`. Dodaj račun s `heroku accounts:add personal`, odaberi ga s `heroku accounts:set`, ispiši postojeće s `heroku accounts`.

`heroku features:enable -a <app-name> preboot` će se pobrinuti da su novo deployani dynoi pokrenuti prije nego ugasi stare. Na taj način se osigurava zero-downtime deployment, ali se povećanja vrijeme deployanja na oko 3 minute. Pritom treba biti oprezan jer se može dogoditi da u istom trenutku budu pokrenuti dynoi sa starim i s novim kodom.

## Server Processes

Routing na razini Herokua je neučinkovit jer load balancer ne zna koliko je koji server zauzet. S druge strane, routing na razini socketa na serveru kojeg osluškuje više procesa je brz i učinkovit - request se nikada neće proslijediti zauzetom workeru. Zato preferiraj manji broj servera, a više workera po serveru. 2 servera s po 30 procesa su puno učinkovitiji od 30 servera s po 2 procesa.

Koristi minimalno 3 workera po serveru, ali idealno koliko god ti resursi dopuštaju. Procesi jedini omogućuju paralelno handlanje requestova.

## Server Threads

U MRI Rubyju threadovi omogućuju paralelizam IO operacija, što je oko 10-25% ukupnog vremena obrade requesta. Prema Amdahlovom zakonu, programi s tako malim dijelom paralelnog izvođenja nemaju koristi od povećanja broja threadova. U većini Rails aplikacija, više od 5 threadova po procesu nema nikakav dodatni učinak.

U JRubyju threadovi omogućuju potpuni paralelizam, pa ih dodaji dok god imaš resursa.

Zbog niza kompliciranih razloga, threadovi mogu trošiti i dosta više memorije nego što bi trebali. Pripazi na memoriju nakon što mijenjaš broj threadova na serveru.

## Server Capacity

Izmjeri koliko 1 proces s 5 threadova troši. Disablaj worker killere, pokreni server s nekoliko workera u produkciji i čekaj 12-24 sata bez restartanja (treba vremena da se zauzme sva memorija). Koristi `heroku ps:exec ps` da izmjeriš memoriju prosječnog workera. Pomoću toga, izračunaj koliko workera možeš imati na serveru, ali ostavi malo lufta: `TOTAL_RAM / (RAM_PER_PROCESS * 1.2)`.

Većina Rails aplikacija koriste 200-400MB po procesu, ali ima onih koje troše i do 1GB. S 3 procesa po serveru, većina Rails aplikacija treba dyno s minimalno 1GB RAMa.

Nikako nemoj dopustiti da se troši više memorije nego što je raspoloživo. Prekoračenje kapaciteta memorije pogubno djeluje na performanse jer se počinje koristiti swap memorija s diska.

CPU resursi su također važni. Pripazi da server ima dovoljno hyperthreadova (CPU coreova) za broj workera koje koristiš. Idealno, broj workera bi trebao biti `1.25-1.5x` broj hyperthreadova.

## ActiveRecord Connection Pool

https://devcenter.heroku.com/articles/concurrency-and-database-connections

Svaki database ima ograničenje koliko maksimalno konekcija može podržavati. Ako koristiš multi-threaded ili multi-process server, imaj na umu da svaki thread zahtjeva svoju database konekciju.

Kod multi-threaded servera, connection pool će se konfigurirati pri inicijalizaciji aplikacije koristeći property `pool` u `config/database.yml`. Kod Pume, svaki proces ima svoj pool, pa je dovoljno je da postaviš `pool: ENV['RAILS_MAX_THREADS']`, po konekciju za svaki thread.

Kod multi-process servera, master proces inicijalizira aplikaciju i tek onda forka workere. Nakon forkanja, svaki se proces mora iznova spojiti na bazu pri čemu će automatski izgraditi svoj connection pool koristeći `config/database.yml`. U `config/puma.rb` dodaj: `on_worker_boot { ActiveRecord::Base.establish_connection }`

## Rack::Deflate

Ako pokrećeš svoj vlastiti server iza remote proxija (npr. nginx, apache), podesi proxy da gzipa odgovore aplikacijskog servera (nginx gzipa HTML po defaultu).

Ako pokrećeš server na Heroku, remote proxy se ne koristi, pa je potrebno gzipati odgovore u samoj aplikaciji. U `application.rb` dodaj `config.middleware.insert_after ActionDispatch::Static, Rack::Deflater`. Neki asseti se pri kompilaciji zapišu na disk gzipani, pa nema potrebe da ih se još jednom gzipa on-the-fly.

Iznimka je passenger koji dolazi s distribucijom nginxa, pa gzipanje dolazi out-of-the-box.

## Rails u produkciji


**Deploy:** Heroku (ako želiš jeftinije tipa EC2 ili Digital Ocean, treba klijentima dati do znanja da s tim ne dobijaju 24/7 security updates)
**Server:** Passenger ili Puma (Unicorn ima problem sa sporim klijentima)
**Metrics:** New Relic, Papertrail
**Rails Dependencies:** Rails 12 Factor, Rack-Cache middleware, Rack-Attack, Rack-Protection, Sprockets 3.3+
**DB:** Postgresql, male Redis instance uz Sidekiq (workere podesi da koriste readonly Follower bazu)
**CDN:** Koristi ih! AWS Cloudfront ili Fastly.

# Literatura

* https://www.speedshop.co/2017/10/12/appserver.html

