# Elasticsearch

## Arhitektura

Za search dio Elasticsearcha zadužen je Apache Lucene, search engine napisan u Javi. Sam ES je framework za deployment Lucena kao distribuirane web aplikacije.

**Node** je jedna instanca Elasticsearcha (server ili proces na serveru). Node može imati različite uloge: data (rad s podatcima), master (upravljanje clusterom), client (održavanje konekcije s klijentima).

**Index** je ekvivalent databaseu u SQL bazama. Podatci se zapravo spremaju i dohvaćaju iz **shardova** koji se brinu za redundanciju. Postoje `primary` i `replica` shardovi, primary shard jedini prima podatke a ostali kopiraju do njega. Svaki shard pripada samo jednom nodeu i indexu.

Sami podaci se zapisuju u **segmentima**, immutable fileovima na disku. Svaki novi dokument stvorit će novi file. U pozadini, Lucene konstantno merga segmente spajajući te podatke u jedan file.

## Distribuiranost

Da bi se omogućila distribuiranost sustava, nodeovi se mogu međusobno spojiti u **cluster**. Jedan node će biti odabran za `master` i on je zadužen za održavanje clustera i donošenje konačnih odluka. Master može postati svaki node koji u konfiguraciji ima `node.master: true`, samo pripazi da ih ima neparan broj da izbjegneš split-brain situaciju.

Cluster radi tako da request možeš poslati bilo kojem nodeu i odgovor će biti isti. Ako radiš `search`, node će broadcastati request svim shardovima u indeksu, i onaj koji sadrži dokument će ga vratiti. Ako radiš `insert`, node će odabrati nasumični primary shard i zapisati ga u njega (i u njegove replike).

## Complex fields

Ako želiš da field bude array, ne trebaš ništa posebno specificirati. Svaki field može imati nijednu, jednu ili više vrijednosti. Jedino ograničenje je da sve vrijednosti u arrayu moraju biti istog tipa.

Praznim fieldovima smatra se `null`, `[]` ili `[null]`.

Za objekte fieldovi su mapirani u tip `object`, a njihovi interni fieldovi popisani su pod `properties`. Lucene ne zna za objekte, već ih indeksira kao fieldove, npr. `comments.name`.

U slučaju da pod fieldom `comments` imaš array objekata, prilikom matchanja array će se flattati tako da će u `comments.name` biti array sa svim vrijednostima fielda `name` u `comments` arrayu.

Ako želiš to izbjeći, možeš koristiti *Nested Objects* koristeći `nested` type.

## Dynamic Field Mapping

Kada se elastic susretne s fieldom dokumenta koji nije definiran u mappingu, on će ga dodati u mapping. Ako je vrijednost novog fielda `bool`, u mapping će ga zapisati kao `bool` i sl. Iznimka je `string`, kojeg će elastic pokušati pretvoriti u `date`, `double` ili `long`, a ako ne uspije, zapisat će ga kao `text`.

Ovo ponašanje se može disablati na razini objekta tako da se postavi `dynamic: false` (ignorira nepoznate fieldove) ili `dynamic: strict` (exception na nepoznate fieldove).

## Queries

Query može biti u `query` kontekstu gdje se računa `score` i rezultati se rangiraju po tome; ili može biti u `filter` kontekstu gdje je match `true` ili `false`.

`match_all: {}` vraća `score: 1.0` za sve dokumente.

### Full-text queries

Full-text queriji koriste se za pretraživanje dužeg teksta i koriste `analyzer`svakog fielda prije matchanja.

`match: { message: "search fo this" }` se interpretira kao boolean `or` query za svaku od riječi u search queriju.
`match: { message: { query: "search fo this", operator: "and" }}` ako želiš koristiti `and`.

`match_phrase: { message: "quick fox" }` traži fraze dopuštajući `slop`, tj. da riječi smiju biti razmaknute za određen broj koraka.
`match_phrase_prefix: { message: "quick brown f" }` isto kao `match_phrase`, a usto radi i prefix match na zadnjoj riječi fraze. Korisno za autocomplete.

`multi_match: { query: "search fo this", fields: ["subject", "message"] }` traži query istovremeno u više fieldova. Fieldovi se mogu navesti kao wildcard, npr. `*_name` za `first_name` i `last_name`. Pojedinom fieldu score se može boostati s `subject^3`.

### Common queries

Za svaku riječ u queriju `the brown fox` potrebno je napraviti zaseban upit. Često se koriste stop-words kako bi se izbjegli upiti riječi koje se nalaze u svim dokumentima. Problem s njima je što se onda upiti za `happy` i `not happy` ne razlikuju.

`common: { body: { query: "the brown fox"} }` radi common query koji prvo radi query za manje frekventne riječi, a onda nad rezultatima radi query za frekventne riječi poput `the`.

### Query strings

`query_string: { query: "subject:brown AND message:quick OR message:fox"}` omogućava boolean operatore u samom queriju.

`simple_query_string` koristi parser koji ne izbacuje exceptione i odabacuje invalid dijelove querija.

### Term level queries

Term level queries rade nad izrazima zapisanim u index, bez analize. Najčešće se radi o brojevima i datumima, a ne full-text fieldovima.

`term: { email: "nikola@mail.com" }` traži dokumente s točno tom vrijednosti fielda.
`terms: { tags: ['a', 'b'] }` pronalazi sve koje imaju tag `a` ili `b`.
Za sve koji imaju i `a` i `b` koristi `bool: { must: ... }`.

`range: { age: { gte: 10, lte: 20 }}` za raspone.
`range: { date: { gte: "now-1d"}}` za datume

`exists: { field: "email" }` matcha dokumente koji imaju ikakvu vrijednost u fieldu, pa makar i prazan string.
Za missing query, koristi `bool: { must_not: { exists: { ... }}}`.

`prefix: { email: "nikola@" }` matcha sve dokumente čiji field počinje s danom vrijednosti.
`wildcard: { email: "nik*@mail.com }` za wildcard matching.
`regexp: { email: "nik.{2}@mail.com }` za regex matching.

`ids: { values: ["1", "4", "100"] }` traži dokumente po `_id` fieldu.

## Compound queries

`bool: {}` query za kombiniranje se sastoji od 4 dijela:
  * `must: []` uvjeti moraju biti ispunjeni (AND).
  * `must_not: []` uvjeti koji ne smiju biti ispunjeni (NOT).
  * `should: []` bar jedan uvjet mora biti ispunjen (OR).
  * `filter: []` uvjeti moraju biti ispunjeni, ali se pokreću u non-scoring, filtering modu.

## Performance

`doc["field"]` je brži od `_source.field` jer je u memoriji.
