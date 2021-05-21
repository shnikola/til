# Javascript

## Varijable

`let x` koristi za promjenjive vrijednosti. Poštuje leksički (block) scope, što znači da ako ga definiraš u `if` bloku, neće biti vidljiv izvan njega.

`const x` koristi za vrijednosti koje jednom postaviš. Poštuje leksički scope.

`var x` se više ne koristi. Ne poznaje block scope, nego je scopan na trenutnu funkciju. Zato se koristio IIFE da ga se ograniči. Dopušta da se deklarira dvaput, pa ga se može slučajno prebrisati.

## Data Types

Javascript ima 7 primitivnih tipova podataka koji su **immutable**:
* `number` za brojeve.
* `bigint` za brojeve veće od `2^53-1`, formata `10n`.
* `string` za tekst.
* `boolean` za true i false.
* `null` za nedostatak vrijednosti.
* `undefined` defaultna vrijednost nepostavljenih varijabli.
* `symbol` identifikatori.

Osmi tip je `object`, i on jedini **mutable** tip.

Tip varijable možeš dobiti operatorom `typeof` (uz napomenu da `typeof null` vraća `object`, što je greška u jeziku).

Funkcije su zapravo tipa `object`, ali `typeof f` vraća `function` da bude zgodnije.

Primitivni tipovi su napravljeni da budu lightweight, ali i dalje podržavaju pozivanje metoda poput `str.toUpperCase()`. Pri takvom pozivu, primitiv se privremeno wrapa u object `String` koji ima sve dodatne propertije i metode.

## Conversions

Operatori i funkcije će automatski konvertirati vrijednosti u type koji koriste. Npr. `alert(5)` će `5` konvertirati u string.

Matematički operatori konvertiraju sve u brojeve, npr. `'6' / '3'` je `2`.
`'6' + '3'` je `'63'` jer string ima definiran operator `+`.

`Boolean(value)` eksplicitno pretvara u boolean.
Falsey vrijednosti su `0`, `""`, `null`, `undefined` i `NaN`. Sve ostalo je true.

`String(value)` eksplicitno pretvara u string.
`String(null) = "null"`, što je malo blesavo.

`Number(str)` (skraćeno: `+str`) eksplicitno pretvara u broj. String mora biti točno broj (jedino uklanja whitespace s početka i kraja), ili vraća `NaN`.
`Number(null) = 0`, ali `Number(undefined) = NaN`.

`parseInt(str)` i `parseFloat(str)` čitaju string po redu i konvertiraju koliko mogu. To je korisno za CSS propertije poput `100px` ili `12rem`.

## Comparisons

Kad se uspoređuju varijable različitog tipa, konvertiraju se u brojeve. Npr. `'02' > true` će uspoređivati `2 > 1`.

To ne vrijedi za `null` i `undefined`. Oni imaju dosta nelogična pravila, pa samo izbjegavaj raditi `<` i `>` s njima.

## Numbers

Operacije s brojevima su uvijek safe - nikad ne možeš dobiti exception, samo `NaN` kao rezultat.

Koristi `isNaN(x)` za provjeru da li je nešto `NaN`. `x === NaN` uvijek vraća `false`.

## Globalni objekt

U globalnom objektu se nalaze varijable i funkcije koje su dostupne svugdje, npr. `alert()` ili `Array()`.

U browseru se taj objekt zove `window`, u Node.jsu `global`. Stadardizirani način za pristupanje u svim environmentima je `globalThis`.

## setTimeout & setInterval

`setTimeout(f, 300)` će pozvati funkciju `f` za 300ms.
`setInterval(f, 300)` će pozivati funkciju svakih 300ms.

Možeš definirati i argumente s kojima će se pozvati funkcija pomoću
`setTimeout(f, 300, "a", "b")`.

Obje funckije vraćaju timer id kojeg možeš koristiti za cancelanje `clearTimeout(id)` i `clearInterval(id)`.

`setTimeout(func, 0)` znači da se funkcija poziva što je prije moguće, čim trenutna skripta završi izvođenje.

Scheduling ne garantira preciznost delaya i može ovisiti o mnogim parametrima (zauzeću CPU-a, je li tab u backgroundu...).

Funkcije koje `setTimeout` poziva će imati postavljen `this = window`, pa pripazi ako pozivaš funkcije iz objekata `setTimeout(user.say)`. Pozovi s wrapper funkcijom ili s `setTimeout(user.say.bind(user), 300)`

## Nullish operator

Za defaultanje vrijednosti možeš koristiti `fullName || userName || "Guest"`. Ovo će preskočiti svaku falsey vrijednost, uključujući i `0` i `""`.

Ako želiš da preskoči samo `null` i `undefined`, koristi `fullName ?? userName ?? "Guest"`.

## Optional chaining

`user?.name` je kao `user && user.name`.
`user?.[key]` ako je key u varijabli.
`user.method?.()` ako nisi siguran postoji li metoda.

## Try Catch

`try {} catch(err) {}` će u slučaju errora izvršiti catch block. `err` je objekt s propertijima `message`, `name` i `stack` (nije standard, ali svi ga podržavaju).

Errori koji nisu uhvaćeni će završiti u globalnom handleru `window.onerror`.

## asm.js

`emscripten` je alat koji kompajlira C/C++ kod u `asm.js`, podskup javascripta napravljen da se izvodi jako brzo. Neke od optimizacija su: koristi samo brojeve (nema stringova, booleana i objekata); svi podatci drže se u jednom velikom arrayu, heapu (nema globalnih varijabli, closurea i data structurea). `asm.js` se može izvršavati na svakom browseru, ali određeni browseri su toliko optimizirani da ga izvršavaju tek 2x sporije od nativnog C/C++ koda.

# Literatura

* https://javascript.info/
* https://medium.com/the-node-js-collection/modern-javascript-explained-for-dinosaurs-f695e9747b70
