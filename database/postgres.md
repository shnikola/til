# Postgres

## times & dates
* koristi `timestamptz` za timestamp s vremenskom zonom.
* `created_at + '1 week'::interval` omogućuje jednostavno zbrajanje i oduzimanje od timestampova
* `date_trunc(<trunc_by>, created_at)` zaokružuje datum, npr. `date_trunc('hour', t)` zaokružuje na okruglo sati.


## UUID
Ugrađeni UUID-evi: `CREATE EXTENSION "uuid-ossp"`.
* dobiješ tip stupca `uuid`, koristi `DEFAULT uuid_generate_v4()`


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
* `SELECT array_agg(name) FROM students GROUP BY age` ispisuje sadržaj svake grupe.
* `SELECT unnest(grades)` pretvara array u redove.


## hstore
Moguće je definirati stupac tipa dictionary: `attrs hstore`. I key i value tipovi su stringovi.
* `CREATE EXTENSION hstore` za enable.
* `SET attrs = 'location => Croatia, length => 10'` za postavljanje vrijednosti.
* `SET attrs = attrs || 'age => 15'` za dodavanje elemenata.
* `attrs->'location'` za pristup određenom keyu.
* `WHERE attrs ? 'location'` za stupce koji imaju postavljen key.
* `WHERE attrs @> 'location => Croatia` za stupce koji sadrže subdictionary u sebi.


## Range
Moguće je definirati stupac tip range (kao u Rubiju): `int4range` (intovi), `numrange` (brojevi), `tsrange` (timestamp)
* `numrange(3, 10) @> 4` provjera da li sadrži element. `&&` imaju li 2 rangea ikakve zajedničke točke.
* `*` presjek, `+` unija


## Indeksi (TODO)
* `CREATE INDEX CONCURRENTLY` kreira index bez da locka cijelu tablicu, kako se inače radi.
* `CREATE INDEX idx ON people WHERE ` kreira parcijalni index s nekim uvjetom.
* `CREATE INDEX idx ON people (lower(name))` funkcionalni indeks, ako često radiš search s



## Text Search
* `LIKE 'a%'` za jednostavni matching.
* `~ 'a.*'` za regexe. `~*` za case insensitive.
* `regexp_split_to_table(text, '\s+')` split string po regexu u redove.
* `SET name = regexp_replace(name, '\s+', ' ')` za regex replacement.

Postgres ima i prilično dobar fulltext search.
* `CREATE INDEX tsv_idx ON documents USING gin(to_tsvector('english', text))` dovoljno je da dodaš funkcionalni index.
* `setweight(to_tsvector('english', title), 'A') || setweight(to_tsvector('english', text), 'D')` za search više stupaca.


## Series
* `generate_series(1, 1000, 10)` generira redove od `1` do `1000`, po `10`.
* `generate_series('2016-03-01 00:00'::timestamp, now(), '10 hours')` generira datume.


## WITH
Stvara privremenu tablicu za trenutni query. Puno jasnije za korištenje nego gnježđenje SELECTova.
Možeš ih i chainati, jednu WITH tablicu koristiti u drugoj, npr:
```sql
WITH regional_sales AS (SELECT region, SUM(amount) AS total_sales FROM orders GROUP BY region),
     top_regions AS (SELECT region FROM regional_sales WHERE total_sales > (SELECT SUM(total_sales)/10 FROM regional_sales) )
SELECT region, product FROM orders WHERE region IN (SELECT region FROM top_regions)
```

## dblink
Za izvršavanje querija na remote bazama, možeš koristiti `dblink_connect('postgres://...')` i `dblink('SELECT ...')`.


## Why Use Postgres
http://www.craigkerstiens.com/2012/04/30/why-postgres/
* `hstore` i `PLV8` za NOSQL fleksibilnost
* `ADD COLUMN` se izvrši instantno, za razliku od drugih DBova gdje traje jako dugo. Jedino će trajati ako imaš default value.
* `DDL Transactions` omogućuju da mijenjaš tablicu i columne s mogućnošću rollbacka ako se nešto sjebe.
* `Foreign Data Wrappers` omogućuju da wrapaš vanjski sistem (MYSQL, Redis, Twitter) i joinaš ga kao da je lokalni.
* `Replication` može se izvršavati asinkrono, ili sinkrono na svaku transakciju.
