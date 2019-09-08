# SQL

## Foreign Keys

Foreign keys osiguravaju integritet podataka, što ne bi trebala biti odgovornost aplikacije, već baze. Umjesto da dodaješ `on_delete` opcije framework modelu, koristi foreign key s `ON DELETE CASCADE` constraintom koji omogućuje atomarno brisanje svih vezanih redova.

Foreign keyevi dodaju minimalan performance overhead, ali uklanjaju potrebu za kompleksnom logikom unutar aplikacije, pa najčešće popravljaju performance.

## Multiple Values

Ako trebaš držati više vrijednosti u jednom fieldu, nikako nemoj koristiti string s listom elemenata odijeljenih zarezima. Quering postane jako kompliciran i ograničen si veličnim stringa.

Druga stvar koju treba izbjegavati je stvaranje dodatnih stupaca za držanje vrijednost (`tag1`, `tag2`, `tag3`). Opet, pretraživanje, dodavanje i brisanje vrijednosti postane presloženo.

Umjesto toga, koristi zasebnu normaliziranu tablicu.

## Tree Models

Više je načina za implementiranje stablaste hijerarhije (npr. comment threada) u bazi. Nijedan nije idealan, pa se treba prilagoditi zahtjevima use-casea.

`parent_id` stupac omogućava lako dodavanje novih nodeova i dohvaćanje neposredne djece, ali dohvaćanje cijelog podstabla zahtjeva rekurzivne upite. Moguće je učitati sve nodeove (`WHERE post_id = ?`) u memoriju aplikacije i tamo izgraditi stablo, ali to može biti memorijski skupo.

`path` stupac (`1/3/12`) sadrži string sa svim ancestorima nodea. Svi potomci nodea se dohvaćaju s `c.path LIKE '1/3/12/%'`. Jedino je šugavo držati takve podatke u stringu.

Closure table koristi dodatnu tablicu s `ancestor` i `decendant` stupcima u kojima su zapisani svi međusobni odnosi nodeova. Dohvaćanje podstabla je jednostavno, a dozvoljava i da se node nalazi u više stabala. Jedino se za svaki node mora dodavati više redova u tablicu, što može biti memorijski zahtjevno za velika stabla.

## Inheritance

Ako u aplikaciji imamo baznu klasu (`Post`) i više podklasa (`UserPost`, `AdminPost`) koje želimo spremati u bazu, postoji nekoliko načina da oblikujemo njihove tablice.

*Single Page Inheritance* drži sve subklase u jednoj tablici (`posts`). Specifični atributi podklasa imaju svoje stupce, a u slučaju da se ne koriste vrijednosti su `NULL`. Potreban je i dodatni stupac u kojem će držati ime podklase. Nedostatak je što dodavanjem novih atributa podklasa tablica postaje sparse. Također, iz same baze nije očigledno koji atributi su zajednički, a koji specifični. Koristi kad imaš mali broj podklasa s malo specifičnih atributa.

*Concrete Table Inheritance* drži svaku subklasu u svojoj tablici (`user_posts`, `admin_posts`). Prednost je što ne miješaš specifične atribute i baza će prijaviti grešku pokušaš li koristiti atribut kojeg nema u podklasi. Ne zahtjeva dodatni stupac za tip. Nedostatak je teže dohvaćanje zajedničkih atributa za sve podklase. Također, ako se dodaje novi zajednički atribut, moraju se mijenjati sve tablice podklasa. Koristi ako ne moraš selektirati više podklasa odjednom.

*Class Table Inheritance* koristi jednu tablicu za zajedničke atribute (`posts`) i po jednu za specifične atribute svake podklase (`user_posts`, `admin_posts`). Koristi kada želiš selektirati zajedničke atribute podklasa.

Oblik tablice koji svakako izbjegavaj je *Entity-Attribute-Value*: generička tablica koja omogućava da dodaješ arbitrarne atribute na neku tablicu (npr. `12, 'featured_by', 'Nikola'`). Sve operacije tada postaju mnogo složenije i moraju se obavljati na aplikacijskoj razini (ne može biti `NOT NULL`, podržava samo jedan tip podataka, ne možeš koristiti foreign keyeve). Ako moraš spremati nestrukturirane podatke, koristi key-value store, a ne relacijsku bazu.

## Polymorphism

U slučajevima poput *Concrete Table Inheritance*, `comments` tablica mora sadržavati polimorfnu asocijaciju `post_id` uz dodatni stupac `post_type`. Polimorfne asocijacije u pravilu treba izbjegavati jer ne dopuštaju foreign keyeve, a dohvaćanje asociranog retka zahtjeva složeni `JOIN`. Umjesto toga, za svaku podklasu asocijacije napravi eksplicitnu join tablicu (`user_post_comments`, `admin_post_comments`).

## Column types

Koristi  `NUMERICAL` ili `DECIMAL` (imaju isto ponašanje) umjesto `FLOAT` i `DOUBLE` kako bi izbjegao pogreške zbog zaokruživanja.

Ne koristi `ENUM` ili `CHECK` constraints koj idefiniraju moguće vrijednosti stupca. Dodavanje novih i izmjena starih vrijednosti postaje komplicirana. Drži moguće vrijednosti stupca u zasebnoj tablici ili aplikacijskom kodu.

## Null

`NULL` predstavlja nepoznatu vrijednost, pa sve operacije u kombinaciji s `NULL` rezultiraju u `NULL` (npr. boolean `NULL OR TRUE` ili konkatenacija teksta `'tekst' || NULL`). Isto tako, ne može se koristiti u `WHERE` naredbama kao normalna vrijednost. Umjesto `WHERE name <> NULL` koristi `WHERE name IS NOT NULL`.

`NULL` nije false, i nemoj ga koristi kao false. Npr. stupac `disabled` neka bude `NOT NULL`.

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

## Data Import

Ako trebaš importati veliki CSV file u bazu, overhead aplikacijskog frameworka može biti preskup. Umjesto toga, koristi direktno učitavanje fileova u tablicu:
* Postgres `COPY posts FROM 'tmp/new_posts.csv' CSV HEADER`
* MySQL `LOAD DATA INFILE '/tmp/posts.txt' INTO TABLE posts IGNORE 1 LINES`

## Locking

Lockanje na razini rowa:
* ako želiš redak zaključati samo za čitanje (da nitko drugi ne može pisati dotad) koristi `SELECT ... LOCK IN SHARE MODE`
* ako želiš redak zaključati za pisanje (nitko drugi ne može čitati ni pisati dotad) koristi `SELECT ... FOR UPDATE`

Kada drugi query želi napraviti SELECT koji uključuje taj redak, bit će blokiran dok se prvi query ne izvrši. Ako ne želi biti blokiran, može napraviti query s `FOR UPDATE NOWAIT` (izbacit će error) ili `FOR UPDATE SKIP LOCKED` (preskočit će lockane redove).

## Job Queue

Zahtjevi job queua u SQL-u su:
* Worker dohvaća prvi slobodan job i zauzima ga.
* Nijedan drugi worker ne smije uzeti job dok je zauzet.
* Ako worker naiđe na exception, job se mora automatski osloboditi.

Česta pogreška se događa u korištenju subquerija koji zapravo nisu atomarni. Npr:
```
UPDATE queue SET is_done = 't' WHERE itemid = (
    SELECT itemid FROM queue WHERE NOT is_done ORDER BY itemid FOR UPDATE LIMIT 1
)
```
neće raditi jer se SELECT subquery izvršava prvi, pa će dva workera istovremeno dohvatiti isti job. Jedan će ga updateati, a drugi će čekati na locku, i paralelnost je izgubljena. U slučaju da nema `FOR UPDATE` locka, oba će workera dohvatiti job i obraditi ga, što je još gore.

Umjesto toga, jobove dohvaćaj s:
```
DELETE FROM queue WHERE itemid =  (
    SELECT itemid FROM queue ORDER BY itemid FOR UPDATE SKIP LOCKED LIMIT 1
)
```

## Connection pooling

Connection pool sadrži listu već uspostavljenih konekcija koje aplikacija može koristiti kako bi izbjegla overhead spajanja.
*Framework Pooling*: kada se aplikacija pokreće, sama stvara pool konekcija. Dio mnogih frameworka (Sequel gem, Rails).
*Persistent Connections*: aplikacija održava konekciju po requestu. Nedostatak je što si ograničen samo na jednu po requestu.

Ako imaš više od 100 konekcija otvorenih, treba ti nešto robusnije kao `PG Bouncer`, standalone pooler.

Ako baza potroši sve dostupne konekcije, bacit će `max_connect_errors`. Ako se neki klijent pokuša spojiti previše puta, bit će blokiran s errorom `host_name blocked`. Da bi se host cache očistio pozovi `FLUSH HOSTS`.

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

## Indeksi

Indeksi su korisni samo za querije koji dohvaćaju mali broj rezultata; ili za izbjegavanje sortiranja. Engine neće koristiti indeks ako procjeni da je sekvencijalni scan brži (npr. ako ima previše rezultata).

Kada testiraš indekse, ne radi to na development mašini. Hoće li se indeks iskoristiti i kako ovisi o konfiguraciji db servera koji je vjerojatno drugačiji kod tebe.

## Encoding

MySQLov `utf8` zapravo ne podržava cijeli Unicode, nego samo 3 od 4 byta. Za pravi UTF-8, koristi `utf8mb4`.

# Literatura

* SQL Antipatterns by Bill Karwin
