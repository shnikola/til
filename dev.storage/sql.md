# SQL

## Column types

`INT` podržava veličine do 2^32 (ako je `UNSIGNED`). Length (npr. `INT(11)`) određuje samo koliko će se nula prikazivati ako se ispisuje sa `ZEROFILL`.

Koristi  `NUMERICAL` ili `DECIMAL` (imaju isto ponašanje) umjesto `FLOAT` i `DOUBLE` kako bi izbjegao pogreške zbog zaokruživanja.

Ne koristi `ENUM` ili `CHECK` constraints koj idefiniraju moguće vrijednosti stupca. Dodavanje novih i izmjena starih vrijednosti postaje komplicirana. Drži moguće vrijednosti stupca u zasebnoj tablici ili aplikacijskom kodu.

Postgres podržava `UUID` type, s `DEFAULT uuid_generate_v4()`.

## Encoding

MySQLov `utf8` zapravo ne podržava cijeli Unicode, nego samo 3 od 4 byta. Za pravi UTF-8, koristi `utf8mb4`.

Neke minor verzije MySqla imaju problem s insertanjem većih JSON-a (> 5MB), što se može zaobići koristeći `SET CHARSET binary` prije i `SET CHARSET default` poslije inserta.

## Null

`NULL` predstavlja nepoznatu vrijednost, pa sve operacije u kombinaciji s `NULL` rezultiraju u `NULL` (npr. boolean `NULL OR TRUE` ili konkatenacija teksta `'tekst' || NULL`). Isto tako, ne može se koristiti u `WHERE` naredbama kao normalna vrijednost. Umjesto `WHERE name <> NULL` koristi `WHERE name IS NOT NULL`.

`NULL` nije false, i nemoj ga koristi kao false. Boolean stupcima (npr. `disabled`) uvijek stavi `NOT NULL` i defaultnu vrijednost.

## Upsert

Postgres: `INSERT INTO users (id, name) VALUES (1, 'Nikola') ON CONFLICT DO UPDATE SET name=excluded.name`

Mysql: `INSERT INTO users (id, name) VALUES (1, 'Nikola') ON DUPLICATE KEY UPDATE name=name`

## Indeksi

Indeksi su korisni samo za querije koji dohvaćaju mali broj rezultata; ili za izbjegavanje sortiranja. Engine neće koristiti indeks ako procjeni da je sekvencijalni scan brži (npr. ako ima previše rezultata). Ako je engine glup, prisili ga da koristi indeks s `FORCE INDEX index_name`.

Kada testiraš indekse, ne radi to na development mašini. Hoće li se indeks iskoristiti i kako ovisi o konfiguraciji db servera koji je vjerojatno drugačiji kod tebe.

`B-Tree` (balanced tree) je defaultni tip indeksa koji radi dobro za sve tipove podataka. `GIN` (inverted index) korisni kad indeksiraš više vrijednosti na jedan row (npr. array stupce ili full-text search). `GiST` (search tree) korisni za geometrijske tipove ili full-text search.

**Partial index** omogućava indeksiranje samo nekih redova u tablici, pa je indeks manji i brži za scaniranje: `CREATE INDEX i ON articles(created_at) WHERE flagged IS TRUE`

**Expression index** omogućava indeksiranje modificarnih podatka, ako često radiš querije koji koriste funkcije, npr. `CREATE INDEX i ON users(lower(name))` ako često radiš query s `WHERE lower(email)` ili `CREATE INDEX i ON articles(date(published_at))` ako pretavaraš `datetime` u `date`.

Stvaranje novog indeksa locka cijelu tablicu za pisanje. `CREATE INDEX CONCURRENTLY` radi sporije, ali bez lockanja.

Indeksi se s vremenom fragmentiraju zbog obrisanih i updateanih redova. Korisno je napraviti `REINDEX`, ali i on dugo traje i locka cijelu tablicu. Zato je bolje stvoriti isti indeks pod drugim imenom `CONCURRENTLY`, i onda obrisati stari.

## Foreign Keys

Foreign keys osiguravaju integritet podataka, što ne bi trebala biti odgovornost aplikacije, već baze. Umjesto da dodaješ `on_delete` opcije framework modelu, koristi foreign key s `ON DELETE CASCADE` constraintom koji omogućuje atomarno brisanje svih vezanih redova.

Foreign keyevi dodaju minimalan performance overhead, ali uklanjaju potrebu za kompleksnom logikom unutar aplikacije, pa najčešće popravljaju performance.

## Multiple Values

Ako trebaš držati više vrijednosti u jednom fieldu, nikako nemoj koristiti string s listom elemenata odijeljenih zarezima. Quering postane jako kompliciran i ograničen si veličnim stringa.

Druga stvar koju treba izbjegavati je stvaranje dodatnih stupaca za držanje vrijednost (`tag1`, `tag2`, `tag3`). Opet, pretraživanje, dodavanje i brisanje vrijednosti postane presloženo.

Umjesto toga, koristi zasebnu normaliziranu tablicu.

## Migrations

`DDL Transaction` je zgodna stvar koju Postgres podržava da možeš rollbackati promjenu ako se dogodi greška pri mijenjanju tablice (npr. u Rails migracijama). MySql to nema.

Mijenjanje strukture tablice u produkciji je nezgodno, pogotovo ako tablica ima već hrpu redova. `ALTER TABLE` će po defaultu lockati tablicu za pisanje. Da bi to izbjegao koristi `ALGORITHM=INSTANT` (Mysql 8.0) ili Postgres u kojem to radi out-of-the-box.

Ako trebaš migrirati tablicu od sto milijuna redova koja se kontinuirano koristi u produkciji, najjednostavnije je stvoriti novu tablicu i prebaciti podatke u nju, koristeći **dual writing pattern**:

1. Dual writing. Stvori novu tablicu, i sve promjene na staroj primjeni i na nju. Umeđuvremenu, prebacuj stare podatke iz stare tablice u background jobu.
2. Prebaci read pathove da koriste novu tablicu. Testiraj da li se sve dobro čita.
3. Prebaci write pathove da koriste novu tablicu. Podesi dual writing da se sve promjene na novoj primjenjuju i na staroj za slučaj da moraš napraviti rollback.
4. Izbriši staru tablicu.

## Tree Models

Više je načina za implementiranje stablaste hijerarhije (npr. comment threada) u bazi. Nijedan nije idealan, pa se treba prilagoditi zahtjevima use-casea.

`parent_id` stupac omogućava lako dodavanje novih nodeova i dohvaćanje neposredne djece, ali dohvaćanje cijelog podstabla zahtjeva rekurzivne upite. Moguće je učitati sve nodeove (`WHERE post_id = ?`) u memoriju aplikacije i tamo izgraditi stablo, ali to može biti memorijski skupo.

`path` stupac (`1/3/12`) sadrži string sa svim ancestorima nodea. Svi potomci nodea se dohvaćaju s `c.path LIKE '1/3/12/%'`. Jedino je šugavo držati takve podatke u stringu.

Closure table koristi dodatnu tablicu s `ancestor` i `decendant` stupcima u kojima su zapisani svi međusobni odnosi nodeova. Dohvaćanje podstabla je jednostavno, a dozvoljava i da se node nalazi u više stabala. Jedino se za svaki node mora dodavati više redova u tablicu, što može biti memorijski zahtjevno za velika stabla.

## Inheritance

Ako u aplikaciji imamo baznu klasu (`Post`) i više podklasa (`UserPost`, `AdminPost`) koje želimo spremati u bazu, postoji nekoliko načina da oblikujemo njihove tablice.

**Single Page Inheritance** drži sve subklase u jednoj tablici (`posts`). Specifični atributi podklasa imaju svoje stupce, a u slučaju da se ne koriste vrijednosti su `NULL`. Potreban je i dodatni stupac u kojem će držati ime podklase. Nedostatak je što dodavanjem novih atributa podklasa tablica postaje sparse. Također, iz same baze nije očigledno koji atributi su zajednički, a koji specifični. Koristi kad imaš mali broj podklasa s malo specifičnih atributa.

**Concrete Table Inheritance** drži svaku subklasu u svojoj tablici (`user_posts`, `admin_posts`). Prednost je što ne miješaš specifične atribute i baza će prijaviti grešku pokušaš li koristiti atribut kojeg nema u podklasi. Ne zahtjeva dodatni stupac za tip. Nedostatak je teže dohvaćanje zajedničkih atributa za sve podklase. Također, ako se dodaje novi zajednički atribut, moraju se mijenjati sve tablice podklasa. Koristi ako ne moraš selektirati više podklasa odjednom.

U ovom slučaju `comments` tablica mora uz asocijaciju `post_id` mora imati i dodatni stupac `post_type`. Polimorfne asocijacije izbjegavaj jer ne dopuštaju foreign keyeve, a dohvaćanje asociranog retka zahtjeva složeni `JOIN`. Umjesto toga, za svaku podklasu asocijacije napravi eksplicitnu join tablicu (`user_post_comments`, `admin_post_comments`).

**Class Table Inheritance** koristi jednu tablicu za zajedničke atribute (`posts`) i po jednu za specifične atribute svake podklase (`user_posts`, `admin_posts`). Koristi kada želiš selektirati zajedničke atribute podklasa.

Oblik tablice koji svakako izbjegavaj je **Entity-Attribute-Value**: generička tablica s 3 stupca koja omogućava da dodaješ arbitrarne atribute na neku tablicu (npr. `12, 'featured_by', 'Nikola'`). Sve operacije tada postaju mnogo složenije i moraju se obavljati na aplikacijskoj razini (ne može biti `NOT NULL`, podržava samo jedan tip podataka, ne možeš koristiti foreign keyeve). Ako moraš spremati nestrukturirane podatke, koristi key-value store, a ne relacijsku bazu.

## Geometrija

Postgres podržava hrpu operacija za geometrijske podatke, tako da ti PostGIS ne treba.

Ako trebaš samo točku, dodaj stupce `longitude` i `latitude` tipa `NUMERIC(8, 5)`. Ako trebaš linije i poligone, koristi `Geometry` tip stupca.

Za provjeru sadrži li shape nešto koristi operator `@>`.
Za računanje udaljenosti `earth_distance` funkciju iz `earthdistance` modula (konvertiraj koordinate s `ll_to_earth(lat, long)`.

PostGIS koristi za importanje `WKT/WKB` formata, za kompleksne 3D geometriji gdje je razlika između sfere i sferoida bitna.

## Fulltext Search

Postgres podržava fulltext search, koristeći `tsvector` data type. To je array normaliziranih riječi koji izbacuje stop riječi (npr. `for`, `a`).

`to_tsvector('english', 'looking for the right words')` pretvara text u `ts_vector`. Više vektora možeš kombinirati s `||`: `to_tsvector('english', title) || to_tsvector('english', description)`.

`to_tsquery('english', 'look')` pretvara tekst u search query koji se može primjeniti operatorom `@@`: `WHERE to_tsvector(...) @@ to_tsquery(...)`.

`ts_rank(to_tsvector(...), to_tsquery(...))` vraća broj od 0 do 1, koristi za `ORDER BY rank`.

## Grouping Ambiguity

`SELECT MAX(score), id FROM posts GROUP BY type` će vratiti najveći `score` za svaku vrstu posta, ali neće nužno vratiti i `id` posta s najvećim scoreom.

Da bi `GROUP BY` vratio pouzdan rezultat, mora se poštovati *Single Value Rule*: svaka stavka `SELECT` querija mora ima samo jednu vrijednost unutar grupe. `MAX(score)` vraća samo jednu vrijednost za cijelu grupu. `type` je unutar `GROUP BY` poziva pa bi imao samo jednu vrijednost po grupi. Svi ostali stupci, poput `id`, vraćaju više različitih vrijednosti, pa ih se ne smije dohvaćati pomoću `SELECT`.

Dohvaćanje stupaca koji nisu u `GROUP BY` ili agregirani u većini baza će izazvati error. Iznimka su `MySQL` i `SQLite` koji će vratiti nasumičnu vrijednost.

## Random

`SELECT * FROM posts ORDER BY RAND() LIMIT 1` nasumično dohvaća red iz tablice. Ali za veće tablice, ovo je vrlo neoptimalna metoda - sortiranje po neindeksiranom stupcu je skupo (a `RAND()` se očito ne može indeksirati) i zahtjeva stvaranje privremene tablice, samo da bi se se skoro cijeli posao odbacio i uzeo tek jedan redak.

Ako tablica nije prevelika, moguće je dohvatiti sve id-jeve u aplikaciju s `Post.pluck(:id)` i tamo jedan odabrati nasumično.

`SELECT * FROM POSTS WHERE id >= :rand_id ORDER BY id LIMIT 1`, gdje je `rand_id = rand() * MAX(id)`. Zahtjeva da `id` budu slijedni integeri. Nije nužno uniformna razdioba, ovisno o gapovima među idevima.

`SELECT * FROM POSTS OFFSET :rand_offset ORDER BY id`, gdje je `randoffset = rand() * COUNT(*)`. Korisno ako se ne možeš pouzdati u slijednost `id`-a, a moraš imati uniformnu razdiobu. Nedostatak je što `OFFSET` i `COUNT(*)` moraju slijedno prolaziti kroz cijelu tablicu.

Neke baze imaju ugrađene metode za sampling tablice. Postgres ima `SELECT * FROM posts TABLESAMPLE SYSTEM (5)` koji vraća približno 5% redova iz tablice.

## Backup and restore

Dump: `pg_dump production_db_name -h db-host.rds.amazonaws.com --verbose > dump.sql`
Restore `psql development_dbn_ame < dump.sql`

## Data Import

Ako trebaš importati veliki CSV file u bazu, overhead aplikacijskog frameworka može biti preskup. Umjesto toga, koristi direktno učitavanje fileova u tablicu:
* Postgres `COPY posts FROM 'tmp/new_posts.csv' CSV HEADER`
* MySQL `LOAD DATA INFILE '/tmp/posts.txt' INTO TABLE posts IGNORE 1 LINES`

## Locking

**Optimistic locking** je strategija u kojoj svaki red ima broj verzije. Kada ga želiš promijeniti, zapišeš trenutnu verziju, i prilikom zapisivanja provjeriš da se verzija nije promijenila, npr. s `UPDATE ... WHERE id = 5 AND version = 23`. Ako update ne uspije, odustaje se od transakcije i daje korisniku da ponovno napravi izmjene. Koristi se kada je preskupo držati lock ili kad očekuješ da će se konflikt rijetko događati.

**Pessimistic locking** je strategija u kojoj ekskluzivno zaključaš redak dok ne napraviš izmjene na njemu. Ima veći integritet od optimističnog lockanja, ali moraš biti pažljiv da ne upadneš u deadlock.

Ako želiš redak zaključati samo za svoje čitanje (da nitko drugi ne može pisati dotad) koristi `SELECT ... LOCK IN SHARE MODE`
Ako želiš redak zaključati za svoje pisanje (nitko drugi ne može čitati ni pisati dotad) koristi `SELECT ... FOR UPDATE`

Kada drugi query želi napraviti SELECT koji uključuje taj redak, bit će blokiran dok se prvi query ne izvrši. Ako ne želi biti blokiran, može napraviti query s `FOR UPDATE NOWAIT` (izbacit će error) ili `FOR UPDATE SKIP LOCKED` (preskočit će lockane redove).

## Transaction Isolation

Izolacija transakcije je tradeoff između peformansa (može li se query izvršavati paralelno s ostalima) i pouzdanosti (možemo li dobiti zastario ili neispravan rezultat). Postavlja se s `SET TRANSACTION ISOLATION LEVEL <level>` za session ili globalno.

`READ UNCOMMITTED` query neće čekati da se druge transakcije dovrše, već će vratiti rezultate koji će se možda rollbackati. To je kad želimo nešto jako brzo i nije nas jako briga je li precizno.

`READ COMITTED` dopušta da dva `SELECT`-a unutar iste transakcije vrate različiti rezultat ako se row umeđuvremenu updateao

`REPEATABLE READ` garantira da će dva `SELECT`-a unutar iste transakcije vratiti jednake redove (ali možda dođu neki novi ako su se insertali umeđuvremenu).

`SERIALIZABLE` garantira da će dva `SELECT`-a unutar iste transakcije vratiti identičan rezultat. Sve ostale transakcije (pisanje, brisanje) su blokirane dok se transakcija ne dovrši.

## Job queue

Najčešće umjesto zasebnog pub/sub servera poput Kafke možeš koristiti najobičniju SQL bazu. Postgres npr. može bez problema handlati 10,000 inserta po sekundi.

Zahtjevi job queua u SQL-u su:
* Worker dohvaća prvi slobodan job i zauzima ga.
* Nijedan drugi worker ne smije uzeti job dok je zauzet.
* Ako worker naiđe na exception, job se mora automatski osloboditi.

Definiraš trigger nad dodavanjem novih jobova u tablicu:
```
CREATE TRIGGER jobs_status AFTER INSERT OR UPDATE OF status ON jobs
FOR EACH ROW EXECUTE PROCEDURE jobs_status_notify();
```

Šalje se notifikacijama svim workerima (i drugim klijentima) koji slušaju na kanalu s `PERFORM pg_notify('jobs_status_channel', NEW.id::text)`.

Svaki worker sluša s `LISTEN jobs_status_channel` i dohvaća job ovako:
```
UPDATE jobs SET status='initializing' WHERE id = (
    SELECT id FROM jobs WHERE status='new'
    ORDER BY id FOR UPDATE SKIP LOCKED LIMIT 1
)
```

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

Provjeri cache hit rate s:
`SELECT sum(heap_blks_read) as heap_read, sum(heap_blks_hit) as heap_hit, sum(heap_blks_hit) / (sum(heap_blks_hit) + sum(heap_blks_read)) as ratio FROM pg_statio_user_tables`. Ako je ratio manji od 99%, povećaj cache.

Korištenje indeksa provjeri s
`SELECT relname, 100 * idx_scan / (seq_scan + idx_scan) percent_usage, n_live_tup rows_in_table FROM pg_stat_user_tables WHERE seq_scan + idx_scan > 0 ORDER BY rows_in_table DESC`. Sve tablice s preko 10,000 rowova bi trebale imati indeks.

Za nalaženje sporih i često pozivanih querija:
`SELECT (total_time / 1000 / 60) as total_minutes, (total_time/calls) as average_time, query FROM pg_stat_statements ORDER BY total_minutes DESC LIMIT 100`

# Literatura

* SQL Antipatterns by Bill Karwin
* http://use-the-index-luke.com - sve o indeksima
* https://layerci.com/blog/postgres-is-the-answer
* https://www.slideshare.net/kigster/from-obvious-to-ingenius-incrementally-scaling-web-apps-on-postgresql
