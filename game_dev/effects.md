# Effects

## Shadows

Prostor je defaultno zasjenjen, a izvor svijetla radi ray casting u svim smjerovima da odredi dokle dopire svijetlo. Najjednostavnija implementacija šalje 360 linija u krug i izračunava presjeke s preprekama. Od točaka dobivenim presjecima iscrtava se poligon svijetla.

Optimalnije rješenje može se dobiti ako poznaješ oblike prepreka. Npr. ako su prepreke pravokutnici, dovoljno je slati linije prema njihovim vrhovima, što zahtjeva puno manje izračuna presjeka.

Za više izvora svijetla, samo iscrtaj poligon za svaki izvor.

Btw, sjene nisu sive - one su komplementarne boji svijetlosti.
