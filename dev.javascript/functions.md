# Functions

`function f() { ... }` je function declaration. Tako deklarirana funkcija se može koristiti i prije deklaracije, jer ih JS engine učita prije izvršavanja skripte.

`var f = function() { ... }` je function expression. Korisna je u slučaju da želiš kondicionalno stvoriti funkciju, npr. `f = check ? function() {...} : function() {...}`.

`var f = (n) => n + 1` je arrow function, skraćeni oblik pisanja expressiona `function(n) { return n + 1; }`. Za ignoriranje parametara koristi `() => ...`. Za više linija koristi `{ body }`` i `return`.

Arrow funkcije ne stavaraju novi function scope, što znači da ne moraš koristi `bind(this)` ili `var that = this`.

## Parameters

Funkcije mogu biti pozvane s proizvoljno parametara, neovisno o tome koliko ih je u definiciji funkcije.

`function sum(a = 0, b = 0)` postavlja defaultne vrijednosti.

`function sum(...vals)` proizvoljan broj parametara pretvara u array.

`function sum({a = 1, b = 2} = {})` koristi named parametre.

## Closure

Closure je funkcija kojoj su pri izvršavanju dostupne varijable definirane  izvan nje, npr. `let a = 1; function() { return a; }`.

U Javascriptu, sve funkcije su closurei. Automatski pamte gdje su kreirane i scopeove iznad sebe.

Jedina izminka su funkcije definirane na opskuran način pomoću
`new Function('a', 'b', 'return a + b')`. One imaju pristup samo globalnom objektu i nikakvim drugim varijablama.

## Function kao objekt

Funkcije nisu poseban type, one su javascript objekti, i kao takve imaju propertije.

`f.name` sadrži ime funkcije koje JS dohvati iz definicije funkcije ili assignmenta u varijablu za function expression.

`f.length` je broj parametara u definiciji. Rest parametri se ne broje.

Funkciji možeš dodavati i custom parametre. To npr. radi jQuery koji osnovnoj funkciji `$()` dodaje pomoćne poput `$.forEach()`.

## this

Funkcije spremljene unutar objekta mogu koristiti `this` kao referencu na objekt. Unutar `user.message()` funkcije, `this = user`.

Iznimka su arrow funkcije, koje ne postavljaju `this`, već zadržavaju kontekst funkcije u kojoj se nalaze.

Ako funkcija nije pridružena objektu, `this` će vratiti `undefined`. Povijesno se vraćao `window`, ali strict mode je to promjenio.

Za razliku od ostalih jezika (npr. `this` u Javi, `self` u Rubyju), `this` u JSu je unbound, tj. evaluira se u runtimeu. To omogućava da se funkcija može reusati u više objekata. S druge strane, pogrešno korištenje se neće detektirati pri parsiranju.

`f.apply(obj, args)` je dinamički način da pozoveš `obj.f(args)`. Korisno ako želiš napraviti generični dekorator za funkciju, npr.
`function wrapper() { return f.apply(this, arguments) }`.

## Debounce i Throttle

Ako želiš ograničiti broj izvršavanja neke funkcije, možeš koristiti debounce i throttle. Nisu nativno implementirani, ali većina librarija ih ima.

`debounce(f, 400)` će pozvati funkciju tek nakon što se event nije dogodio `400ms`, sa zadnjim poslanim argumentima. Korisno za procesiranje konačnog rezultata (npr. korisnik je prestao tipkati ili dovršio resize).

`throttle(f, 400)` će pozvati funkciju prvi put i onda maksimalno jednom svakih `400ms`. Korisno za praćenje updateova koji ne smiju biti prečesti (npr. scrolling ili mousemove)
