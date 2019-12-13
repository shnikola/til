# Data Storage

## Odabir baze

* Relational DB: MySQL, PostgreSQL, Oracle, SQL Server
* Document Stores: MongoDB, CouchDB, Amazon DynamoDB
* Key Value Stores: Redis, Memcached, Riak, Amazon DynamoDB
* Wide Column Stores: Cassandra, HBase, Accumulo, Hypertable
* Search Engines: ElasticSearch, Solr, Splunx, Sphinx
* Graph DB: Neo4j, Titan, OrientDB
* Time Series DB: RRDtool, InfluxDB, Graphite

Pri odabiru vrste spremišta, uzmi u obzir tip aplikacije:
* Online Transaction Processing: veliki broj korisnika, istovremeno izvršavaju puno malih transakcija. Trebaju biti brze što ostvaruju cachiranjem "live" podataka.
* Analytics: mali broj korisnika izvršavaju spore querije kroz cijele data setove

Za većinu aplikacija **RDB** isprva nude sve potrebne funkcionalnosti. Druge tehnologije počni uvoditi tek kada se javi potreba za skaliranjem  .

Event logovi (npr. praćenje korisničkih eventova) često počnu kao append only tablice u relacijskoj bazi. Ali ubrzo postanu problematične jer generiraju velik broj writeova (milijune dnevno) i opterećuju hardware. Ako ne postoji dobar razlog za držanje tih podataka u RDB-u, prebaci ih u specijalizirani storage, npr. AWS Redshift.

**Elasticsearch** je odličan za full text search, i skalira se za veliku količinu podataka (i do TB). Jedino je zeznut za konfigurirati.

**Redis** je dobar za spremanje privremenih podataka. Koristi ga kao cache koji nudi semantiku listi, hasha i seta. Ali nije transactional i moraš ga koristiti pod pretpostavkom da svi podatci mogu u bilo kojem trenutku nestati.

**RabbitMQ** je queue visokih performansi za komunikaciju point-to-point ili pub-sub.

## Remote Cache

Cache (npr. Memcached) radi na pretpostavci kako je brže dohvatiti podatak iz memorije nego obaviti skupu operaciju ili komunicirati s remote db-om koji drži podatke na disku. U tom slučaju je čak i network poziv, ako je istoj regiji (npr. na AWSu) brža opcija. Naravno, lokalni cache je najbrži, ali nije skalabilan.

## Data Analysis

Ako dobijaš 1TB podataka godišnje, i podatci su strukturirani, ne treba ti Hadoop/Hive/Presto. Savršeno dobro će poslužiti replika Postgres baze, BigQuery ili Redshift.

## The Log

*Log* je append-only lista. Novi unos ide na kraj liste, pa je sortirana po vremenu dodavanja. U njega zapisujemo evente (npr. klikove korisnika) ili modifikacije podataka (npr. `create` ili `update`).

Koristan je za integraciju različitih sustava za prikupljanje podataka. Svi podatci se drže u centralnom logu koji omogućava real-time subscription.

Izvori podataka mogu biti aplikacije koje trackaju eventove (npr. klikove), ili db tablice koje primaju updateove. Oni očiste i pripreme podatke prije nego ih pošalju u log.

Potrošači mogu biti cache sustavi, Hadoop, search sustavi itd. Svaki potrošač vodi svoj lokalni `time` dokad su pročitali log. Ako je potrebno, mogu zapisivati i stanje u lokalnoj memoriji ili tablici, a u slučaju pogreške, uvijek ga mogu iznova izgraditi iz loga.

Prednosti loga su:
* Potrošači ne moraju znati jesu li podatci došli iz RDBMS-a, key-value storea ili iz nekog real time sustava. Svi podatci dolaze jednakog formata, na jedno mjesto.
* Log se ponaša kao veliki buffer koji omogućava procesima da se restartaju ili failaju bez da uspore ostatak sustava.

Skaliranje i optimiziranje loga (ipak ne možemo čuvati svaki živi event zauvijek):
* Particioniranje recorda po nekom id-ju, batching čitanja i pisanja
* Za event data (tracking), čuvati samo window, stare evente brisati.
* Za keyed data (db), brisati recorde čije novije verzije imamo u logu.

## Blockchain

Blockchain je distribuirana baza koja se sastoji od blokova. Svaki blok je potpisan hashem i sadrži podatke o eventu (npr. novčanoj transakciji), timestamp, i hash prethodnog bloka.

Ukoliko se ijedan blok promijeni, morat će se promijeniti i njegov hash, a samim time i hashevi svih blokova koji slijede nakon njega. Block chainovi su distribuirani (nalaze se kod više klijenata), pa je dovoljno usporediti posljednje blokove da se provjeri je li neki od blokova tampered. Ispravan block chain odabire se decentraliziranim koncenzusom.

# Literatura

* https://engineering.linkedin.com/distributed-systems/log-what-every-software-engineer-should-know-about-real-time-datas-unifying
* https://anders.com/blockchain
