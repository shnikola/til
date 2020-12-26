# Linearna algebra

Linearna algebra proučava linearne jednadžbe, vektorske prostore i matrice.

## Vektori

Vektor je osnovna građevna jedinica u linearnoj algebri. U praksi ga susrećemo u dva oblika, iz perspektive fizičara (vektor je strelica u prostoru s duljinom i smjerom) i iz perspektive programera (vektor je uređeni niz brojeva).

Linearna algebra poopćenjuje ove dvije perspektive. Vektor je sve što ima operaciju **zbrajanja** i **skaliranja** (množenja s brojem).

Vektor može biti `n`-dimenzionalan, što znači da se strelica nalazi u `n`-dimenzionalnom prostoru, odnosno da niz brojeva je duljine `n`.

Zbrajanje brojeva je zapravo zbrajanje jednodimenizonalnih vektora. To je još jasnije kad zamisliš zbrajanje vektora na brojevnom pravcu.

Kad govorimo o vektorima u koordinatnom sustavu, najlakše je zamisliti strelice koje kreću iz ishodišta. Ako radiš sa zasebnim vektorima, koristi strelice, a ako radiš sa skupovima vektora, zamisli skup točaka (vrhova strelica).

Radi lakšeg snalaženja, vertikalne vektore u nastavku označavam s `<a, b, c>`, a matrice kao `[<a,b>, <c,d>]`, gdje je `<a,b>` prvi stupac.

## Kombinacije

Skup svih linearnih kombinacija neka dva vektora naziva se **raspon** (span, ljuska). Drugim riječima, raspon govori koje sve rezultate možeš dobiti samo zbrajanjem i skaliranjem.

Vektor koji se nalazi u rasponu nekog skupa vektora je **linearno zavistan**; ako se ne nalazi u rasponu (tj. ne može ga se dobiti linearnom kombinacijom), vektor je **linearno nezavistan**.

Ako su 2D-vektori kolinearni, njihov raspon je pravac na kojem se nalaze; u svakom drugom slučaju njihov raspon je cijela ravnina.

Raspon dva 3D-vektora je ravnina (u prostoru). Da bi se pokrio cijeli prostor, potrebno je dodati treći vektor koje ne leži u toj ravnini (tj. koji je linearno nezavistan).

## Transformacije

Transformacija je fancy ime za funkciju koja prima vektor i vraća drugi vektor. Nazivamo ih transformacije da naglasimo kako se objekti u koordinatnom sustavu pritom grafički transformiraju.

Transformacija je linearna ako je **aditivna** (`L(v+w) = L(v) + L(w)`) i **skalabilna** (`L(cv) = cL(v)`). Vizualno, to znači da sve linije ostaju linije, i ishodište ostaje na istom mjestu.

Svaki vektor možemo izraziti linearnom kombinacijom baznih vektora `î` i `ĵ` koordinatnog sustava. Dovoljno je znati gdje bazni vektori završe nakon transformacije da bi znali gdje svaki vektor završi. Npr. ako vektori `î` i `ĵ` postanu `<3, -4>` i `<2, 1>`, bilo koji vektor `aî + bĵ` će transformacijom postati `a<3, -4> + b<2, 1>`.

Svaka linearna transformacija na 2D ravnini je tako opisana sa samo 4 broja. To možemo upisati u matricu T oblika `[<3, -4>, <2, 1>]`

Prvi stupac je transformacija vektora `î`, a drugi vektora `ĵ`. Ako želiš transfomirati vektor `v = <x, y>`, treba samo izračunati vektorski umnožak `T*v`, koji vraća novi vektor `x<3, -4> + y<2, 1> = [3x+2y, -4x+y]`.

Svaki put kad susretneš matricu, možeš je shvatiti kao određenu linearnu transformaciju prostora. Neke od popularnih transformacija su:
- identitet `[<1, 0>,<0, 1>]` ostavlja sve kako je.
- rotacija `[<0, 1>,<-1, 0>]`
- shear `[<1, 1>,<1, 0>]` iskrivljuje po x-osi.

**Umnožak dvaju matrica** daje novu matricu koja je jednaka kombinaciji tih dviju transformacija. Obrati pažnju da se transformacije izvode počevši od desne matrice (kao i kompozicija funkcija `f(g(x))`). Množenje matrica je asocijativno, ali ne i komutativno - redoslijed transformacija je bitan.

**Inverzna matrica** je ona čiji umnožak s početnom matricom daje matricu identiteta. Grafički, inverzna matrica radi obrnutu transformaciju, vraćajući vektore u početni položaj.

Matrice s različitim brojem stupaca i redova rade transformacije između dimenzija. Npr. `3x2` matrica 2D-vektore transformira u 3D-vektore: 2 stupca znači da uzima 2 bazna vektora `î` i `ĵ`, a 3 retka da transformirani bazni vektori imaju 3 dimenzije.

## Determinanta

**Determinanta** je mjera koliko se bilo koja **površina smanji ili poveća** nakon trasnformacije. Ako je determinanta 2, svaka površina će se udvostručiti. Ako je determinanta 0.5, svaka površina će biti upola manja. Ako je determinanta 0, transformacija sve površine pretvara u pravac (ili točku).

Ako je determinanta negativna, to znači da transformacija mijena orijentaciju prostora (tj. "preokrene" ravninu na drugu stranu).

Isto vrijedi i za više dimenzije. U 3D transformacijama determinanta mjeri koliko se volumen mijenja.

## Rang i Kernel

**Rang** (rank) matrice je broj dimenzija rezultata transformacije. 3D transformacija može imati najviše rang 3 i to zovemo **puni rang**. Ako matrica sve vektore transformira u ravninu ima rang 2. Ako ih transformira na pravac, ima rang 1.

Skup svih mogućih rezultata transformacije naziva se **prostor stupaca** (column space). Stupci matrice su transformacije jediničnih vektora, a njihov raspon je prostor stupaca.

Vektor `<0, 0>` će uvijek biti u prostoru stupaca, jer ishodište uvijek ostaje na istom mjestu. Kod transformacija nižeg ranga i drugi vektori mogu završiti u ishodištu. Npr. kod transformacije koja cijeli prostor stavlja na pravac, svi vektori okomiti na taj pravac će postati `<0, 0>`. Skup vektora koji će transformacija pretvoriti u `<0, 0>` zovu se **null space** ili **kernel**.

## Skalarni umnožak (dot product)

**Skalarni umnožak** je mjera **duljine projekcije** jednog vektora na drugi. Zbog simetrije je komutativna, pa nam nije bitno koji vektor projiciramo na kojega. Ako vektori gledaju u suprotnom smjeru, skalarni umnožak je negativan. Ako su okomiti, duljina projekcije je 0, pa je i skalarni umnožak 0.

Za vektore  `u = <1, 2, 3>` i `v = <4, 5, 6>`, skalarni umnožak je jednak duljini projekcije `v'` skaliran s duljinom vektora `u`. Računa se kao suma `1*4 + 2*5 + 3*6`.

Skalarni umnožak se može dobiti i transformacijom. Vodoravna 1x2 matrica `T = [<1>,<2>]` predstavlja transformaciju koja projicira dani vektor na pravac vektora `<1, 2>`. Npr. vektor `<4, 5>` će se projicirati u točku `T*v = 1*4 + 2*5 = 14` na tom pravcu. Isto vrijedi i za projekcije iz viših dimenzija, npr. `T = [1,2,3]`.

Drugim riječima, svaki skalarni umnožak vektorom (npr. `<1, 2>⋅v` je isto što i transformacija vodoravnom matricom (npr. `[1 2]*v`). Vrijedi i obrnuto - svaka transformacija koja rezultira skalarom ima pripadajući vektor na koji se projicira. Takvu pojavu koja povezuje dva različita koncepta nazivamo **dualnost**.

Transformacije koje zadržavaju isti skalarni umnožak dva vektora nazivamo **ortonormalne transformacije**. U njima okomiti pravci ostaju isti (npr. rotiranje, skaliranje).

## Vektorski umnožak (cross product)

**Vektorski umnožak** predstavlja **površinu paralelograma** kojeg dva vektora zatvaraju. Umnožak je pozitivan ako je smjer vektora isti kao između `î` i `ĵ`(CCW); ako je smjer suprotan umnožak je negativan.

Preciznije, vektorski umnožak je vektor kojem je duljina površina paralelograma, a smjer okomit na paralelogram.

Za izračunati veličinu površine između `u = <3, 1>`i `v = <2, -1>`, možemo koristiti determinantu matrice `[<3, 1>, <2,-1>]` .

Ta matrica predstavlja transformaciju jediničnih vektora u vektore `u` i `v`. Pošto je površina između jediničnih vektora `1`, a determinanta mjera za koliko se ta površina poveća, onda će determinanta transformacijske matrice biti jednaka površini između vektora `u` i `v`.

Za 3D-vektore je malo kompliciranije jer ne možemo izračunati determinantu 3x2 matrice, ali postoji metoda u kojoj se za prvi stupac uzme `<î, ĵ, k>`. Bitno je da vektorski umnožak u višim dimenzijama ne mjeri volumen, nego i dalje površinu između dva n-dimenzionalna vektora.

## Promjena baze

Koordinatni sustav je zapravo način da vektore zapišemo kao niz brojeva. Zapis `v = <-1, 2>` u standardnom koordinatnom sustavu znači da je vektor `v` zbroj skaliranih baznih vektora `-1î` i `2ĵ`. Pritom je koordinatni sustav strogo definiran odabirom baznih vektora. U standardnom slučaju, oni su okomiti i jednake duljine.

Ali za bazne vektore možemo uzeti i neka druga dva vektora, u drugačijem odnosu. Npr. vektori koji se u standardnom sustavu zapisuju kao `î = <2, 1>` i `ĵ = <-1, 1>`, mogu biti baza novog koordinatnom sustava `K₂` i u njemu se zapisivati kao `<1,0>` i `<0,1>`.

Vektor zapisan kao `<-1,2>` u `K₂` sustavu, u standardnom sustavu je `<-4, 1>`. Standardni zapis možemo dobiti transformiranjem `K₂` zapisa `<-1, 2>` s matricom `A = [<2, 1>, <-1, 1>]`. Stupci matrice su bazni vektori `K₂`, u zapisu našeg koordinatnog sustava. Za obrnuto prevođenje iz našeg zapisa u `K₂` zapis, koristimo inverznu transformaciju `A⁻¹`.

Zapis matrica transformacije također ovisi o koordinatnom sustavu. Da bi preveli standardnu rotaciju `T = [<0, 1>,<-1, 0>]` u `K₂` sustav, koristimo `A⁻¹TA`: s desna na lijevo, prvo prebacujemo vektor u naš sustav, transformiramo ga, i onda prebacujemo nazad u `K₂` sustav.

## Svojstveni vektori (eigenvectors)

Većina transformacija će maknuti vektor s njegovog spana, tj. pravca na kojem se nalazi. Vektore koji nakon transformacije ostanu na svom spanu zovemo **svojstveni vektori transformacije**. Faktor za koliko se svojstveni vektori skaliraju nazivamo **svojstvena vrijednost** (eigenvalue).

Neke transformacije nemaju svojstvene vektore. 2D rotacija rotira svaki vektor, pa nijedan ne ostaje na svom pravcu. 3D rotacija ima jedan svojstveni vektor - os rotacije. Svojstvena vrijednost svake rotacije je `1`, jer se ništa ne povećava ni smanjuje.

Svojstveni vektori su oni `v` za koje vrijedi `Av = λv`, pri čemu je `λ` svojstvena vrijednost. Lijeva strana je transformacija (umnožak matrice i vektora), a desna skaliranje. Rješenja tražimo iz jednadžbe `det(A-λI) = 0`.

Ako je matrica **dijagonalna**, npr. `[<4,0,0>,<0,2,0>,<0,0,-3>]`, njeni svojstveni vektori su bazni vektori sustava `î`, `ĵ` i `k`, sa svojstvenim vrijednostima `4`, `2` i `-3`. Dijagonalne matrice su zgodne za računanje jer se lako množe (`[<2,0>,<0,3>]ⁿ = [<2ⁿ,0>,<0,3ⁿ>]`).

Ako moraš npr. naći veliku potenciju nedijagonalne matrice `M`, možeš:
1. pronaći svojstvene vektore `M`,
2. prebaciti `M` u sustav gdje su svojstveni vektori baza
3. `M` će postati dijagonalna, pa je lako izračunati što treba.
4. Vratiti rezultat nazad u naš sustav.

## Apstraktni vektorski prostori

Do sada smo vektore predstavljali kao strelice u prostoru ili nizove brojeva, ali bitno je upamtiti da je vektor bilo koji objekt koji ima operaciju zbrajanja i skaliranja.

Npr. **funkcije** se mogu zbrajati (`(f+g)(x) = f(x)+g(x)`) i skalirati (`(2f)(x) = 2f(x)`). Zbrajanje funkcija možemo zamisliti kao zbrajanje vektora koordinatu po koordinatu, samo imamo beskonačno koordinata (po jednu za svaki `x`).

Kada znamo da su funkcije vektorski objekti, možemo na njih primjeniti svu prethodnu teoriju koju smo dosad razvili. Prostor polinomijalnih funkcija možemo prikazati pomoću beskonačno mnogo baznih funkcija `b₀(x) = 0`, `b₁(x) = x`, `b₂(x) = x²` itd. Svaki polinom je linearna kombinacija baznih funkcija, baš kao i kod vektora.

Možemo prepoznati i linearne transformacije kao operatore koji uzimaju funkciju i vraćaju novu funkciju, kao što to radi npr. **derivacija**. Bitno je samo da je operator linearan, tj. da je aditivan i skalabilan.

Skup objekata koji imaju vektorska svojstva (strelice, nizove brojeva, funkcije) nazivamo **vektorski prostor**. Ako ispunjava 8 aksioma, na njega se mogu primjenjivati svi koncepti linearne algebre (transformacije, kernel, svojstveni vektori, itd). Ti aksiomi nisu "zakoni prirode", nego dogovoreno sučelje između matematičara koji smišljaju nove vektorske prostore.

# Literatura

* https://www.youtube.com/playlist?list=PLZHQObOWTQDPD3MizzM2xVFitgF8hE_ab

