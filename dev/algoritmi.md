# Algoritmi

## Pretraživanje

Ako nemaš sortiranu listu, moraš koristiti **linearno pretraživanje**, koje u najgorem slučaju radi `O(n)`.

Trik za malu optimizaciju je na kraj liste dodati element koji se traži (**sentinel**). Onda ne moraš u svakom koraku provjeravati `i < n`, nego samo `list[i] == el`. Kad pronađeš element, provjeriš je li njegov `i < n` (nalazi se u listi) ili nije.

Ako je lista sortirana, **binarno pretraživanje** je `O(log n)`.

Binarno pretraživanje nije pogodno ako je dohvaćanje pojedine vrijednosti sporo (npr. ako se čita s diska s glavom, ili dohvaća s neta). U tom slučaju je bolje podijeliti `N` elemenata u blokove veličine `sqrt(N)`, pretraživati slijedno početni element bloka, pa onda slijedno unutar bloka.

## Raspoređivanje

Američki kongres zahtjeva da je u njemu broj zastupnika svake države proporcionalan broju stanovnika. Problem je što proporcije ne daju nužno cijeli broj zastupnika.

**Huntington-Hill** metoda: dodijeli po jedno mjesto svakoj državi. Zatim odaberi državu koja ima najveći prioritet, izračunat pomoću populacije `P` i trenutnog broja dodjeljenih mjesta `r`: `P / sqrt(r * (r + 1))`.

Postoje varijacije u računanju prioriteta, kao **D'Hondt** `P / (r + 1)` i **Webster-Sainte-Lague** `P / (2r + 1)`.

## Sortiranje

**Merge sort** dijeli array na dvije polovice, rekuzivno poziva merge sort na svakoj, i rezultat merga. Obje polovice su sortirane pa je merge `O(n)`. Cijeli algoritam je `O(nlogn)`.

**Quick sort** odabere pivot (npr. medijan, ili jednostavno zadnji element), pa stavi manje elemente od pivota lijevo, a veće desno (`O(n)`). Zatim rekurzivno poziva quick sort na lijevoj i desnoj strani. Najčešće je `O(nlogn)`, ali u najgorem slučaju (kad je array već sortiran) je `O(n^2)`. Ipak, u praksi je brži od ostalih sortiranja jer se može učinkovito implementirati na većini arhitektura.

## Quine

Quine je program koji, kada se pokrene, ispisuje svoj source. Ograničenja su da ne smije koristiti I/O (tj. čitati vlastiti file) i ne smije biti prazan.

Quine se uobičajeno sastoji od dva dijela: data koji sadrži code, i code koji ispisuje data. Format je:
```
data = "..."
puts "data = " + data.inspect + data
```

Data se složi da sadrži kod ispod prve linije: `"\nputs \"data = \" + data.inspect + data"`

# Literatura

* http://jeffe.cs.illinois.edu/teaching/algorithms
