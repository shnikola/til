# Rust

Rust je low-level jezik (uspoređuje se s C++) odlične sintakse (slične Rubyju). Najveće prednosti Rusta su **static typing** koji je dovoljno pametan da većinu tipova sam infera, i **ownership** sutav kao alternativa uobičajenim metodama memory managementa.

Tjeranje developera da bude eksplicitan u svojim namjerama (npr. mutability, lifetime varijabli) omogućuje da se većina grešaka ulovi u compiletimeu, što je super. S druge strane, ownership nije baš najintuitivniji i često imaš osjećaj da se boriš s compilerom ili randomly stavljaš znakove dok ne bude sretan (`let s3 = s1 + &s2`).

Da sam prisiljen raditi u C-u, Rust bi mi se činio kao moderna, neizmjerno bolja alernativa - preuzeo je pregršt dobrih ideja iz drugih jezika (`match`, `iter` chaining, error handling s `Option` i `Result`) koje olakšavaju život programeru. Ali nepobitna je činjenica da se C toliko ukorijenio da će ga teško ijedan jezik izgurati s trenutne pozicije, pa Rust još neodlučno plovi, tražeći svoju primarnu namjenu.

## Functions

`fn is_divisible_by(lhs: u32, rhs: u32) -> bool { ... }` definira funkciju s `u32` inputima i `bool` outputom.

`fn print(n: u32) { ... }` ako funkcija nema output, možeš preskočiti `->` dio.

`fn print(n: mut u32) { ... }` ako dopuštaš da se input mijenja.

## Structures and Methods

`struct User { name: String, age: u32 }` definira strukturu s propertijima.

`user = User { name: "Nikola", age: 33 }` instancira strukturu.

`impl User { fn sayHello(&self) { ... } }` dodaje instance metodu na `User` koju pozivaš s `user.sayHello()`.

`&self` je syntax sugar za `self: &Self`, gdje je `Self` tip objekta nad kojim se metoda poziv. Ako metoda mijenja instancu, koristi `fn change(&mut self)`.

`impl User { fn search() { ... } }` je statička metoda (associated function) koju pozivaš s `User::search()`.

## Matching

`match a { ... }` ide po zadanim izrazima i vraća prvi koji se slaže uz x.

`1 | 2 => ...` za kombiniranje slučajeva.
`3..=6 => ...` za range.
`Point { x, y: 1 } => ...` za destruct s provjerom vrijednosti.
`Point { x, y } if x > 2 => ...` dodatni uvjet.
`_ => ...` wildcard patter, koristi za "else" izraz.

## Enums

Enum omogućuje da definiraš type tako da nabrojiš njegove moguće vrijednosti, npr. `enum Encoding { Utf8, Ascii }`. Pojedina vrijednost se koristi kao `Encoding::Utf8`.

Enum može imati dodatne podatke vezane uz svoje varijante, npr.
`enum IpAddr { V4(u8, u8, u8, u8), V6(String) }`. Pojedinu IP adresu možemo instancirati kao `IpAddr::V4(127, 0, 0, 1)`. Na taj način se dobija varijanta "subclassa" koje imaju isti type, pa ih možemo slati u iste funkcije.

Enumi se dobro slažu s matchanjem:
```
    match ip {
        IpAddr::V4 => "legacy",
        IpAddr::V6(str) => str,
    }
```

## Option

Option type se koristi za slučajeve kada vrijednost može biti nešto ili ništa. Na ovaj način compiler prisiljava developera da handla oba slučaja.

Option je enum tipa `enum Option<T> { Some(T), None }`. Handla ga se matchanjem, npr.
```
    let result = match o {
        Some(i) => i,
        None => return Err("Didn't get a result"),
    }
```
Match mora pokriti sve moguće vrijednosti enuma, i ako zaboravimo neku compiler će se pobuniti. Za catch-all slučaj koristi `_ => ...`.

Ako te zanima samo jedan uvjet matcha, koristi `if let` syntax sugar:
```
if let Some(i) = o {
    println!("{}", i);
}
```

## Error Handling

`panic!("Something went wrong")` koristi za unrecoverable errore, gdje će se program prestati izvoditi ako dođe do tog koda.

`Result { Ok(T), Err(E) }` enum koristi za pristojnije handlanje errora.
Npr. `let r = File::open("hello.txt")` vraća Result enum.

`f = File::open("hello.txt").unwrap()` će vratiti rezultat ako je Ok, ili napraviti panic ako je Err.
`f = File::open("hello.txt").expect("Failed to open hello.txt")` radi isto kao `unwrap()`, samo omogućuje custom poruku za panic.
`f = File::open("hello.txt").unwrap_or_else(|error| ... )` prima closure za pametnije handlanje errora.

Operator `?` će prekinuti izvođenje ako naiđe na `Err` i vratiti ga iz funkcije, npr. `File::open("hello.txt")?.read_to_string(&mut s)?`.

## Generics

Generics omogućuju da istovremeno definiraš varijante iste funkcije za različite tipove.

Za funkcije: `fn largest<T>(list: &[T]) -> &T { ... }`
Za strukturu: `struct Point<T> { x: T, y: T }`

## Traits

Traitovi su skup funkcija koje neki tip može implementirati (nešto kao interface).

`trait Summary { fn summarize(&self) -> String; }` definira trait (samo potpis bez implementacije).

`impl Summary for Tweet { summarize(&self) -> String { ... } }` implementira trait za strukturu.

Trait može biti definiran s defaultnom implementacijom. U tom slučaju dovoljno ga je dodati strukturi s `impl Summary for Tweet {}`.

Traitovi se mogu koristiti umjesto tipova u funkcijama, npr.
`fn notify(item: &impl Summary) { ... }` će primiti bilo koji `item` koji implementira `Summary`.
`fn notify<T: Summary>(item: &T)` je duži način (*trait bound*) za pisanje istog koji omogućuje veću fleksibilnost.
`fn notify(item: &(impl Summary + Display)) { ... }` zahtjeva da `item` sadrži oba traita.

Moguće je implementirati trait na svim tipovima koji ispunjavaju neki uvjet:
`impl<T: Display> ToString for T { ... }` implementira `ToString` trait svim tipovima koji imaju `Display` trait.

## Display

`#[derive(Debug)]` iznad strukture omogućava ispisivanje instance pomoću `println!("{:?}", i)`.

## Memory management

Za upravljanje memorijom većina jezika koriste ili ručno alociranje i oslobađanje (npr. C) ili garbage collection (npr. Java). Jedan od jedinstvenih featurea Rusta je **ownership**.

Postoje stroga pravila kako se podatci smiju dijeliti i proslijeđivati i zahvaljujući tome memorija se može automatski alocirati i oslobađati bez korištenje garbage collectora. Alokacija memorije se analizira pri compile timeu, pa je nemoguće da se dogodi memory leak.

Za početak, korisno je znati razliku između **stacka** i **heapa**. Na stack se stavljaju podatci fiksne veličine: primitivni tipovi (npr. `u32`, `f32`, `bool`) i arrayi. Kad se u kodu poziva funkcija, vrijednosti proslijeđene funkciji i njene lokalne varijable se stavljaju na stack. Kada je funkcija završena, te vrijednosti se popaju sa stacka i više ne koriste.

Podatci čija veličina nije poznata pri compile timeu (npr. mutable `String`, `Vector`) se stavljaju na heap, a u varijablu (tj. stack) se zapisuje pointer na heap. Zapisivanje u heap je sporije jer zahtjeva pronalaženje dovoljno velikog komada memorije, te svako čitanje zahtjeva čitanje pointera.

Rustov ownership se brine o tome koji su podatci na heapu. `let s = String::from("hello")` će zapisati string na heap, i čim se scope u kojem je `s` definiran zatvori, memorija će se automatski osloboditi.

## Ownership

1) Svaka vrijednost u Rust programu ima varijablu koja se naziva **owner**.
2) Svaka vrijednost može imati samo jednog ownera.
3) Kada owner izađe iz scopea, vrijednost se odbacuje (`drop`) i njena memorija se oslobađa.

Odlična stvar kod ovakvog sistema je što sprječava da dvije varijable istovremeno mijenjaju isti podatak. Samo jedna varijabla može biti vlasnica podatka. Ako assignaš vrijednost u drugu varijablu `let b = a`, varijabla `a` se više ne može koristiti.

Slanje variabli u funkciju je isto vrsta assignanja. `foo(name)` daje ownership varijable `name` funkciji, i nakon izvršenja vrijednost se dropa. Jedan način da je se nastavi koristiti je da je funkcija vrati nazad, npr. `let n = foo(name)`, ali to je uglavnom gnjavaža.

Ako želiš poslati varijablu u funkciju bez da izgubiš ownership, možeš napraviti **borrow** i poslati referencu. `foo(&name)` funkciji šalje referencu na varijablu `name` koja se nakon izvršenja dropa, ali ownership ostaje kod `name`. Referenca je immutable, pa se funkcija ne može mijenjati borrowanu varijablu.

Ako želiš borrowati vrijednost funkciji i dopustiti da je promijeni, pozivaš s `foo(&mut name)`. Rust ne dopušta da napraviš više od jedne mutable reference istovremeno, pa se podatkovni race condition ne može dogoditi. Također je nemoguće stvoriti null pointer ili dangling pointer - compiler neće dopustiti ništa od toga.

Ukratko, velik broj programerskih muka dolazi iz **shared mutable statea**. Dok se funkcijsko programiranje izbjegava mutable state, Rust izbjegava shared state.

## Copying

Zbog pravila ownershipa `let s2 = s1` ne radi kopiju, nego samo prebacuje referencu na novog ownera. Prednost toga je što se nikad neće raditi "deep" kopija podataka na heapu (kao npr. Ruby kad radiš `arr.sort.reverse`).

Ako želiš kopiju možeš je pozvati eksplicitno s `s.clone()` ili definirati `Copy` trait za svoj type.

## Smart Pointers

Rust ima dvije vrste pointera: reference (`&a`) koje samo sadrže broj adrese u memoriji, i smart pointere. Smart pointeri omogućuju čuvanje dodatnih informacija o podacima koji se referenciraju, a mogu i sami ownati i mijenjati podatke. Često ne moraš znati da je objekt smart pointer da bi ga koristio, npr. `String` i `Vec<T>` su smart pointeri.

`Box<T>` je generičan smart pointer objekt koji se može koristiti za različite slučajeve kada želiš držati vrijednost na heapu umjesto na stacku.

Rust pri kompilaciji mora znati veličinu svakog typea. To je problem ako želiš napraviti rekurzivan type poput: `enum List { Cons(i32, List), Nil }`.
U tom slučaju možeš koristiti `Box` pointer, čija je veličina fiksna:
`enum List { Cons(i32, Box<List>), Nil }`.

Također, ako imaš veliku količinu podataka na stacku čiji ownership prebacuješ, uglavnom ne želiš da ih se kopira. Koristeći `Box`, podatci će se držati na heapu i neće se kopirati.

## Tuple & Array

Tuple `(1, 'a')` je niz vrijednosti fiksne duljine. Vrijednosti mogu biti različitih tipova. `t.0`, `t.1` za dohvaćanje pojedinog elementa.

Array `[1, 2, 3]` je niz vrijednosti istog tipa. Duljina im se mora znati pri compile timeu, pa se array uglavnom ne koristi, eventualno za fiskne liste tipa `MONTHS = ["January", "February", ...]`.

Tuple i array su fiksnih veličina pa se alociraju na stacku.

Referenca arraya, vektora ili stringa, `&[i32]` ili `&str`, naziva se **slice**. Možeš referencirati cijeli niz `foo(&arr)` ili dio `foo(&arr[2..6])`.

## Vector

Vector `vec![1, 2, 3]` je lista dinamične veličine. Sprema se na heapu. Type mu je `Vec<T>`.

Element dohvaćaš s:
* `&v[100]` vraća referencu, radi panic ako je out of scope.
* `v.get(100)` vraća `Option`, `None` ako je out of scope.

`for i in &v` iterira po elementima vektora.
`for i in &mut v` iterira i dopušta mijenjanje elemenata.

## HashMap

`let count = map.entry(word).or_insert(0)` je `map[word] || = 0`
`*count += 1` za mijenjanje vrijednosti.

## Closures

`let c = |x| { x + 1 }` je closure, anonimna funkcija s environmentom (kao Ruby block). Ne zahtjeva pisanje tipova, nego se infera iz prvog korištenja.

Closure može koristiti varijable definirane iznad njegovog scopea, i defaultno ih borrowa. Ako želiš preuzeti njihov ownership, koristi:
`let c = move |x| { a + x }`

Svaki closure ima jedinstveni, anonimni type. Compiler im dodjeljuje traitove ovisno o tome kako koriste environment: defaultno implementiraju `FnOnce` (mogu se pozvati bar jednom), ako samo borrowaju mutably dobiju i `FnMut`, ako borrowaju immutably dobiju i `Fn`.

## Iterators

`let mut iter = v.iter()` vraća lazy iterator nad iterabilnim objektom.
`iter.next()` vraća `Option` sa sljedećim elementom (ili `None`).

Za ručno pozivanje `next()` potrebno je da `iter` bude mutable jer mijenjamo njegovo interno stanje. Ne moramo to raditi u `for` petljama, je one preuzimaju ownership i interno ga učine mutabilnim.

`v.iter()` vraća immutable reference na elemente kolekcije.
`v.iter_mut()` vraća mutable reference na elemente.
`v.into_iter()` preuzima ownership i vraća owned elemente.

Iterator adaptors su metode koje vraćaju novi iterator:
* `v.iter().enumerate()` vraća tuple (element, index).
* `v.iter().map(|x| x + 1)`
* `v.iter().filter(|x| x > 5)`

Consuming adaptors su metode koje konzumiraju iterator i vraćaju rezultat:
* `v.iter().collect()` pretvara iterator u kolekciju
* `v.iter().sum()`

## IO

`fs::read_to_string("file.txt")` čita cijeli file u string.

`println!()` macro ispisuje na STDOUT.
`eprintln!()` ispisuje na STDERR

## Modules

Modul je skup funkcija, structova i trait blokova. Omogućava organizaciju koda unutar projekta za reuse i kontrolu privatnosti.

`mod my_mod { ... }` stvara modul čiji su članovi privatni.
`pub fn foo()` definira funkciju u modulu kao public, pa se može zvati izvan modula.

Da bi koristio funkciju ili struct iz nekog modula, referencirati apsolutno i relativno:
`crate::some_folder::my_mod::foo()` je apsolutni path, `crate` znači root trenutnog cratea.
`self::my_mod::function()` je relativni path, `self` znači trenutni modul.

`use crate::some_folder::my_mod` dodaje `my_mod` u scope kao symbolic link, pa možeš pozivati funkciju s `my_mod::foo()`.

Možeš dodati i samu funkciju s `use crate::some_folder::my_mod::foo`, ali konvencija je koristiti parent module kako bi se razlikovala od lokalno definiranih funkcija.

Ako bi `use` doveo do konflikta u imenu, možeš definirati novi naziv koristeći `use std::io::Result as IoResult`.

## Packages

`cargo new my-project` stvara novi project, tj. package. Metadata packagea i upute za buildanje nalaze se u `Cargo.toml` fileu.

Defaultno, `cargo build` će buildati jedan **binary crate** koristeći `src/main.rs`. Package može imati više binary crateova ako ih drži u `src/bin/` folderu.

Ako postoji `src/lib.rs`, buildat će **library crate**.

# Literature

* [https://doc.rust-lang.org/book]
