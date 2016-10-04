# Computer Science

## Types
https://gist.github.com/garybernhardt/122909856b570c5c457a6cd674795a9c
*Type* je skup vrijednosti koje varijabla može poprimiti.
* *Statically typed* jezici ograničavaju type svakoj varijabli, pa mogu provjeriti ispravnost tipova pri *compile timeu*.
* *Dynamically typed* jezici ne znaju tip varijable dok ne dobiju konkretnu vrijednost u *runtimeu*.

To *ne znači* da statični jezici zahtjevaju pisanje type deklaracije (`int a`) - `Haskell` recimo zaključuje tipove prema funkcijama koje se pozivaju.

Statički jezici su u pravilu brži od dinamičnih jer ne moraju provjeravati tipove prilikom runtimea.


Pojmovi *strong* i *weak typed* nemaju službenu definiciju, pa ih nemoj koristiti. Radije govori o konkretnim značajka type sistema: je li statično ili dinamično typed, radi li implictno konvertiranje između tipova (npr. `"a" + 1` je dopušten u  `Javascriptu`, u `Rubiju` nije), je li memory-safe itd.

Dodavanje tipova u dimačan jezik je otežano zbog `eval`. Pitanje je koji bi tip trebala vraćati funkcija koja izvodi bilo koji komad koda? Neki jezici uvode *gradual typing*: po defaultu su dinamčni, ali dopuštaju statičke anotacije (npr. `Python`, `Typescript`).
