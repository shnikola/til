# Infitezimalni račun

Infitezimalni račun proučava promjene funkcija (diferencijalni račun) i sume beskrajno malih veličina (integralni račun).

## Derivacije

Derivacija funkcije određuje koliko se izlaz funkcije mijenja za malu promjenu u ulazu.

Neka je `s(t)` funkcija koja mjeri pređeni put automobila u svakom trenutku. Brzinu možemo mjeriti kao `Δs/Δt`, promjenu puta na nekom vremenskom intervalu. Što je `Δt` manji, to je mjera preciznija. Na grafu `s(t)`, brzina je omjer rasta funkcija `(s(t+Δt) - s(t))` naprama koliko ide udesno `Δt`.

Formalno, **derivacija funkcije** je omjer `(f(x+dx) - f(dx))/dx`, kada se `dx` približava `0`. Derivaciju `f(x)` označavamo s `df/dx`, `d(f)`, ili `f'(x)`. Derivacija je nova funkcija `f'(x)` koja je u nekoj točki `a` jednaka promjeni izvorne funkcije u toj točki.

Grafički, derivacija `f'(a)` je jednaka nagibu tangente na krivulju `f` u točki `a`. Taj nagib je omjer horizontalne komponente `dx` i vertikalne komponente `df`. Vrijedi `df = f'(a) * dx`.

Koristimo izraz "približava nuli" (tj. limes), jer pojam "beskonačno mala vrijednost" nije baš najbolje definiran. Promjenu ne možemo mjeriti u jednoj točki (trebaju nam dvije vrijednosti za promjenu), pa je derivaciju bolje shvatiti kao **najbolju aproksimaciju promjene oko dane točke**.

## Kombiniranje funkcija

Svaka složena funkcija se može zapisati kao kombinacije **zbrajanja**, **množenja** i **kompozicije** jednostavnijih funkcija. Deriviranje složenih funkcija slijedi ova pravila:

Zbrajanje: `d(f + g)/dx = df/dx + dg/dx`. Vizualno, ako je funkcija zbroj `f` i `g`, onda će promjena za `dx` biti jednaka zbroju promjena `df` i `dg`.

Umnožak: `d(f * g)/dx = f*dg/dx + g*df/dx`. Vizualno, zamislimo površinu `f*g`, promjena za `dx` će povećati stranice za `dg` i `dh`, ukupno dva tanka pravokutnika i mali kvadrat koji se zanemari.

Kompozicija: `df(g)/dx = df/dg * dg/dx`. Vizualno, zamisli kako promjena `f` ne ovisi direktno o `x`, nego o `g`, pa je `d(sin(x²))/d(x²) = cos(x²)`, tj. `d(sin(x²)) = cos(x²)*d(x²) = cos(x²)*2x*dx`

## Eksponenijalna funkcija i e

Derivacija exponencijalne funkcije `f(x) = aˣ` je `f'(x) = aˣ(aᵈˣ-1)/dx`. Kada se `dx` približava `0`, `(aᵈˣ-1)/dx` se približava nekom broju, ovisno o `a`.

Baza za koju je taj broj jednak `1` je `e = 2.71828`. U tom slučaju je `f'(x) = eˣ`, tj. derivacija daje istu funkciju.

Eksponencijalne funkcije ostalih baza se mogu prikazati kao `e^(ln(a)x)`, pa je prema derivaciji kompozicije `f'(x) = ln(a)*aˣ`.

Ako prirodni fenomen ima brzinu promjene proporcionalan trenutnoj vrijednosti (npr. rast populacije ovisi o veličini populacije u tom trenutku), radi se o eksponencijalnoj funkciji. Eksponencijalne funkcije preferiramo pisati u obliku `f(x) = e^(kx)`, gdje je `k` konstanta proporcionalnosti.

## Implicitna diferencijacija

Umjesto uobičajene funckije `f(x)` možemo imati zadanu **implicitnu krivulju**, npr. kružnicu `x² + y² = 5²`. Da bi izračunali tangentu na krivulju, trebamo nekako izračunati derivaciju te krivulje u točki.

Krivulju možemo promatrati kao funkciju dvije varijable `S(x,y) = x^2 + y^2`. `dS = 2xdx + 2ydy`, gdje su `dx` i `dy` male promjene u ulazima. Ako se ograničimo na promjene po kružnici, `2xdx + 2ydy = 0`, jer `S` uvijek ostaje isti. Iz toga dobijemo `dy/dx` kao nagib tangente.

Pomoću ovoga možemo izračunati derivaciju `y = ln(x)`. Napišemo li ga u implicitnom obliku `x = e^y` i deriviramo, dobijemo`dx = e^y*dy`, tj. `dy/dx = 1/e^y = 1/x`.

## L'Hopitalovo pravilo

U slučaju da `lim f(x)/g(x) = 0/0` za neki `x->a`, to znači da funkcije `f(x)` i `g(x)` prolaze kroz x-os u točki `a`. Dodamo li mali `dx`, vrijednost funkcija će biti `df` i `dg`, tj. `f'(a)*dx` i `g'(a)*dx`, pa je omjer jednak `lim (f'(a)*dx)/(g'(a)*dx) = lim f'(x)/g'(x)`.

Na taj način možemo izračunati `lim sin(x)/x` za `x->0`: `lim cos(0)/1 = 1`.

## Integralni račun

Neka je `v(t)` funkcija koja mjeri brzinu automobila u svakom trenutku.
Ukupni pređeni put možemo izračunati ako podijelimo na mnogo malih pravokutnika širine `Δt` i zbrojiti njihove površine `v(t)*Δt`.

Integral `∫f(x)*dx` predstavlja "sumu" površina pravokutnika širine `dx` i visine `f(x)`. Koristimo `∫` umjesto `Σ` jer nije prava suma, nego vrijednost kojoj suma teži kada `dt` teži u `0`. Vrijednost kojoj integral teži je površina ispod grafa.

Integriranje je proces suprotan od deriviranja (množimo s `dx` umjesto dijelimo) i proces rješavanja integrala je naći antiderivacuju `F(x)` koja derivirana daje `f(x)`.

Temeljni teorem infitezimalnog računa glasi `ₐ∫ᵇf(x) = F(b) - F(a)`. To znači da je za izračunati površinu za cijeli raspon ulaza od `a` do `b` dovoljno imati samo dvije vrijednosti: antiderivaciju u početnoj i krajnjoj točki.

Integriranje koristimo kada želimo sumirati vrijednosti koje nisu diskretne, nego kontinuirane. Npr. za računanje prosječne vrijednosti funkcije `f(x)` na intervalu `[0, n]` ne možemo upotrijebiti klasično računanje prosjeka jer imamo beskonačno vrijednosti. Umjesto toga, uzimamo uzorke širine `dx`, pri čemu ćemo imati `n/dx` uzoraka. Prosječna vrijednost je onda `f(xᵢ)/(n/dx)) = 1/n * Σ(f(xᵢ)dx)`. Za `dx -> 0`. to je `1/n * ₀∫ⁿf(x) = (F(n) - F(0))/n`.

## Taylorov red

Želimo aproksimirati proizvoljnu funkciju `f(x)` (npr. `cos(x)`) pomoću polinoma oblika `P(x) = c₀ + xc₁ + x²c₂ + ...`, ali samo u okolici točke `x = 0`.

Za početak, želimo da funkcija i polinom imaju istu vrijednost u točki `0`, tj. `P(0) = f(0)`, pa je `c₀ = f(0)`.

Zatim želimo da funkcija i polinom imaju isti nagib u točki `0`, tj. `P'(0) = f'(0)`, pa je `c₁ = f'(0)`. Isto vrijedi i za sljedeću derivaciju, `2*c₂ = f''(0)`, itd. Svaka sljedeća derivacija će nam dati sljedeći koeficijent (koji se zbog deriviranja polinoma množi s faktorijelom).

Konačno, **Taylorov polinom** je aproksimacija oblika `P(x) = f(0) + f'(0)*x/1! + f''(0)*x/2! + f'''(0)*x/3! + ...`. Ako koristimo beskonačno mnogo članova, to je **Tylorov red**.

Ovim putem koristeći samo informaciju o **derivacijama u jednoj točki** dobijamo **vrijednosti funkcije u okolnim točkama.**

Aproksimacije nekih funkcija (npr. `eˣ` i `sin(x)`) postaju preciznije što više članova polinoma dodajemo, čak i za točke koje su dosta udaljene od `x = 0`. Taylorov red tih funkcija konvergira u vrijednost funkcije cijelom njihovom domenom.

Ostale funkcije konvergiraju samo unutar određenog raspona od `x = 0`. Npr. `ln(x)` konvergira za `x < 2`, za ostale divergira sve više za svakim članom polinoma kojeg dodajemo.
