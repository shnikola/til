# Objects

`{a: 1, b: 2}` je uobičajen način za stvaranje objekta pomoću literala.
`{[a]: 1}` za computed property, key je vrijednost varijable `a`.

`{a, b}` je skraćeni način za napisati `{ a: a, b: b }`. Oba načina se mogu kombinirati: `{ a, b: 30}`.
`cart = { add() {...} }` vrijedi i za dodavanje funkcija.

## Keys

Key objekta može biti string ili symbol. Svi ostali tipovi će se pretvoriti u string. Tako je `obj[0]` isto što i `obj["0"]`.

`"a" in obj` provjerava postoji li key u objektu, čak i ako je postavljen na `undefined`. Rijetko se koristi, najčešće je `obj.a` dovoljno.

`for (let key in obj)` iterira kroz sve keyeve objekta. Ordering je poprilično blesav: ako se key može pretvoriti u broj, onda ih sortira kao brojeve, a ostali idu redom kojim su dodavani u objekt.

`Object.keys(obj)` vraća sve keyeve objekta kao array.
`Object.entries(obj)` vraća key value parove u obliku `[['a', 1], ['b', 3]]`.

## Properties

`Object.defineProperty(obj, "a", { value: 1, writable: false}` pretvara property u read-only.
`Object.defineProperty(obj, "a", {enumerable: false}` će isključiti property iz `for..in` enumeracije.

## Cloning

Za shallow copy objekata koristi `clone = Object.assign({}, user)` ili skraćeno `clone = {...user}`.

Za deep copy najjednostavniji je `JSON.parse(JSON.stringify(user))`.

## Symbols

`id = Symbol()` će generirati unikatan objekt kojeg možemo koristiti kao key. `obj[id]` će biti dostupan samo kodu koji ima pristup varijabli `id`.

Čak i iteracijom `for key in obj`, symbol keyevi će ostati nevidljivi. Symboli su tako način za dodavanje propertija u objekt koje nitko drugi ne može čitati ni mijenjati. (Tehnički, može im se pristupiti koristeći `Object.getOwnPropertySymbols(obj)`, ali to nitko ne koristi).

Također postoje globalno definirani symboli poput `Symbol.toPrimitive` i `Symbol.iterator` pomoću kojih možeš konfigurirati ponašanje objekta.

## Comparison

`obj1 == obj2` je true samo ako su reference na isti objekt. Nativna usporedba po vrijednostima ne postoji.

## Conversion

Prilikom operacija nad objektima poput `obj - 5`, `obj1 + obj2`, ili `alert(obj)`, objekti će se **konvertirati u primitivni tip**. Javascript nažalost ne dopušta overload operatora za objekte, nego sve pretvara u broj ili string.

U `boolean` kontekstu (npr. `if (obj)`), svi objekti se konvertiraju u `true`, čak i `{}`.

Za definiranje konverzije u `string` (`alert(obj)`), `number` (`obj1 > obj2`) ili `default` (može biti string i number), koristi metodu
`obj[Symbol.toPrimitive] = function(hint) {...}`, ili unutar literala
`{ [Symbol.toPrimitive](hint) {...} }`. Hint ima vrijednost `string`, `number` ili `default`.

Ako `Symbol.toPrimitive` nije definiran, fallbacka se na `toString()` ili `valueOf()`. U praksi, dovoljno je definirati `toString()` jer vjerojatno nećeš izvršavati operacije nad objektom.

## Constructor functions

Konstruktori su funkcije poput `function User(name) { this.name = name }`. One su alternativan način za stvaranje objekta koristeći `new User("Nikola")`. Konvencija je da se pišu velikim početnim slovom, iako nije obavezno.

`user = new User("Nikola")` interno postavlja `this` na prazan objekt, izvršava tijelo funkcije, i vraća `this`. Nemoj stavljati `return` u tijelo konstruktora.

`user = new function() { ... }` je anonimni konstruktor koji je zgodno koristiti ako setup objekta ima kompleksniju logiku.

## Prototype

Svaki objekt ima skriveni property `[[Prototype]]` koji je jednak drugom objektu ili `null`.

Kada pokušaš čitati property `obj.a` koji ne postoji na `obj`, js će ga potražiti i u `obj.__proto__.a`. Ako je u pitanju funkcija, `this` će i dalje referncirati `obj`, pa će nasljeđene metode raditi s trenutnim objektom.

Prototype se koristi samo za čitanje propertija. Za pisanje i brisanje prototype se ignorira.

Prototype možeš dodati na objekt s `obj.__proto__ = parent`, ili još bolje na konstruktor funkciju `User.prototype = parent`.

Svi built-in objekti imaju metode zapisane u prototypeu (`Array.prototype`, `Object.prototype`, `Date.prototype`), dok objekti samo sadrže podatke.

## Arrays

Array ima `array.length` property koji je iz nekog razloga writeable. `array.length = 2` će truncatati array na prva dva elementa.

Za usporedbu arraya opet ne možeš koristiti `==` nego moraš ručno iterirati i provjeravati.

`for (let e of array)` se koristi za iteriranje kroz elemente arraya. Štoviše, možeš učiniti svaki objekt iterabilnim ako implementiraš `obj[Symbol.iterator]`metodu. Nemoj koristiti `for..in` jer on samo ide kroz keyeve, a iterator možda implementira poseban redoslijed.

Arrayi od ES6 imaju ugrađene metode za pretraživanje i transformiranje. Na bazi `array.forEach`, funkcija uvijek dobija argumente redom: item, index, list.

Funkcije su `find`, `findIndex`, `every`, `some`, `filter`, `map`, `sort`, `reduce`. Najbolje ih je koristiti u kombinaciji s arrow funkcijama, npr. `array.every(x => x > 0)`.

Postoje objekti koji su *array-like*, imaju `length` i indeksirane propertije, ali nemaju ugrađene gornje funkcije ni iterator. Možeš ih pretvoriti u array koristeći `Array.from(obj)`

`...array` je spread operator za rastavljanje arraya na argumente, kao ruby splash (`*`) operator, npr. `[...arr, 1, 2, 3]`.

## Map

`map = new Map([1, 'a'], [4, 'b'])` stvara hash map koji dopušta **keyeve bilo kakvog tipa** (čak i objekte). Za usporedbu, objekt može imati samo string keyeve. Map također ima zgodne metode poput `map.size` i `map.forEach` za iteraciju.

Za čitanje i pisanje vrijednosti koristi `map.get(k)` i `map.set(k, v)`. Nemoj koristiti `map[k]`, jer će postaviti property na objektu i pretvoriti key u string.

## Set

`set = new Set([1, 2, 2, 3, 3, 4, 5])` stvara kolekciju koja sadrži samo unique elemente.

Za čitanje i pisanje koristi `set.has(v)`, `set.add(v)` i `set.delete(v)`.

## Destructuring

`let [a, b, c] = [1, 2, 3]` postavlja varijable iz arraya ili nekog drugo iterabla.
`let [head, ...tail] = [1, 2, 3]` stavlja ostatak u `tail`.

`[a, b] = [b, a]` zamjenjuje vrijednosti varijablama.

`let {a, b, c} = obj` postavlja varijable iz objekta `{a: 1, b: 2, c: 3}`.

Ovo je popularno u dohvaćanju dijelova iz modula, npr.
`const { component } = Redux` radi `component = Redux.component`.

## Date

`new Date()` vraća trenutno vrijeme kao objekt. Date objekt ima metode za dohvaćanje dijelova datuma: `getFullYear` (nemoj koristiti `getYear`), `getMonth` (pazi jer glupo vraća 0-11), `getDate`, `getHours`, `getMinutes`, `getSeconds`.

`new Date("2017-01-26")` parsira ako mu je zadan string, ili `new Date(1612806618000)` ako su mu zadane milisekunde.

`Date.now()` vraća timestamp u milisekundama.

## Regex

Metode za provjeru sadržaja stringa: `str.startsWith("abc")`, `str.endsWith("abc")`, `str.includes('abc')`.

Za matchanje koristi `str.match(/a/g)`. Nemoj koristiti `re.test('ab')` jer je regex objekt iz nekog bleasavog razloga stateful.


