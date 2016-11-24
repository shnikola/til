# Coding

## Types
https://gist.github.com/garybernhardt/122909856b570c5c457a6cd674795a9c
*Type* je skup vrijednosti koje varijabla može poprimiti.
* *Statically typed* jezici ograničavaju type svakoj varijabli, pa mogu provjeriti ispravnost tipova pri *compile timeu*.
* *Dynamically typed* jezici ne znaju tip varijable dok ne dobiju konkretnu vrijednost u *runtimeu*.

To *ne znači* da statični jezici zahtjevaju pisanje type deklaracije (`int a`) - `Haskell` recimo zaključuje tipove prema funkcijama koje se pozivaju.

Statički jezici su u pravilu brži od dinamičnih jer ne moraju provjeravati tipove prilikom runtimea.


Pojmovi *strong* i *weak typed* nemaju službenu definiciju, pa ih nemoj koristiti. Radije govori o konkretnim značajka type sistema: je li statično ili dinamično typed, radi li implictno konvertiranje između tipova (npr. `"a" + 1` je dopušten u  `Javascriptu`, u `Rubiju` nije), je li memory-safe itd.

Dodavanje tipova u dimačan jezik je otežano zbog `eval`. Pitanje je koji bi tip trebala vraćati funkcija koja izvodi bilo koji komad koda? Neki jezici uvode *gradual typing*: po defaultu su dinamčni, ali dopuštaju statičke anotacije (npr. `Python`, `Typescript`).


## 5 lessons in object-oriented design from Sandi Metz
https://18f.gsa.gov/2016/06/24/5-lessons-in-object-oriented-design-from-sandi-metz
* Jedini razlog refaktoriranja koda je kako bi ga se lakše mijenjalo u budućnosti.
* Počni od duplikacije pa pojednostavni. Duplikacija je jeftinija od pogrešne apstrakcije.
* Refactoring treba biti "safe and boring" - puno testova koje nakon svake pojedine promjene pokrećeš.
* Napiši najbolji mogući kod sada, ali budi spreman da ga obrišeš sutra.


## 10 Modern Software Over-Engineering Mistakes
https://medium.com/@rdsubhas/10-modern-software-engineering-mistakes-bc67fbef4fc8
* Business uvijek pobjeđuje development. Zahtjevi uvijek divergiraju.
* Preferiraj izolirane akcije umjesto reusanja business logike, koja će se sigurno mijenjati.
* Prije dodavanja featura razmisli hoće li se koristiti (npr. je li stvarno potreban CMS ili OAuth login)


## Null Object
Isplati se ako imaš puno conditionala, npr. `if (user) { user.name } else { 'No name.' }`. Downside je što treba držati public interface `User` i `NullUser` usklađenim.


## Growing a Language, Guy Steele (1998)
https://www.youtube.com/watch?v=_ahvzDzKdB0
Predavanje u kojem predavač ograničava svoj jezik na jednosložne riječi, a ostatak definira po potrebi.
Nekoliko zanimljivih crtica:
* Programski jezik se treba razvijati prirodno, kroz community ljudi koji ga koriste.
* Ne planiraj kako će ti jezik izgledati, jer onda community nema osjećaj da sudjeluje u odlukama.
* Treba omogućiti da korisničke definicije budu što sličnije programskim primitivima (seamless).
* "Plan for Growth, Plan for Warts"
* "Meta means to step back from your own place. What you used to do is now what you see. What you were is now what you act on. Verbs turn to nouns."
