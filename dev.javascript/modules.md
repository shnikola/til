# Modules

Najčešće ne želiš da varijable koje koristiš u jednoj funkciji budu vidljive izvan te funkcije. Zato javascript ima scopeove koji ograničavaju vidljivost varijabli i funkcija.

Ako želiš varijablu učiniti vidljivu dvjema funkcijama, moraš je definirati u scopeu iznad obje funkcije, npr. u globalnom scopeu. Tako radi jQuery - ako želiš koristiti jQuery plugine, moraš se pobrinuti da se jQuery učita prvi i doda `$` varijablu u globalni scope.

Ovaj pristup nije idealan. Prvi problem je što su međuovisnosti komponenata implicitne, pa je lako skršiti sve slučajnim promjenom redoslijeda skripti. Drugi problem je što je globalna varijabla dostupna svima, pa je može mijenjati i maliciozni kod.

Prije nego što je JS dobio podršku za module, koristilo se nekoliko sistema.

## AMD

AMD dozvoljava asinkrono loadanje modula.

Modul se definira s
```
define('math', ['dependecy'], function (dependency}) {
  return { sum: sum };
})
```

Modul importaš s `const math = require('math')`.

## CommonJS

CommonJS sistem modula se koristio za servere (npr. Node). Funkcije iz `math.js` koje želiš učiniti dostupnim drugim fileovima definiraš s
`exports = { sum: sum }` ili `exports.area = (r) => r * r * 3.14`.

Modul importaš s `const math = require("./math.js")` ili `const sum = require("./math.js").sum`. Kada interpreter dođe do `require` metode, sinkrono učita taj file i izvrši ga.

Prilikom exporta objekt i njegove vrijednosti se kopiraju. Ako se exporting modul kasnije promijeni tu vrijednost, moduli koji su ga importali neće vidjeti promjenu.

## ES6 Moduli

U ES6, svaki file je zaseban module, a dijele feature preko `export` i `import`

Modul `lib/math.js` se exporta s `export function sum() { ... }` i `export var pi = 3.14`.

Module importaš s
* `import * as math from 'lib/math'` dopušta korištenje `math.sum` i `math.pi`
* `import {sum, pi} from 'lib/math'` dopušta korištenje `sum` i `pi`

ES moduli koriste live bindings, pa se exportane vrijednosti shareaju i mijenjaju u svim modulima koji ih importaju.

Postoji podrška i za asyncroni load: `System.import('lib/math').then(...)`
