# Types

*Type* je skup vrijednosti koje varijabla može poprimiti.

*Statically typed* jezici ograničavaju type svakoj varijabli, pa mogu provjeriti ispravnost tipova pri *compile timeu*.

*Dynamically typed* jezici ne znaju tip varijable dok ne dobiju konkretnu vrijednost u *runtimeu*.

To ne znači da statički jezici zahtjevaju pisanje type deklaracije (`int a`). `Haskell` recimo zaključuje tipove prema funkcijama koje se pozivaju.

Statički jezici su u pravilu brži od dinamičnih jer ne moraju provjeravati tipove prilikom runtimea.

Pojmovi *strong* i *weak typed* nemaju službenu definiciju, pa ih nemoj koristiti. Radije govori o konkretnim značajka type sistema: je li statično ili dinamično typed, radi li implicitno konvertiranje tipova (npr. `"a" + 1` je dopušten u  `Javascriptu`, u `Rubyju` nije), je li memory-safe itd.

Dodavanje tipova u dimačan jezik je otežano zbog `eval`. Pitanje je koji bi tip trebala vraćati funkcija koja izvodi proizvoljan kod? Neki jezici uvode *gradual typing*: po defaultu su dinamični, ali dopuštaju statičke anotacije (npr. `Python`, `Typescript`).

# Literatura

* https://gist.github.com/garybernhardt/122909856b570c5c457a6cd674795a9c

