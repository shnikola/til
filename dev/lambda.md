# Lambda Calculus

Lambda calculus je mali ali moćan framework za definiranje funkcija. Sve što je izračunljivo se može definirati pomoću funkcije, pa time i izraziti lambda notacijom.

Općenito, calculus je bilo kakav sistem računanja, tj. manipuliranja simbolima. Diferencijalni calculus se bavi računanjem jako malih promjena, integralni calculus se bavi akumulacijom vrijednosti, a lambda calculus se bavi definicijom i apliciranjem funkcija.

## Povijest

Nakon što je Godel rasturio matematiku, Alonso Church pokušava složiti sustav koji je barem može računati stvari koje su donekle izračunljive. `1932` razvija lambda calculus kao notaciju za zapisivanje funkcija. To je sićušan jezik od svega nekoliko linija, ali uskoro shvaća da se može iskoristiti za dobijanje mnogo kompleksnijih rezultata.

`1936` Church pomoću lambda notacije rješava slavni neriješeni Hilbertov *decision problem* - dokazuje da ne postoji metoda koja će odrediti da li zadani problem ima rješenje. Samo 2 mjeseca kasnije, Alan Turing rješava isti problem pomoću Turingovog stroja. Nije oduševljen kad sazna da ga je Church za dlaku pretekao, ali čita njego rad i uviđa da su **lambda calculus i Turingov stroj ekvivalentni**, samo zapisani na različit način. Odlazi u Princeton i doktorira pod Churchovim mentorstvom.

## Notacija

`λa.a+1` je zapis anonimnog lambda izraza.
`λ` (*function signifier*) znači da započinjemo definiciju.
`a.` (*parameter variable*) je argument kojeg funkcija prima.
`a+1` (*return expression*) je tijelo funkcije.

U lambda calculusu sve su funkcije **unarne**.
`λa.λb.a+b` (tj. `λa.(λb.a+b)`) je currying, način da napraviš funkciju koja prima više parametara. To je lambda izraz koji prima parametar `a` i vraća funkciju `λb.a+b`.
`λab.a+b` je skraćeni način zapisivanja curry funkcija, ali i dalje je bitno da inputi dolaze jedan po jedan, a ne istovremeno.

`f a` (*application*) je poziv funkcije `f` s parametrom `a`. Malo je čudan zapis ako si se naviknuo na oblik `f(a)`, ali nije strašno.

`f a b` je zapravo `(f a) b`, jer je apliciranje lijevo asocijativno.
`f (a b)` znači da se prvo aplicira `a b`, a onda njihov rezultat na `f`.

`λa.b x` je zapravo `λa.(b x)`, jer se body greedy evaulira.
`(λa.b) x` stvara anonimnu funkciju i poziva je s inputom `x`.

## Beta reduction

Proces apliciranja funkcije se naziva beta-redukcija. Zapravo je prilično jednostavno, u tijelo funkcije zapišeš input umjesto parametra. Npr:

`λb.λc.b 2 3 = (λb.λc.b 2) 3 = λc.2 3 = 2`.

Do kraja aplicirana funkcija se naziva **beta-normal form**. Postoje lambda izrazi koji nemaju beta normalnu formu, nego se razvijaju zauvijek (npr. neograničena rekurzija).

## Kombinatori

Kombinator je funkcija bez slobodnih varijabli, tj. funkcija koja u tijelu koristi samo varijable iz argumenata.

Simboli za osnovne kombinatore je Haskell Curry preuzeo od Schonfinkelovih njemačkih naziva, a Raymond Smullyan im je dodijelio ptičja imena.

`I := λx.x` (*identity*) vraća input.

`M := λf.ff` (*mockingbird*), prima funkciju i aplicira je sa samom sobom.
`MI = I` jer `I` apliciran sa samim sobom vraća `I`.
`MM` nema normalnu formu, jer se razvija zauvijek. Naziva se i `Ω`.

`K := λab.a` (*kestrel*) prima dva inputa i vraća prvi. Naziva se još i `const` jer `K5` stvara funkciju koja uvijek vraća konstantu `5`.
`KI = λab.b` (*kite*) prima dva inputa i vraća drugi.

`C := λfab.fba` (*cardinal*) prima funkciju i dva parametra, te je poziva obrnutim redoslijedom.
`CK = KI` jer `CKab = Kba = b`, tj. uvijek vraća drugi parametar.

`B := λfga.f(ga)` (*bluebird*) radi kompoziciju unarnih funkcija `f` i `g`, aplicirajući zdesna nalijevo.

## Boolean algebra

Sve može biti izraženo preko funkcija, pa tako i boolean vrijednosti. Boolean primitive true i false možemo enkodirati kao funkciju sličnu ternarom operatoru `bool ? p : q`, tj. `bool p q`.

`TRUE := λpq.p` true vraća prvi argument. To je zapravo `K`.
`FALSE := λpq.q` false vraća drugi argument. To je `KI`.

`NOT := λp.p F T`. Ako je b `T` vratit će `F` i obrnuto. To je zapravo `C`.
`AND := λpq.pqp` (ili duže, `λpq.p q F`)
`OR  := λpq.ppq` (ili duže, `λpq.p T q`)

## Church numerals

Lambda calculusom se mogu izraziti i prirodni brojevi. Svaki broj je funkcija koja prima funkciju i argument. Kvantitet se odražava brojem apliciranja funkcije.

`N0 := λfa.a` ne aplicira funkciju nijednom. `N0 = FALSE`, što je isto zgodno.
`N1 := λfa.fa` aplicira funkciju jednom.
`N2 := λfa.f(fa)` aplicira funkciju dvaput, itd.

`SUCC := λnfa.f(nfa)`, pa je `SUCC N2 = λfa.f(N2 fa) = N3`.
`SUCC = λnf.Bf(nf)`, koristeći `B`

`ADD := λnk.n SUCC k`, jer je `ADD N2 N3 = (SUCC (SUCC N3)) = N2 SUCC N3`.

`MUL := λnkf.n(kf)`, jer je `MULT N2 N3 f a = (f (f (f (f (f (f a)))) = ((f ∘ f ∘ f) ∘ (f ∘ f ∘ f)) a = N2 (N3 f) a`, tj. 2 puta apliciraj po 3 puta apliciranu funkciju. `MULT` je zapravo `B`.

`POW := λaf.fa`, jer je `POW N2 N3 = N2 x N2 x N2 = N2 ∘ N2 ∘ N2 = N3 N2`, tj. 3 puta primjeni N2 funkciju.

`ISZERO := λn.n KF T` (`KF` je funkcija koja uvijek vraća `F`). Ako je `n = N0`, neće se aplicirati, i vratit će se `T`, u svim ostalim slučajevima vratit će se `F`.

## Data structures

`PAIR := λabf.fab` je najmanja struktura podataka, closure. `PAIR ab` čuva argumente dok ne apliciraš treći argument `f`.

`FST := λp.pK` vraća prvi element takvog para.
`SND := λp.pKI` vraća drugi element.

## Y Combinator

Lambda calculus nema petlje ni rekurziju. Y kombinator omogućuje rekurziju u jezicima koji je ne podržavaju. Prima ne-rekurzivnu funkciju i vraća istu funkciju koja će se izvršavati rekurzivno.

`Y = λf.(λx.f(xx))(λx.f(xx))`.

# Literatura

* https://www.youtube.com/watch?v=3VQ382QG-y4
