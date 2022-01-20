# Funkcijsko programiranje

Funkcijsko programiranje može značiti dvije stvari:
1) Izbjegavanje mutable stanja
2) Korištenje first class funkcija (tj. funkcije se mogu koristiti kao value)

## Lexical Scope

Vrijednost funkcije sadrži:  **kod** koji funkcija izvršava i **environment** u trenutku kad je funkcija definirana. Ovaj par `(code, env)` se naziva **closure**.

Pozivanje funkcije izvršava `code` koristeći `env` i predane `argumente`. **Lexical scope** znači da funkcija koristi kontekst iz mjesta gdje je definirana, a ne gdje se poziva.

Suprotnost lexical scopeu je dynamic scope, ali se vrlo rijetko koristi jer ne podržava modularnost. Konceputalno blizak mu je exception handling: `raise` ne mora biti sintaksno unutar `handle` expressiona, nego dinamički (tj. u call stacku).

Kod jezika koji ne podržavaju closure (npr. `C`), može ih se emulirati kao funkcije koje primaju dodatan `env` argument.

## Argumenti

Neki jezici (npr. `ML`) implementiraju argumente tako da svaka funkcija prima samo jedan argument, pa je poziv `f(a, b)` s `f(1, 2)` zapravo pattern matching na tuple `(a, b) = (1, 2)`.

Nekim jezicima (npr. `Haskell`) je tuple argumenata samo syntax sugar za currying, pa su sve funkcije interno oblika `fun sum = fn x => fn y => fn z => x + y + z`. Drugi jezici (npr. `ML`) imaju zasebne implementacije za curried i za tuple argumente.

## Kompozicija

`fun sqrt_of_abs = Math.sqrt o Real.fromInt o abs` neki jezici imaju operator za kompoziciju funkcija (čita se s desna na lijevo)

`fun sqrt_of_abs i = i |> abs |> Real.fromInt |> Math.sqrt` pipeline operator se lakše čita jer opisuje smjer kretanja podataka.

## Eager vs Lazy Evaluation

*Eager evaluation* je karakteristika jezika da se argumenti funkcije evaluiraju prije poziva funkcije, čak i ako se neće koristiti u funkciji. Za usporedbu, expressioni u `if` izrazu se evaluiraju samo po potrebi.

*Lazy evaluation* postoji u nekim jezicima (npr. `Haskell`), gdje se argumenti evauliraju tek kad se koriste.

U ostalim jezicima lazy evaluation se može emuirati pomoću lambde bez argumenta (*thunk*): umjesto `exp()`, funkciji se preda `lambda { exp() }`, a ona će je evaluirati po potrebi. Emulacija bi se također trebala pobrinuti da se rezultat thunka cachira, kako bi se izbjegla višestruka evaluacija (nešto kao *promise*).

## Memoization

Memoizacija je cachiranje rezultata funkcije, uzimajući u obzir argumente.

Memoizacija se isplati ako je održavanje cachea jeftinije nego ponovo računanje, i ako će se cachirani rezultati opet iskoristiti.

Memoizacije je posebno bitna za rekurzivne programe (npr. fibonacci) čije se vrijeme izračuna povećava eksponencijalno bez cachiranja. Ova tehnika naziva se i *dynamic programming*.

## Tail Recursion

Imamo standardnu rekurziju za računanje faktorijela:
`fact(n) { n > 1 ? n * fact(n-1) : 1 }`

Svaki rekurzivni poziv mora pričekati da se sljedeći dovrši kako bi izračunao `n * fact(n-1)`. Pritom svaki poziv dodaje svoj kontekst na callstack, i za dovoljno velik `n` dogodit će se stack overflow.

Ovo se može izbjeći korištenjem tail rekurzije:
`fact(n, a) { n > 1 ? fact(n-1, a*n) : 1}`.

Ključna razlika je što tail rekurzija za svoj posljednu naredbu vraća `fact(n-1, a*n)`, bez ikakvih dodatnih izračuna. To znači da se stack trenutne funkcije `f(n, a)` može jednostavno zamijeniti s `f(n-1, a*n)`, pa stack nikad ne raste. Ovo se zove *tail call optimization* i većina programskih jezika je implementira.

Funkcije napisane u tail recursion obliku najčešće koriste *accumulate* argument u kojem se prenosi izračun, pa su kompliciranije od običnih. Svaka funkcija se može napisati u ovom obliku, ali to ne znači da je ovaj oblik uvijek optimalniji - npr. kretanje po stablu je mnogo bolje napraviti klasičnim stilom.

## Quine

Quine je program koji, kada se pokrene, ispisuje svoj source. Ograničenja su da ne smije koristiti I/O (tj. čitati vlastiti file) i ne smije biti prazan.

Quine se uobičajeno sastoji od dva dijela: data koji sadrži code, i code koji ispisuje data. Format je:
```
data = "..."
puts "data = " + data.inspect + data
```
Data se složi da sadrži kod ispod prve linije: `"\nputs \"data = \" + data.inspect + data"`

