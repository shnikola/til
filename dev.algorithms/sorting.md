# Sorting

## Raspoređivanje

Američki kongres zahtjeva da je u njemu broj zastupnika svake države proporcionalan broju stanovnika. Problem je što proporcije ne daju nužno cijeli broj zastupnika.

**Huntington-Hill** metoda: dodijeli po jedno mjesto svakoj državi. Zatim odaberi državu koja ima najveći prioritet, izračunat pomoću populacije `P` i trenutnog broja dodjeljenih mjesta `r`: `P / sqrt(r * (r + 1))`.

Postoje varijacije u računanju prioriteta, kao **D'Hondt** `P / (r + 1)` i **Webster-Sainte-Lague** `P / (2r + 1)`.

## Sortiranje

**Merge sort** dijeli array na dvije polovice, rekuzivno poziva merge sort na svakoj, i rezultat merga. Obje polovice su sortirane pa je merge `O(n)`. Cijeli algoritam je `O(nlogn)`.

**Quick sort** odabere pivot (npr. medijan, ili jednostavno zadnji element), pa stavi manje elemente od pivota lijevo, a veće desno (`O(n)`). Zatim rekurzivno poziva quick sort na lijevoj i desnoj strani. Najčešće je `O(nlogn)`, ali u najgorem slučaju (kad je array već sortiran) je `O(n^2)`. Ipak, u praksi je brži od ostalih sortiranja jer se može učinkovito implementirati na većini arhitektura.

**Sleep sort** generira worker za svaki broj koji spava linearno s veličinom broja, a zatim vrati broj mainu. Brojevi će se vratiti sortiranim redoslijedom.

# Literatura

* [http://jeffe.cs.illinois.edu/teaching/algorithms]
