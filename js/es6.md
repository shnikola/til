# ES6

## Variables _IE 11+_
Umjesto `var x` koja je globalna koristi:
* `let x` za promjenjive vrijednosti, lexically scoped
* `const x` za vrijednosti koje jednom postaviš, lexically scoped

## String templates
Koristi backtikove za interpolaciju unutar stringa: ``My name is ${name}``.

## Destructuring
Postavljanje varijabli iz arraya ili objekta:
* `let [a, b, c] = [1, 2, 3]`
* `let {a, b, c} = {a: 1, b: 2, c: 3}`
* `let [head, ...tail] = [1, 2, 3]` stavlja ostatak u `tail`

## Arrow Functions
Shorthand način za pisanje funkcija:
* `n => n + 1` umjesto `function(n) { return n + 1; }`
* korisno za iteratore, npr. `array.filter(n => n > 0)`
* ne stavaraju novi function scope, što znači da ne moraš koristi `bind(this)` ili `var that = this`.

## Function Parameters
* `function sum(a = 0, b = 0)` za defaultne vrijednosti.
* `function sum(...vals)` za proizvoljan broj parametara koji su dostupni kao array.
* `function sum({a = 1, b = 2} = {})` za named parametre.

## Classes
Klase su syntax sugar oko prototype modela: `class Cat { ... }`
* `constructor(x, y) { ... }` za inicijalizaciju s `new Cat(1, 2)`.
* `meow() { ... }` za definiranje public metode.
* `get name()` i `set name()` definiraju custom getter i setter za `cat.name`
* `static find()` za class metodu.
* `class Tiger extends Cat` za inheritance.
* `super.meow()` za dohvaćanje parent objekta.

## Data Structures
* `new Map([["a", 1], ["b", 2]])` pravi hash table.
* `new Set([1, 2, 2, 3, 3, 4, 5])` drži samo unique elemente.

## Modules
Svaki file je zaseban module, a dijele feature preko `export` i `import`
* `export function sum() { ... }` i `export var pi = 3.14` u `lib/math.js`
* `import * as math from 'lib/math'` dopušta korištenje `math.sum` i `math.pi`
* `import {sum, pi} from 'lib/math'` dopušta korištenje `sum` i `pi`
Moduli se čak mogu dinamički loadati sa `System.import('lib/math').then(...)`


# Literatura
* https://es6cheatsheet.com/#cheatsheet usporedba sa starim kodom.
