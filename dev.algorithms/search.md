# Search

## List search

Ako nemaš sortiranu listu, moraš koristiti **linearno pretraživanje**, koje u najgorem slučaju radi `O(n)`.

Trik za malu optimizaciju je na kraj liste dodati element koji se traži (**sentinel**). Onda ne moraš u svakom koraku provjeravati `i < n`, nego samo `list[i] == el`. Kad pronađeš element, provjeriš je li njegov `i < n` (nalazi se u listi) ili nije.

Ako je lista sortirana, **binarno pretraživanje** je `O(log n)`.

Binarno pretraživanje nije pogodno ako je dohvaćanje pojedine vrijednosti sporo (npr. ako se čita s diska s glavom, ili dohvaća s neta). U tom slučaju je bolje podijeliti `N` elemenata u blokove veličine `sqrt(N)`, pretraživati slijedno početni element bloka, pa onda slijedno unutar bloka.

## State search

Pretraživanje prostora stanja (npr. rješavanje labirinta ili sokobana) može se rješavati *breadth-first* ili *width first*.

Breadth-first algoritam ima oblik `solve(initial_state, data)`, gdje je `data` immutable stanje svijeta (oblik terena, cilj), a `state` promjenjivo stanje (pozicije aktera). Držimo ih odvojene kako bi smanjili potrošnju memorije. `state.transitions(data)` daje sva iduća stanja u koje je moguće preći koji se dodaju u `queue`.

U iteraciji po stanjuma bitno je izbjeći kombinatornu eksploziju. Zato tijekom pretraživanja držimo dosad viđena stanja u setu `parents`, i ignoriramo ih ako se ponove. Ako su akteri međusobno zamjenjivi, možemo smanjiti entropiju tako što ćemo situacije u kojima su aktori zamijenili mjesta smatrati istim stanjem (npr. `result.actors.sort`).

## Optimizacije

Unaprijed definiraj veličinu svih containera kako bi izbjegao resize tijekom računanja: `HashMap::with_capacity(4 * 1024)`.

