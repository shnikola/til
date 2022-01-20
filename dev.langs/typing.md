# Typing

Type je skup vrijednosti koje varijabla može poprimiti.

## Type checking

Cilj type checkinga je spriječiti da se određene greške (npr. `int + string`) dogode prilikom izvršavanja programa. Može biti implementiran na različite načine.

**Static type checking** provjerava uvjete u *compile timeu*, prije pokretanja programa. Compiler svakoj varijabli dodjeljuje type i provjerava vrijednosti koje joj se dodjeljuju, operatori i funkcije u kojima se koristi.

**Dynamic type checking** provjerava uvjete u *run timeu*, tijekom izvršavanja programa. Prilikom svakog korištenja varijable provjerava se njen trenutni tip i funkcija s kojom se trenutno koristi.

Općenito, jezici sa static type checkingom su brži (tipovi se ne moraju pohranjivati ni provjeravati tijekom izvođenja) i dopuštaju ranije otkrivanje bugova. Jezici s dynamic checkingom su fleksibiliniji i pogodniji za prototipiranje, kada nije bitno da je svako moguće stanje pokriveno.

Static i dynamic checking su samo dvije točke na kontinuumu trenutaka u kojem se može vratiti greška. Static je prijavljuje u compile timeu, dynamic u run timeu, ali postoje i druge varijante: u editoru, u linkeru, ili ne prijavljivati grešku uopće (npr. `3/0 = Infinity`).

## Soundness and completeness

Neka type sistem želi spriječiti stanje `X`.

*Sound* sistem nikad neće dopustiti nevaljan program koji radi `X` (nema *false negative*).
*Complete* sistem nikad neće odbiti valjan program koji nikad ne radi `X` (nema *false positive*).

Prema teoremu neodlučivosti, statična provjera jezika ne može biti i sound i complete, a da se uvijek završi. Zato se većina programskih jezika odlučuje za *soundness* kako bi bili zaštićeni od `X`, po cijenu odbijanja nekih validnih programa. *Completeness* se može poboljšati s naprednim featurima poput genericsa.

Pritom se ne može reći da je sistem sound ili complete sam za sebe, nego za svako od pojedinačnih stanja `X` koje želimo spriječiti.

## Type inference

Statička provjera ne znači da jezik zahtjeva pisanje type deklaracije (npr. `int a`). Mnogi jezici koriste **type inference**, tj. automatsko zaključivanje typa pri compileu. Inference može biti jednostavan ili kompleksan, ovisno o samom jeziku.

Jezik može imati inference bez da su varijable typed, i može imati typed varijable bez da postoji inference (npr. Java)

## Compound types

Jezici uz elementarne tipove (`int`, `string`, ...) mogu podržavati i složene tipove:
* **Product type** opisuje tipove koje sadrže skup tipova, npr. `tuple (int, string)`.
* **Sum type** opisuje tipove koje sadrže jedan od tipova iz skupa, npr. `datatype rank = Jack | Queen | King | Num of int`.
* **Recursive type** opisuje tipove koje referiraju na same sebe, npr. `tree_node (value, tree_node left, tree_node right)`

## Subtyping

`ColorPoint {x: int, y: int, c: str}` je *subtype* od `Point {x: int, y: int}`. Type sistemi koji dopuštaju subtyping će dopusiti da funkcija koja prima `Point` može primiti i `ColorPoint.`

(Poseban slučaj je depth subtyping koji se sound samo ako su fieldovi immutable. Ako funkcija može mijenjati vrijdnost fieldova, depth subtyping se ne smije dopustiti)

Funkcija `f` se može zamijeniti s `g` pod sljedećim uvjetima:
a) `g` return type je subtype od `f` return typea, tj. funkcija vraća isto ili više.
b) `g` argumenti su supertype od `f` argumenata, tj. funkcija prima isto ili manje.

To znači da se funkcija `(int, int) -> (int, int)` može zamijeniti s `(int) -> (int, int, string)`.

Generici nisu isto što i subtype.
Genericima se definira ponašanje za općeniti type (`List<T>`).
Subtypingom se definira ponašanje za typove s određenim fieldovima (`Point`).

Neki jezici (`Java`) dopuštaju kombiniranje generica i subtypinga (*bounded polymorphism*):
`List<T> closest(List<T> pts) where <T extends Point>`

## Null

`null` bi trebao biti supertype svih tipova, tj. record bez fieldova. Umjesto toga, `Java` i `C#` ga tretiraju kao *supertype* svih tipova, tj. tip koji se može koristiti svugdje - zato gubimo garanciju da se `obj.m()` može pozvati bez straha za null exception.

## Gradual typing

Dodavanje tipova u dimačan jezik je otežano zbog `eval`. Pitanje je koji bi tip trebala vraćati funkcija koja izvodi proizvoljan kod? Neki jezici uvode *gradual typing*: po defaultu su dinamični, ali dopuštaju statičke anotacije (npr. `Python`, `Typescript`).

## Weak typing

Pojmovi *strong* i *weak typed* nemaju službenu definiciju i svakom znače nešto drugo: static vs dynamic checking, implicit vs explicit definitions, implicit vs explicit converting, itd.

Neki jezici (`C`, `C++`) dopuštaju implicitne konverzije pomoću pointera.
Neki jezici (`Ruby`) dopuštaju indeksiranje arraya izvan granica.
Neki jezici (`javascript`) douštaju pozivanje funkcija s krivim brojem argumenata.

Ništa od toga nema veze sa statičkim ili dinamičkim type checkingom. Oni jednostavno imaju fleksibilniju semantiku korištenja primitiva, i tako povećavaju skup dozvoljenih dozvoljenih programa po cijenu otkrivanja bugova.

# Literatura

* [https://gist.github.com/garybernhardt/122909856b570c5c457a6cd674795a9c]