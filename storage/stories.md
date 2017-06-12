# Stories

## Why You Should Never Use MongoDB

http://www.sarahmei.com/blog/2013/11/11/why-you-should-never-use-mongodb/

MongoDB je document-oriented database, gdje umjesto normaliziranih redova, entiteti su denormalizirani JSONi bez stroge strukture i sheme. Na primjeru TV serije sa Sezonama i Epizodama i Glumcima, gdje bi trebalo 4 joina da dohvatiš sve podatke koji su raspršeni posvuda, dok u Mongu sve podatke držiš u jednom dokumentu i dohvaćaš ih jednim queriem.

Međutim, ako želiš dohvatiti u kojim je sve Serijama bio neki Glumac - što bi bilo trivijalno da si u relacijskoj bazi - je sad nemoguće. Jer si podatke koji su očito relacijski ugurao u nerelacijsku strukturu. I 4 joina uopće nisu puno! Relacijske baze napravljene su da izdrže desetke joinova bez problema.

Mongo je zapravo cache store i dobar je samo za potpuno arbitrarne JSONe za koje ti nije bitno što se unutra nalazi. A i u tom slučaju bolje ti je koristiti Postgresov `hstore`.

## Reddit's database has only two tables

http://kev.inburke.com/kevin/reddits-database-has-two-tables/

Reddit je imao problem s normaliziranom bazom: dodavanje stupaca je bilo sporo, kompliciralo je backup i deployment.
Umjesto relacijske baze, oni za svaki entitet (Users, Links, Comments), imaju dvije tablice: `comment_things` (id, ime, timestamps), i `comment_data` (thing_id, key, value). Koriste relacijsku bazu kao *key-value* store.

Najveći problem s ovim je što se moraš sam brinuti za konzistentnost podataka, umjesto da koristiš mehanizme same baze usavršene za relacijski tip podataka. A problemi koje su naveli uopće nisu problemi: Postgres omogućuje gotovo besplatno dodavanje novih stupaca, a za MySql uvijek možeš dodati pomoćnu tablicu s novim stupcem, triggerima i UPDATEom prebaciti podatke u nju, i preimenovati je kad završiš.

## Postgres tips from Instagram (2013)

http://instagram-engineering.tumblr.com/post/10853187575/sharding-ids-at-instagram
http://instagram-engineering.tumblr.com/post/40781627982/handling-growth-with-postgres-5-tips-from

Problem: handlanje 10K likeova po sekundi.
1. **Sharding**, definiran na razini baze:
  * Svaki shard je jedna Postgres *schema* (ne sql schema, nego PG Schema kao kolekcija tablica). U nekoliko DB servera definirano je više tisuća shardova (schema). Na ovaj način mogu početi s malim brojem servera, a poslije prebacivati shardove na druge servere bez da shardaju podatke iznova.
  * Umjesto inkrementalnog ID-a, svaki shard generira svoj. Uvjeti su: da budu sortirani po vremenu nastanka (ne moraš koristiti dodatni stupac za sortiranje), da su 64-bitni integeri (manji indeksi plus Redis je optimiziran za integere)
  * ID se sastoji od: 41 bitova za timestamp u milisekundama, 13 bitova za shard ID, i 10 bitova za inkrementalni sequence tog sharda. To znači da mogu generirati 1024 ID-a po milisekundi po shardu.
2. **Partial indexes**:
  * Ako često filtriraš po nekoj karakteristici koja je u malom broju redova.
  * Npr. kad traže tagove, sortiraju po broju fotografija, pa su `CREATE INDEX CONCURRENTLY on tags WHERE media_count > 100`.
  * Query planner je dovoljno pametan da iskoristi taj indeks.
3. **Functional indexes**:
  * U nekim slučajevima žele indeksirati po stringovima koji su jako dugi, što bi dupliciralo puno podataka.
  * Umjesto toga, naprave `CREATE INDEX CONCURRENTLY on tokens (substr(token), 0, 8)`.
  * Performance je poboljšan, a sad index zauzima 10 puta manje mjesta.
4. **pg_reorg**:
  * Redovi ne dolaze u bazu istim redoslijedom kojim ih želimo queriati. Npr. likeove jednog usera često želimo dohvatiti odjednom, ali su raspršeni po cijelom disku.
  * `pg_reorg` duplicira postojeću tablicu, ali unosi redove `ORDER BY` indeksom, pa će povezane stvari brže dohvaćati.
5. **WAL-E**:
  * Arhivira sve Write-Ahead Logove na S3. Uz regularni backup i WAL-E, možeš restorati bazu na bilo koji željeni trenutak.

## Making MySQL Better at GitHub (2013)

https://github.com/blog/1880-making-mysql-better-at-github

Prebacivali su bazu u novi cluster s boljim hardwareom. Pritom su se pripremili na sljedeći način:
1. Pomoću `tcpdump` prikupili su `SELECT` querije s produkcije i replayali ih na novi cluster.
2. Isprobavali su različite konfiguracije, mijenjajući ih jednu po jednu: `innodb_buffer_pool_size`, `innodb_thread_concurrency`, `innodb_io_capacity`, `innodb_buffer_pool_instances`. Svaku su testirali bar po 12 sati.
3. Bilježili reponse time, zastoje u querijima, `SHOW ENGINE INNODB STATUS` i `SEMAPHORES` za concurrency.
4. Prebacili velike tablice s povijesnim podatcima na zaseban cluster. Time se oslobodio disk i buffer pool za "hot" podatke.

## Dropbox moves from Amazon (2016)

https://blogs.dropbox.com/tech/2016/03/magic-pocket-infrastructure/

Dosad su hostali fileove na S3, a metadatu kod sebe. Ali s preko 500 PB podataka, to je postalo preskupo.

Odlučili su razviti in-house rješenje, jedan od svega nekoliko exobyte-scale storage sustava na svijetu.

## Why Uber Engineering Switched from Postgres to MySQL (2016)

https://eng.uber.com/mysql-migration/

Uber je počeo kao monolitna Python aplikacija na Postgresu, a danas je milijun
mikroservisa na `Schemaless`, sharding layeru izgrađenom na Mysqlu. Razlozi zbog kojeg su odustali od Postgresa:
* *Write amplification*: svaki update rowa u pozadini stvara novi row, te preusmjerava sve indekse na njega (čak i ako se indeksirani stupci nisu promijenili). Drugim riječima, promjena samo nekoliko bytova uzrokuju gomilu operacija na fizičkom layeru.
* *Replication*: to se još više amplificira na replikama mastera, gdje se umjesto logičkih ("promijeni stupac u redu") šalju naredbe na razini diska ("stvori novi row, prebaci primary key na njega, prebaci indekse na njega"), što troši puno više bandwidtha.
* *Upgrade* je zeznut s replikama, ali postoji neki `pglogical` alat koji tome pomaže.

Još neke prednosti Mysqla:
* *Caching*: Postgres većinom prepušta cachiranje kernelovom page cacheu, koji je sporiji od Mysqlovog custom LRU cachea (InnoDB buffer pool).
* *Connection Handling*: Postgres pokreće proces po konekciji, što je znatno skuplje nego MySqlov thread po konekciji. Mysql puno bolje handla velik broj konekcija.
