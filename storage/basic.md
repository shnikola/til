# Database

TODO: paginacija http://www.xarg.org/2011/10/optimized-pagination-using-mysql/
Memcached

## The Log

https://engineering.linkedin.com/distributed-systems/log-what-every-software-engineer-should-know-about-real-time-datas-unifying

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

## Common performance problems

Za spore querije koristi `EXPLAIN` da vidiš detalje upita. Stupac `rows` (Mysql), odnosno `Plan Rows` (Postgres) govore kroz koliko će redova upit morati proći u najgorem slučaju. Ako je vrijednost `1`, upit će se dobro skalirati. Ako je vrijednost jednaka ukupnom broju redova u tablici, upit zahtjeva *full table scan* što nije skalabilno.

`SELECT COUNT(*)` zahtjeva full page scan, pa ga izbjegavaj za velike tablice.
* cachiraj vrijednost, bilo kao stupac u tablici, ili u memoriji.
* ne prikazuj ukupan broj gdje nije potrebno (npr. kod paginacije).
* umjesto točnog broja napiši npr. "of Thousands".

Pri paginaciji s `OFFSET(1000)`, skenira se i svih 1000 prethodnih redova. Upit za zadnju stranicu će efektivno morati proći kroz cijelu tablicu. Usto, dodavanje novih redova tijekom paginacije će dovesti do prikaza istih redova na različitim stranicama.
* Izbaci direktna skakanja na N-tu stranicu, prikažu samo "Next" i "Prev".
* Koristi `WHERE id < 1000 ORDER BY id DESC` umjesto `OFFSET` kad možeš.
* U slučaju sortiranja po drugom stupcu, koristi u kombinaciji s `id`, npr. `WHERE created_at < ? AND id < 20 ORDER BY created_at DESC, id DESC`

## Indexes

Indeksi su korisni samo ako dohvaćaš mali broj rezultata; ili za izbjegavanje sortiranja.

Engine automatski neće koristiti indeks ako procjeni da je sekvencijalni scan brži (npr. ako ima previše rezultata)

Unique indeksi su uvijek potrebni, jer `validates_uniqueness_of` ne štiti od concurrent requestova.

Kada testiraš indekse, ne radi to na development mašini. Hoće li se indeks iskoristiti i kako ovisi o konfiguraciji db servera koji je vjerojatno drugačiji kod tebe.

Pronalaženje nekorištenih indeksa.

## Migrations in production

https://stripe.com/blog/online-migrations

Ako trebaš migrirati tablicu od sto milijuna redova koja se kontinuirano koristi u produkciji, puno je brže stvoriti novu tablicu i prebaciti podatke u nju. Ta to se koristi *dual writing pattern*:
1. Dual writing. Stvori novu tablicu, i sve promjene na staroj primjeni i na nju. Umeđuvremenu, prebacuj stare podatke iz stare tablice u background jobu.
2. Prebaci read pathove da koriste novu tablicu. Testiraj da li se sve dobro čita.
3. Prebaci write pathove da koriste novu tablicu. Podesi dual writing da se sve promjene na novoj primjenjuju i na staroj za slučaj da moraš napraviti rollback.
4. Izbriši staru tablicu.

## Remote Cache

Cache (npr. Memcached) radi na pretpostavci kako je brže dohvatiti podatak iz memorije nego obaviti skupu operaciju ili komunicirati s remote db-om koji drži podatke na disku. U tom slučaju je čak i network poziv, ako je istoj regiji (npr. na AWSu) brža opcija. Naravno, lokalni cache je najbrži, ali nije skalabilan.

## Data Analysis

Ako dobijaš 1TB podataka godišnje, i podatci su strukturirani, ne treba ti Hadoop/Hive/Presto. Savršeno dobro će poslužiti replika Postgres baze, BigQuery ili Redshift.

# Literatura

* https://sqlbolt.com/ - interaktivni tutorial, super za učenje
* http://use-the-index-luke.com - sve o indeksima
