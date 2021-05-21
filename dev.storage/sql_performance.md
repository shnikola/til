# SQL performance

## Indeksi

Indekse koristi za columne koji imaju dobro podijeljene vrijednosti. Ako je `users.active = 1` za 90% usera, engine za upit `WHERE active = 1` neće koristiti indeks jer će procijeniti da se ne isplati kad će većinu rezultata dobiti sekvencijalnim scanom.

U ovom slučaju puno bolje je napraviti **partial index**:
`CREATE INDEX i ON users(active) WHERE active = 0`
Takav indeks će biti manji i brži za scaniranje.

Osim filtriranja, indekse koristi i za columne po kojima sortiraš.

Engine ponekad zna biti glup; možeš ga prisiliti da koristi indeks s `FORCE INDEX index_name`.

## Tipovi indexa

`B-Tree` (balanced tree) je defaultni tip indeksa koji radi dobro za sve tipove podataka. `GIN` (inverted index) korisni kad indeksiraš više vrijednosti na jedan row (npr. array stupce ili full-text search). `GiST` (search tree) korisni za geometrijske tipove ili full-text search.

**Expression index** omogućava indeksiranje modificarnih podatka, ako često radiš querije koji koriste funkcije, npr. `CREATE INDEX i ON users(lower(name))` ako često radiš query s `WHERE lower(email)` ili `CREATE INDEX i ON articles(date(published_at))` ako pretavaraš `datetime` u `date`.

Stvaranje novog indeksa locka cijelu tablicu za pisanje. `CREATE INDEX CONCURRENTLY` radi sporije, ali bez lockanja.

Indeksi se s vremenom fragmentiraju zbog obrisanih i updateanih redova. Korisno je napraviti `REINDEX`, ali i on dugo traje i locka cijelu tablicu. Zato je bolje stvoriti isti indeks pod drugim imenom `CONCURRENTLY`, i onda obrisati stari.

Kada testiraš indekse, ne radi to na development mašini. Hoće li se indeks iskoristiti i kako ovisi o konfiguraciji db servera koji je vjerojatno drugačiji kod tebe.

## Mass updating

Update je spora operacija. Ako trebaš updatati redove po cijeloj tablici, dodaj where da selektiraš samo one koji se doista trebaju updatati. Umjesto
`UPDATE users SET email = lower(email)`
koristi
`UPDATE users SET email = lower(email) WHERE email != lower(email)`.

## Data import

Ako importaš puno podataka u praznu tablicu, dodaj constrainte, foreign keyeve i indexe tek nakon što si unio podatke.

Ako importaš veliki CSV file u bazu, overhead aplikacijskog frameworka može biti preskup. Umjesto toga, koristi direktno učitavanje fileova u tablicu:
* Postgres `COPY posts FROM 'tmp/new_posts.csv' CSV HEADER`
* MySQL `LOAD DATA INFILE '/tmp/posts.txt' INTO TABLE posts IGNORE 1 LINES`

## Connection pooling

Connection pool sadrži listu već uspostavljenih konekcija koje aplikacija može koristiti kako bi izbjegla overhead spajanja.
*Framework Pooling*: kada se aplikacija pokreće, sama stvara pool konekcija. Dio mnogih frameworka (Sequel gem, Rails).
*Persistent Connections*: aplikacija održava konekciju po requestu. Nedostatak je što si ograničen samo na jednu po requestu.

Ako imaš više od 100 konekcija otvorenih, treba ti nešto robusnije kao `PG Bouncer`, standalone pooler.

Ako baza potroši sve dostupne konekcije, bacit će `max_connect_errors`. Ako se neki klijent pokuša spojiti previše puta, bit će blokiran s errorom `host_name blocked`. Da bi se host cache očistio pozovi `FLUSH HOSTS`.

## Multiple Databases

**Horizonal sharding** je dijeljenje sadržaja tablica u više baza, kako bi bilo manje redova za selektiranje i indeksiranje. Za metodu shardinga može biti zadužena aplikacija (prednost: ne treba dodavati nove komponente) ili database router (prednost: ako treba mijenjati, na jednom je mjestu).

**Functional partitioning** je dijeljenje tablica u različite baze prema njihovoj upotrebi. Na taj način odvajanje tablice u koju se često piše neće degradirati performance ostalih tablica.

**Read replicas** mogu ići samostalno, ili u kombinaciji s gornjim podjelama. Replike smanjuju pritisak na writing bazu i obavljaju select upite.

## EXPLAIN

Za spore querije koristi `EXPLAIN` da vidiš detalje upita. Stupac `rows` (Mysql), odnosno `Plan Rows` (Postgres) govore kroz koliko će redova upit morati proći u najgorem slučaju. Ako je vrijednost `1`, upit će se dobro skalirati. Ako je vrijednost jednaka ukupnom broju redova u tablici, upit zahtjeva *full table scan* što nije skalabilno.

## Brojanje redova

`SELECT COUNT(*)` zahtjeva full page scan, pa ga izbjegavaj za velike tablice.
* cachiraj vrijednost, bilo kao stupac u tablici, ili u memoriji.
* ne prikazuj ukupan broj gdje nije potrebno (npr. kod paginacije).
* umjesto točnog broja napiši npr. "of Thousands".

Pri paginaciji s `OFFSET(1000)`, skenira se i svih 1000 prethodnih redova. Upit za zadnju stranicu će efektivno morati proći kroz cijelu tablicu. Usto, dodavanje novih redova tijekom paginacije će dovesti do prikaza istih redova na različitim stranicama.
* Izbaci direktna skakanja na N-tu stranicu, prikažu samo "Next" i "Prev".
* Koristi `WHERE id < 1000 ORDER BY id DESC` umjesto `OFFSET` kad možeš.
* U slučaju sortiranja po drugom stupcu, koristi u kombinaciji s `id`, npr. `WHERE created_at < ? AND id < 20 ORDER BY created_at DESC, id DESC`

Provjeri cache hit rate s:
`SELECT sum(heap_blks_read) as heap_read, sum(heap_blks_hit) as heap_hit, sum(heap_blks_hit) / (sum(heap_blks_hit) + sum(heap_blks_read)) as ratio FROM pg_statio_user_tables`. Ako je ratio manji od 99%, povećaj cache.

Korištenje indeksa provjeri s
`SELECT relname, 100 * idx_scan / (seq_scan + idx_scan) percent_usage, n_live_tup rows_in_table FROM pg_stat_user_tables WHERE seq_scan + idx_scan > 0 ORDER BY rows_in_table DESC`. Sve tablice s preko 10,000 rowova bi trebale imati indeks.

Za nalaženje sporih i često pozivanih querija:
`SELECT (total_time / 1000 / 60) as total_minutes, (total_time/calls) as average_time, query FROM pg_stat_statements ORDER BY total_minutes DESC LIMIT 100`
