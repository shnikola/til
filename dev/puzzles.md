# Programming Puzzles

## Pravokutnici

Dana je lista točaka u koordinatnom sustavu, zadatak je naći koliko pravokutnika paralelnih s koordinatnim osima se može nacrtati.

Svaki pravokutnik određuju dva vertikalna brida. Trebamo grupirati bridove s istim `y1` i `y2`. Ako imamo `n` bridova u istoj ravnini, oni čine `n*(n-1)/2` pravokutnika.

Za svaku točku iteriramo kroz sve točke točno iznad nje, i zapišemo brid u map counter. Na kraju prođemo kroz mapu i izračunamo broj bridova.
