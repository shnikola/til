# Natjecanja

## Algebra

### Diofantske jednadžbe

Cilj je naći **cjelobrojna** riješenja jednadžbe s dvije nepoznanice. najčešća metoda je prebaciti jednu nepoznanicu na suprotnu stranu i pokušati da završi u nazivniku.

Za naći `xy - 2y = 7x - 5` prebaci `x` na desnu stranu: `y = 7(x-5)/(x-2)`, Razlomak se sredi da je `x` samo u nazivniku: `y = 7 + 9/(x-2)`. Riješenja su oni `x` koji dijele brojnik: `-7, -1, 1, 3, 5, 11`.

Ako ne možeš prebaciti jednu nepoznanicu u nazivnik, probaj faktorizirati izraz s lijeve strane, a s desne strane faktoriziraj broj.

Za naći `x² - y² = 91` raspiši `(x-y)(x+y) = 7*13`. Očito je `x > y`, pa imaš slučajeve `x-y` može biti `1, 7, 13, 91`.

U slučaju da imaš složeni izraz tipa `x² + 3x + 24 - y² = 0`, opet ga se može izmasirati do razlike kvadrata: `4y² - (2x+3)² = 87`

Za naći `x² + y² = 91` raspiši sve kvadrate manje od 91, pa vidi čiji zbroj odgovara. Nije baš neka pametna metoda ali ajde.

Za naći `√(x) + √(y) = √(48)`, prebaci `x` na desnu stranu, pa kvadriraj: `y = 48 - √(48x) + x`, pa vidi kada je `48x` kvadrat za `x <= 48`.

### Apsolutne vrijednosti ||

`|komplicirani razlomak| < 1` napiši kao presjek dvije nejednadžbe, `r > -1` i `r < 1`

`|a| - a >= 0`, jer broj nije veći od svoje apsolutne vrijednosti (`|a| >= a`).
`|a|+|b| >= |a+b|`, poznata kao i nejednakost trokuta.

`log(x²)` je `2*log(|x|)`. Pripazi da dodaš apsolutnu vrijednost ako izbacuješ parni eksponent.

### Floor ⌊⌋

`⌊x⌋ = x - x'`, gdje je `x' < 1` (decimalni dio broja `x`)
`⌊2x⌋ = ⌊2⌊x⌋ + 2x'⌋ = 2⌊x⌋ + ⌊2x'⌋`x
`⌊x+y⌋ = ⌊x⌋ + ⌊y⌋ + ⌊x' + y'⌋`

### Jednadžbe

Prvo provjeri jesu li možda svi pribrojnici veći od 0, npr.
`(x+y)^2 + √(x-y) = 0`

Ako imaš faktoriziranu stranu sastavljenu od dvaju sličnih izraza, npr.
`(2x+3)²(x+1)(x+2) = 6`, napravi supstituciju kao sredinu izraza:
`t = x + 3/2`, pa će biti `t²(t-1/2)(t+1/2) = 6`

Ako imaš polinom kojeg nikako ne možeš rastaviti, npr.
`x⁴ - x³ - 13x² + 2x + 4 = 0`, probaj podijeliti sve s `x²` i supstituciju
`t = x - 2/x`.

Ako imaš funkciju koja radi s recipročnim vrijednostima, probaj uvrstiti `(x+1)/x = 1 + 1/x`, tj `x/(x+1) = 1 - 1/(x+1)`

Ako je zadana jednadžba višeg stupnja s parametrom koji se kvadrira, npr. `x⁴ - 2ax² + x + a² -a = 0`, riješi jednadžbu po `a`, i pomoću riješenja možeš faktorizirati izraz, `(a-a₁)(a-a₂)`.

### Sustavi jednadžbi

Ako imaš **sustav od n jednadžbi**:
- probaj ih sve zbrojiti/pomnožiti, pa rezultat toga uvrsti u jednu jednadžbu.
- ako se sve ponište kad ih zbrojiš, probaj naći trivijalno rješenje (npr. `x1 = x2 = x3 = ...`) i dokazati da je jedino (pretpostavi da je `x1 > x2` itd.)

### Nejednakosti

Nejednakosti smiješ kvadrirati samo ako su obje strane pozitivne. Ako su obje negativne, mijenja se smjer nejednakosti. Ako ne znaš, možeš samo prebaciti sve na jednu stranu.

Za **nenegativne brojeve** vrijedi **nejednakost sredina** `K >= A >= G >= H`, gdje je:
- `K = √((a² + b² + c²)/3)`
- `A = (a + b + c)/3`
- `G = ³√(abc)`
- `H = 3/(1/a + 1/b + 1/c)`

Jednakost vrijedi samo ako su svi elementi jednaki.

Iz `A >= G` slijede transformacije:
- `(a+b)² >= 4ab` kad imaš kvadrat svega.
- `a² + b² >= 2ab` kad imaš kvadrat pojedinih.
- `a² + b² + c² >= ab + bc + ac` iz `(a-b)² + (b-c)² + (c-a)² >= 0`.
- `a²/bc + bc >= 2a` kad imaš isti izraz u nazivniku i brojniku.

Izraze možeš pretvoriti u konstante:
- `a/b + b/a >= 2`
- `√(ab)/(a+b) <= 1/2`
- `(a²-ab+b²)/(a²+ab+b²) >= 1/3` (ako imaš kubove)

Za sve **realne brojeve** vrijedi **Cauchy-Schwarz**:
`(a₁² + a₂² + ... an²)*(b₁² + b₂² + ... bn²) >= (a₁b₁ + a₂b₂ + ... anbn)²`.

Jednakost vrijedi ako je `aₓ = kbₓ`.

Iz CS slijede:
- `(a₁² + a₂² + ... an²) >= (a₁ + a₂ + ... an)²/n` (uvrsti se `bₓ = 1`), kad imaš zadan `a₁ + a₂ + ... an` ili `a₁² + a₂² + ... an²`.
- `a₁²/b₁ + a₂²/b₂ + ... + an²/bn >= (a₁ + a₂ + ... an)²/(b₁ + b₂ + ... bn)` (**Tituova lema**), kad imaš razlomke kojima su brojnici kvadrati s bilo kakvim nazivnicima.
- ako su članovi pozitivni brojevi, ne moraju biti kvadrati, npr: `(a + b + c)*(1/a + 1/b + 1/c) >= (1+1+1)²`

Nejednakosti možeš koristiti i za stvaranje dodatnih uvjeta u jednadžbama:
- `a³ + b³ + 1 = 3ab` iskoristi da je `a³ + b³ + 1 >= 3ab` (AG), pa jednakost znači da su svi jednaki `a = b = 1`.

### Rastavljanja

Ako se spominje **suma dvaju kvadrata**, probaj primjeniti Diofantovu jednakost: ako su dva cijela broja zbrojevi 2 kvadrata, onda je i njihov umnožak zbroj dvaju kvadrata, tj: `(a² + b²)(c² + d²) = (ac + bd)² + (ad - bc)²`

`(a² + b²)/2` se može napisati kao zbroj kvadrata `((a+b)/2)² + ((a-b)/2)²`.

Ako je zadan `ab+bc+ac`:
- `a² + b² + c² + 2(ab + bc + ac) = (a+b+c)²`
- `a² + b² + c² - (ab + bc + ac) = 1/2((a-b)² + (b-c)² + (c-a)²)`

Ako je zadan `ab/c + bc/a + ac/b`:
- kvadriraj cijeli izraz i binomni koeficijenti će se pokratiti.

Razlomke oblika `2/(a+b-c)`, podijeli na 2 `1/(a+b-c)` i regrupiraj.

## Kompleksni brojevi

Vjerojatno ćeš nešto petljati s apsolutnim vrijednostima i konjugacijom. Ako imaš zadane vrijednosti, probaj izračunati apsolutnu vrijednost koristeći `z*z̄ = |z|²`

Ako je `|z| = 1`, to znači da je `z = 1/z̄`. Usto, možeš množiti sve s `z*z̄`.

Ako nemaš zadane brojeve, i želiš se riješiti apsolutne vrijednosti, koristi `|z|^2 = z*z̄`.

Konjugacija je super jer se distribuira po zbrajanju, množenju i djeljenju. Ako ti smeta, možeš je se riješiti pod apsolutnom vrijednosti:
`|z̄| = |z|`, čak i `|z̄ + w̄| = |z + w|`.

Ako imaš potenciju, izbaci je van: `|z²| = |z|²`.

Polinom `P(z)` s korijenima `a, b, c, ...` se može napisati kao `P(z) = (z-a)(z-b)(z-c)`.

U raspisanom obliku to je `z³ + z²(z₁ + z₂ + z₃) + z(z₁z₂ + z₂z₃ + z₁z₃) + z₁z₂z₃`, što je korisno ako imaš zadano jedan od tih koeficijenata.

Ako imaš zadano jedno rješenje polinoma, podijeli polinom s `z-z₁` da si olakšaš traženje ostalih.

## Teorija brojeva

### Znamenke

Ako su ti zadane **rotacije znamenaka** `abc`, `bca`, itd:
- probaj `10*abc - bca = 999a` (`999 = 3³*37`)

Broj `A` ima `log(A) + c` znamenaka, gdje je `0 < c <= 1` (jednakost je samo ako je `A = 10ⁿ`).

Broj `111...1` s `n` znamenki se može napisati kao `(10ⁿ - 1)/9`

### Djeljivost

Za dokazati da je izraz **paran ili neparan**:
- `a-b` i `a+b` su iste parnosti.
- ako je `(a-1)(a+1)` parni, onda je jedan od njih djeljiv samo s `2¹`, a drugi većom potencijom `2`.

Za dokazati da izraz (npr. `n² - n + 2`) **nije djeljiv** s npr. 5:
- uvrsti svaki od 5 mogućih `n`: `5k`, `5k+1`, `5k+2`, `5k+3`, `5k+4`. Time si pokrio sve prirodne brojeve.

Za djeljivost **prostih brojeva**:
- svi prosti brojevi osim 2 i 3 su oblika `6k-1` ili `6k+1`

Za djeljivost **kvadrata**:
- kvadrati su oblika `3k` ili `3k+1` za djeljivost s 3.
- kvadrati su oblika `4k` ili `8k+1` za djeljivost s 4.

Za pronaći **zajednički djeljitelj brojeva** brojeva `a` i `b` (ili, češće, dokazati da im je `nzd(a, b) = 1`):
- `a+1` i `a` nemaju zajednički djelitelj.
- `nzd(a, b) = nzd(a, b - ka)`
- ako je `p` prost broj, `nzd(a, p)` je ili `p` ili `1`.

Broj `n = p₁^a * p₂^b * ...` ima `(a+1)(b+1)...` djelitelja: eksponent na `p₁` može biti `0, 1, ..., a`, itd. To je ujedno i broj načina na koji se može rastaviti na 2 faktora.

Složeni brojevi manji od `100` imaju proste faktore manje od `√(100) < 10`: `2, 3, 5, 7`.

Dokaži da ako je `u² + uv + v²` djeljiv s 9, da su i `u` i `v` djeljivi s 3.
Trik je raspisati izraz tako da je jedan od pribrojnika djeljiv s nečim, npr. `(u-v)² - 3uv = 9k`, što znači da je i `(u-v)²` djeljiv s 3, i onda se nastavlja po tome.

Nađi najmanji `k` tako je da je zbroj kvadrata bilo kojih `k` uzastopnih brojeva djeljiv s 15.
Prvo gledamo djeljivost s 5 tako da napišemo uzastopne brojeve kao `(5l)²`, `(5l+1)²`, do `(5l+4)²`. Ispadne da će zbroj `5` uzastopnih kvadrata uvijek biti djeljivo s 5. Isto tako napišemo za 3: `(3m)²`, `(3m+1)²`, `(3m+2)²`; ispadne da će zbroj `9` uzastopnih kvadrata biti djeljivo s 3. Najmanji `k` je višekratnik ta dva uvjeta, 45.

## Kombinatorika

Šahovski turnir (1 bod za pobjedu, 0.5 za izjednačenje, 0 za poraz), traži se broj igrača s nekim zadanim uvjetom (npr. svaki igrač je dobio pola bodova u partijama za zadnjom trojicom u krajnjem poretku). Ključno je primjetiti da je broj podijeljenih bodova jednak broju odigranih partija.

20 ljudi šalje po 10 poruka ostalima, koliko će biti obostranih poruka? Ukupno poruka je `20*10 = 200`, a ukupno "kanala" `20*19/2 = 190`. To znači da će 10 poruka morati ići istim kanalima, tj. biti obostrane.

Koliko ima uređenih parova cijelih brojeva (m,n) za koje vrijedi `|m| + |n| < 2004`? Podijeli na slučajeve - prvo izbroj samo za prirodne brojeve, pa za negativne, i na kraju dodaj ako je jedan od njih 0.

3 vodoravna pravca sjeku se s 7 okomitih pravaca u 21 točki, crvene ili plave boje. Dokaži da postoji pravokutnik svih vrhova iste boje.
Recimo da crvenih ima više, barem 11. Zatim podijeli na slučaj kada postoji stupac s 3 crvene (mora biti još jedan s 2 crvene), i kad ne postoji (moraju biti dva ista rasporeda s 2 crvene).

U kvadrat stranice 10 ucrtavamo 100 točaka. Dokaži da postoji trokut kojima su vrhovi točke (ili vrhovi kvadrata) čija je površina manja od 1/2.
Prva točka koju ucrtamo dijeli kvadrat na 4 trokuta. Svaka iduća je unutar nekog trokuta, i dijeli ga na 3 trokuta, tj. dodaje 2 nova trokuta. Nakon 100 točaka, kvadrat će biti podijeljen na 4 + 99*2 = 202 trokuta. Ako je svakom površina `>= 1/2`, ukupna površina kvadrata bi bila `> 100`.

Ako su različiti brojevi poredani u krug i promatramo sume po 3 broja, suma ne može biti ista 2 puta za redom (jer bi to znači da je isti broj dvaput u krugu).

U poliedru između dva vrha može biti samo jedan brid. Broj bridova možeš dobiti tako da izbrojiš bridove koji izlaze iz svakog vrha i podijeliš s 2.

## Geometrija

Za dokazati da je **|AB| = 2|DE|**:
- probaj dodati polovište na AB.

Za dokazati da nešto **raspolovljuje** dužinu:
- probaj dokazati da su neka 2 trokuta sukladna.
- probaj naći dva prava kuta iznad te dužine, po Talesu je dužina promjer.

Za dokazati da su **dužine AB i AC sa zajedničkim vrhom jednake**:
- probaj dokazati da je pravac između vrha A i polovišta osnovice BC okomit, što znači da je simetrala stranice, pa su krakovi AB i BC jednaki.

Za dokazati **okomitost**:
- probaj računanje kutova
- probaj složiti da ortocentar kroz koji ta visina prolazi.

Za dokazati da je **neki kut veći od drugog**:
- probaj složiti trokut s oba i dokazati da je nasuprotna stranica veća.

Za dokazati da **kut je ili nije tup:**
- nacrtaj kružnicu promjera nasuprotne stranice. Ako je kut unutar kružnice, tup je; ako je izvan kružnice, oštar je.

Za dokazati da se **3 pravca sijeku u istoj točki**:
- uzmi dva da se sjeku, a za treći promatraj dužine od i do sjecišta, pa probaj dokazati da je kut između njih `180`,
- Cevin poučak, ako pravci prolaze vrhovima trokuta, onda vrijedi `|AF|/|FB|*|BG|/|GC|*|CH|/|HA| = 1`

### Trokuti

Ako moraš dokazati jednakost koja uključuje neku s ničim povezanu dužinu, probaj tu dužinu dodati na neki postojeći pravac.

Ako ti je negdje zadan kut od `60` ili `30`, probaj spustiti visinu pokraj njega da dobiješ pola jednakostraničnog trokuta.

Ako imaš **simetralu kuta:**
- probaj dodati neku dužinu da dobiješ sukladne trokute koji dijele stranicu na simetrali i jednaki kut koji raspolovljuje.
- simetrala kuta A dijeli stranicu a u omjeru `b/c`.
- okomica na simetralu je simetrala vanjskog kuta

Ako imaš dva prava kuta nad istom dužinom, po Talesu možeš provući kružnicu kroz njih. Onda probaj naći kutove nad istom tetivom. Ako su dva prava kuta nad suprotnim stranama dužine, to je tetivni četverokut, isto možeš provući kružnicu.

Ako imaš polovište stranice trokuta i neku visinu, probaj Talesa.
Ako imaš polovište stranice trokuta, probaj dodati srednjicu (dodaj polovište druge stranice, dobiješ paralelu s osnovicom, ili obrnuto).

Za sličnost trokuta je dovoljno jedan zajednički kut i dvije stranice s jednakim omjerom (npr. težišnice dijele težišta na 1:2).

Težišnica dijeli trokut na dva trokuta jednakih površina. Slično, dužina iz vrha koja dijeli stranicu u omjeru `m:n`, dijeli trokut na trokute površina omjera `m:n`.

Sve 3 težišnice dijele trokut na 6 trokuta istih površina.

Površina trokuta je `a*b*sin(γ)`. Ako se traži nejednakost, `P <= a*b`, jednakost vrijedi za pravokutni trokut koji ima najveću površinu.

### Pravokutni trokuti

Za pravokutni trokut kojem je spuštena visina vrijedi `v² = p*q`, `a² = p*c`, `b² = q*c`.

Težišnica na hipotenuzu je duljine pola hipotenuze (Tales).

Ako treba dokazati da je kut `ABC < 30`, probaj dokazati da je `|BC| > |AB|*√3/2` (općenito da je `|BC| > |AB|*sin(α)`).

Radijus opisane kružnice: `R = c/2` (Tales).
Radijus upisane kružnice: `a + b = c + 2r` (sukladni trokuti)

### Četverokuti

Ako je četverokut **trapez**:
- trokuti nad osnovicom imaju iste površine (`a*v/2`)
- ako imaš dijagonale i visinu, povuci paralelu s dijagonalom tako da dobiješ trokut s bazom `a+c`. Površina trokuta je ista kao i površina trapeza, `v*(a+c)/2`, a osnovicu možeš dobiti preko pitagore.
- probaj povući paralele s krakovima

Ako je **paralelogram**:
- dijagonale se raspolavljaju.
- trokuti na nasuprotnim stranicama čiji se vrhovi dodiruju zauzimaju pola površine paralelograma (povuci visinu kroz zajednički vrh). Očito vrijedi za dijagonale, ali i za sve ostale trokute.

Ako je četverokut **romb**:
- stranice su paralelne i iste duljine
- dijagonale su okomite i raspolavljaju se

Ako imaš dva nasuprotna kuta čiji je zbroj `180`, to je tetivni četverokut kojem možeš opisati kružnicu, pa koristiti obodne kutove i Talesa.

### Mnogokuti

Zbroj vanjskih kutova mnogokuta je 360. Zbroj unutarnjih je `(n-2)*180.`

### Kružnica

Simetrala svake tetive prolazi kroz središte kružnice

Za obodni kut ne mora biti ista tetiva u pitanju - bilo koje dvije tetive iste duljine zatvaraju isti kut. Ako imaš dva ista obodna kuta, njihove tetive su iste duljine.

Lukovi jednake duljine imaju tetive jednake duljine.

Simetrala kuta nad tetivom dijeli kružni luk s druge strane na dva jednaka dijela (zbog jednakih kuteva, moraju biti jednaki i lukovi).

Kut između tangente i tetive je jednak obodnom kutu nad tetivom.

Okomica iz središta kružnice na tetivu raspolavlja tu tetivu.

Ako je zadan radijus i neki pravi kut, produži radijus u promjer i probaj talesa za sukladnost.

Ako je kružnica upisana u kut (ima dvije tangente), kroz njeno središte prolazi simetrala kuta. Vrh kuta je jednako udaljen od dirališta obje tangente.

Ako je zadano središte upisane kružnice trokuta, kroz njega prolaze simetrale kutova.

Ako je zadan radijus upisane kružnice trokuta, `P = r*s`.
Ako je zadan radijus opisane kružnice trokuta, `P = abc/4R` i `a = 2*R*sin(α)`.

Ako ti zadaju pravac koji prolazi kroz polovišta dijaganola, probaj spustiti okomicu iz svakog crha na pravac - dobit ćeš sukladne trokute.

### Konstrukcija

Za konstrukciju bilo kojeg `√(m*n)` broja možeš koristiti Euklidov poučak: nacrtaj kružnicu promjera `m+n`, pa na između m i n digni okomicu. Duljina okomice je `√(m*n)`.

# Literatura

* https://brilliant.org/
