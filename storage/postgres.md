# Postgres

## times & dates

Koristi `timestamptz` za timestamp s vremenskom zonom.

`created_at + '1 week'::interval` omogućuje jednostavno zbrajanje i oduzimanje od timestampova

`date_trunc(<trunc_by>, created_at)` zaokružuje datum, npr. `date_trunc('hour', t)` zaokružuje na okruglo sati.

## UUID

Ugrađeni UUID-evi: `CREATE EXTENSION "uuid-ossp"`.
Dobiješ tip stupca `uuid`, koristi `DEFAULT uuid_generate_v4()`

## Array

Moguće je definirati stupac tipa array: `grades integer[]`.
* `SET grades = {5, 1, 5, 3}` ili `ARRAY[5, 1, 5, 3]` za postavljanje vrijednosti.
* `SET array = array || 1` dodavanje elementa.
* `grades[1]` za pristup određenom elementu. Indeksi počinju od `1`.
* `grades[1:2]` za dohvaćanje sliceova (od 1. do 2. elementa).
* `WHERE 5 = ANY (grades)` za arraye kojima je bar jedan element `5`
* `WHERE 3 < ALL (grades)` za arraye kojima su svi veći od `3`
* `WHERE grades @> {1, 2}` za arraye koji sadrže `1` i `2`.
* `WHERE grades && {1, 2}` za arraye koji sadrže `1` ili `2`.

Pomoćne funkcije:
* `SELECT array_agg(name, ',') FROM students GROUP BY age` ispisuje sadržaj svake grupe.
* `SELECT unnest(grades)` pretvara array u redove.

## hstore

Moguće je definirati stupac tipa dictionary: `attrs hstore`. I key i value tipovi su stringovi.
* `CREATE EXTENSION hstore` za enable.
* `SET attrs = 'location => Croatia, length => 10'` za postavljanje vrijednosti.
* `SET attrs = attrs || 'age => 15'` za dodavanje elemenata.
* `attrs->'location'` za pristup određenom keyu.
* `WHERE attrs ? 'location'` za stupce koji imaju postavljen key.
* `WHERE attrs @> 'location => Croatia` za stupce koji sadrže subdictionary u sebi.

## JSON

todo
`PLV8` je jezik kojim možeš raditi s JSONom.

## Range

Moguće je definirati stupac tip range (kao u Rubiju): `int4range` (intovi), `numrange` (brojevi), `tsrange` (timestamp)
* `numrange(3, 10) @> 4` provjera da li sadrži element. `&&` imaju li 2 rangea ikakve zajedničke točke.
* `*` presjek, `+` unija

## Series

* `generate_series(1, 1000, 10)` generira redove od `1` do `1000`, po `10`.
* `generate_series('2016-03-01 00:00'::timestamp, now(), '10 hours')` generira datume.

## Indeksi

* `B-Tree` (balanced tree) defaultni, dobri za jednakosti i range querije. Rade dobro sa svim tipovima.
* `GIN` (inverted index) korisni kad indeksiraš više vrijednosti na jedan row. Za `array` stupce ili full-text search.
* `GiST` (search tree) korisni za operacije različite od jednakosti i rangea. Za geometrijske tipove ili full-text search.

*Parcijalni Indeks* omogućava indeksiranje samo dijela tablice, pa je indeks manji i brži za scaniranje.
* `CREATE INDEX articles_flagged_created_at_index ON articles(created_at) WHERE flagged IS TRUE`

*Expression Indeks* omogućava indeksiranje modifikacije podataka, ako često radiš querije koji koriste funkcije.
* `CREATE INDEX users_lower_email ON users(lower(name))` ako često radiš query s `WHERE lower(email)`
* `CREATE INDEX articles_day ON articles(date(published_at))` ako pretavaraš `datetime` u `date`.

Stvaranje novog indeksa locka cijelu tablicu za pisanje. `CREATE INDEX CONCURRENTLY` radi sporije, ali bez lockanja.
Indeksi se s vremenom fragmentiraju zbog obrisanih i updateanih redova. Korisno je napraviti `REINDEX`, ali i on dugo traje i locka cijelu tablicu. Zato je bolje stvoriti isti indeks pod drugim imenom `CONCURRENTLY`, i onda obrisati stari.

## Text Search

* `LIKE 'a%'` za jednostavni matching.
* `~ 'a.*'` za regexe. `~*` za case insensitive.
* `regexp_split_to_table(text, '\s+')` split string po regexu u redove.
* `SET name = regexp_replace(name, '\s+', ' ')` za regex replacement.

Postgres ima i prilično dobar full-text search.
* Dodaj funkcionalni index: `CREATE INDEX tsv_idx ON documents USING gin(to_tsvector('english', text))`
* Weighted search više stupaca: `setweight(to_tsvector('english', title), 'A') || setweight(to_tsvector('english', text), 'D')`

## WITH

Stvara privremenu tablicu za trenutni query. Puno jasnije za korištenje nego gnježđenje SELECTova.
Možeš ih i chainati, jednu WITH tablicu koristiti u drugoj, npr:
```sql
WITH regional_sales AS (SELECT region, SUM(amount) AS total_sales FROM orders GROUP BY region),
     top_regions AS (SELECT region FROM regional_sales WHERE total_sales > (SELECT SUM(total_sales)/10 FROM regional_sales) )
SELECT region, product FROM orders WHERE region IN (SELECT region FROM top_regions)
```

## Upsert

Upsert je CREATE_OR_UPDATE koji se izvodi unutar *jedne transakcije* na razini baze.
Naredba je `INSERT INTO users (id, name) VALUES (1, 'Nikola') ON CONFLICT DO UPDATE SET name=excluded.name`

## Schema Migrations

* `DDL Transaction` omogućuje da mijenjaš tablicu i stupce s mogućnošću rollbacka ako se dogodi error (MySQL to nema).
* Ako dodaješ novi stupac, tablica se mora lockati za pisanje. `ADD COLUMN` se izvrši *instantno* ako nema default value i constraintove. Zato ga prvo dodaj bez, a onda u background jobu ispuni s default vrijednostima.

## Performance

Cache hit rate:
* `SELECT sum(heap_blks_read) as heap_read, sum(heap_blks_hit) as heap_hit, sum(heap_blks_hit) / (sum(heap_blks_hit) + sum(heap_blks_read)) as ratio FROM pg_statio_user_tables`.
* Ako je manji od 99%, povećaj cache.

Index usage:
* `SELECT relname, 100 * idx_scan / (seq_scan + idx_scan) percent_usage, n_live_tup rows_in_table FROM pg_stat_user_tables WHERE seq_scan + idx_scan > 0 ORDER BY rows_in_table DESC`.
* Sve tablice s preko 10,000 rowova bi trebale imati indeks.

Queriji:
* `CREATE extension pg_stat_statements` zapisuje querije u normaliziranom obliku za bolju statistiku.
* `SELECT (total_time / 1000 / 60) as total_minutes, (total_time/calls) as average_time, query FROM pg_stat_statements ORDER BY total_minutes DESC LIMIT 100` za nalaženje sporih i često pozivanih querija.
* `EXPLAIN ANALYZE` na query za više informacija

## Connection pooling

Connection pool sadrži listu već uspostavljenih konekcija koje aplikacija može koristiti kako bi izbjegla overhead spajanja.
*Framework Pooling*: kada se aplikacija pokreće, sama stvara pool konekcija. Dio mnogih frameworka (Sequel gem, Rails).
*Persistent Connections*: aplikacija održava konekciju po requestu. Nedostatak je što si ograničen samo na jednu po requestu.

Ako imaš više od 100 konekcija otvorenih, treba ti nešto robusnije kao `PG Bouncer`, standalone pooler.

## Remote connections

* `dblink` za izvršavanje querija na remote bazama. Koristi `dblink_connect('postgres://...')` i `dblink('SELECT ...')`.
* `Foreign Data Wrappers` omogućuju da wrapaš vanjski sistem (MYSQL, Redis, Twitter) i querijaš ga kao lokalni.

## Replication

todo
https://www.postgresql.org/docs/current/static/high-availability.html

## psql

Shell varijable:
* `HISTFILESIZE=` i `HISTSIZE=` za neograničen history size.
U `.psqlrc` dodaj:
* `\x auto` prikazuje rezultat u najboljem formatu.
* `\timing on` ispisuje koliko je svaki quer trajao
* `\set HISTCONTROL ignoredups`
Korisne naredbe:
* `\d` ispisuje sve relatione u bazi. `\dt` samo za tablice.
* `\d <table>` ispisuje info o tablici.
* Postavi `EDITOR=atom` (ili koji već), pa koristi `\e` za pisanje querija u editoru.
