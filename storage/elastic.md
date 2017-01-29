# Elastic Search

## Combining Filters

`bool: {}` query za kombiniranje se sastoji od 4 dijela:
  * `must: []` uvjeti moraju biti ispunjeni (AND).
  * `must_not: []` uvjeti koji ne smiju biti ispunjeni (NOT).
  * `should: []` bar jedan uvjet mora biti ispunjen (OR).
  * `filter: []` uvjeti moraju biti ispunjeni, ali se pokreću u non-scoring, filtering modu.

## Complex fields

Za arraye nije potrebno posebno specificirati tip fielda. Svaki field može imati nula, jednu ili više vrijednosti. Jedino svaka vrijednost u arrayu mora biti istog tipa.

Praznim fieldovima smatra se `null`, `[]` ili `[null]`.

Za objekte fieldovi su mapirani u tip `object`, a njihovi interni fieldovi popisani su pod `properties`. Lucene ne zna za objekte, već ih indeksira kao fieldove, npr. `comments.name`.

U slučaju da pod fieldom `comments` imaš array objekata, prilikom matchanja array će se flattati tako da će u `comments.name` biti array sa svim vrijednostima fielda `name` u `comments` arrayu.

Ako želiš to izbjeći, možeš koristiti *Nested Objects* koristeći `nested` type.

## Matching multiple values

`terms: { tags: ['a', 'b'] }` pronalazi sve koje imaju tag `a` ili `b`, a ne točno `['a', 'b']`

## Performance

* `doc["field"]` je brži od `_source.field` jer je u memoriji.
