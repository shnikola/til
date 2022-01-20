# Object Oriented Programming

OOP je ideja o *organizaciji koda*. Klase skrivaju podatke i pružaju interface za rad s tim podatcima.

## Inheritance

Inheritance omogućava dodavanje novih funkcionalnosti bez potrebe da se mijenja postojeći kod.

Subclass nasljeđuje sve metode od parenta, ali svaku od njih može *overrideati* s vlastitom implementacijom.

Prilikom pozivanja metode, objekt prolazi kroz ancestor klase i odabire implementaciju koju će upotrijebiti. To se naziva *dynamic dispatch*.

Problem je što za pisanje subclassa često treba dobro poznavati superclass (što ako se neka overrideana metoda interno koristi?) Inheritance je koristan alat, ali rjeđe nego se propagira.

## Abstract methods

Postoji paralela između apstraktnih metoda i first-order funkcija. U oba slučaja, jezik omogućava pattern da se kodu `F` prosljeđuje drugi kod `G`.

U OOP, metoda `F` superklase poziva apstraktnu metodu `G`, a različite subklase pružaju različite implementacije koje će se pozvati kroz dynamic dispatch.

U FP, funkcija `F` prima funkciju `G` kao argument, a različiti calleri šalju različite implementacije koje će se pozvati u tijelu funkcije `F`.

## FP vs OOP

Svaki program se sastoji od `A` tipova podataka i `B` operacija nad njima, tj. `AxB` različitih ponašanja koje treba implementirati. Razlika između funkcijskog i objektno-orijentiranog programiranja je kako organiziraju kod: prema tipovima podataka ili prema operacijama.

OOP organizira program u `A` klasa. Svaka klasa će imati `B` metoda za svaku od operacija. Lako je dodati novi type (doda se nova klasa), teško je dodati novu operaciju (treba dodati metodu u svaku klasu). Pomaže korištenje interfacea i apstraktnih metoda.

FP organizira program u `B` funkcija. Svaka funkcija će imati `case when` za svaki od `A` tipova. Lako je dodati novu operaciju (doda se nova funkcija), teško je dodati novi type (treba ga dodati u svaki `case`). Pomaže static typing koji će reći da pattern matching u caseu nije pokrio sve slučajeve.

Koji pristup je bolji ovisi o tome što radiš (npr. za interpreter funkcijski, za GUI je objektni) i o promjenama koje očekuješ.

## Double Dispatch

Poseban problem prešućen gore su operacije s dva typea (npr. `add(x, y)`), što čini `A²` kombinacija različitih typova.

FP će `add` implementirati kao funkciju s `A²` caseova.

OOP će dodati metodu `add(x)`, ali umjesto da u njoj radi `if x.is_a?(String)`, bolje rješenje je koristiti *double dispatch*: svaka klasa definira pomoćne `add_int`, `add_string`, `add_rational`, a `Integer#add(x)` će se proslijediti na `x.add_int(self)`.

Još bolje je ako jezik dopušta multimethods tipa `add(int)`, `add(string)`, `add(Rational)`, pa će dispatch pri runtimeu odrediti koju od metoda pozvati prema klasi argumenta

## Class vs Type

Statically typed OOP jezicima su type i class isto (objekt klase `Point` ima type `Point`), ali radi se o dva različita koncepta:
* *classom* se definira ponašanje objekta (koje metode pruža)
* *typeom* se definira interface pojedine metode (koje argumente prima, što vraća)

