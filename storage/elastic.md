# ElasticSearch

## Combining Filters
`bool: {}` query za kombiniranje se sastoji od 4 dijela:
  * `must: []` uvjeti moraju biti ispunjeni (AND).
  * `must_not: []` uvjeti koji ne smiju biti ispunjeni (NOT).
  * `should: []` bar jedan uvjet mora biti ispunjen (OR).
  * `filter: []` uvjeti moraju biti ispunjeni, ali se pokreću u non-scoring, filtering modu.
