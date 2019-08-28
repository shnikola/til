# Statistics

## Mode, Median, Mean

Promatrajući sortirani niz vrijednosti `x = [...]`, želimo pronaći jednu vrijednost koja će ga najpreciznije opisati. Postoji nekoliko opcija:
* `mean` je prosječna vrijednost elemenata niza.
* `median` je vrijednost koja se nalazi u sredini niza.
* `mode` je vrijednost koja se najčešće pojavljuje u nizu.

Postoji logičan način kako se dođe do ova 3 pojma. Traženje opisne vrijednosti `m` zapravo je traženje vrijednosti koja se najmanje razlikuje svih vrijednosti niza. Pitanje je samo kako definiramo "razliku".
* Ako je razlika `|x[i] - m|^0`, svaki različit element brojimo kao `1`, a svaki jednaki kao `0`. Ukupna razlika će biti najmanja kada je `m` vrijednost koja se najčešće pojavljuje - mod.
* Ako je razlika `|x[i] - m|^1`, mjerimo apsolutnu razliku između svakog elementa i `m`. Ukupna razlika je najmanja kada koristimo medijan.
*  Ako je razlika `|x[i] - m|^2`, mjerimo kvadrat razlike između svakog elementa i `m`. Ukupna razlika je najmanja kada koristimo mean.

Ako su mean i median jednaki, to znači da je distribucija simetrična: sa svake strane prosjeka je jednaki broj točaka. Kada nisu jednaki, distribucija je nesimetrična (*skewed*). U tom slučaju će mode i dalje biti vrh grafa distribucije, median će biti u sredini, ali mean će imati ekstremnije vrijednosti.

## Spread

**Range** je razlika između najviše i najniže vrijednosti (`max-min`). To nam ne govori puno o vrijednostima između, pa možemo koristiti **Interquartile Range** (IQR) koji uzima u obzir srednjih 50% vrijednosti i tako zanemaruje ekstreme. Ali i dalje se koriste samo dvije vrijednosti za računanje raspona, pa ne saznajemo puno o vrijednostima između.

**Varijanca** (*variance*) uzima u obzir sve vrijednosti. Za svaku vrijednost se računa **devijacija** (*deviation*), razlika od prosjeka (`x[i] - m`), devijacije se kvadriraju i računa im se prosjek (`∑(x[i] - m)^2 / N`).

Problem s varijancom je što ako uzimamo uzorak populacije (`n`), varijanca će biti **pristrana** (*biased*), tj. biti će konzistentno manja od stvarne varijance nad cijelom populacijom (to se da matematički pokazati). Zato se procjena varijance iz uzorka računa kao (`∑(x[i] - m)^2 / n-1`). Za dovoljno veliki `n`, razlika se ne primjeti.

Varijancina vrijednost je nekakav glupi kvadrat, pa se umjesto toga koristi **standardna devijacija** koja je korijen varijance, pa se može uspoređivati s vrijednostima populacije. Standardna devijacija je prosječna razlika između neke vrijednosti u populaciji i prosjeka populacije.

## Korelacija

**Koeficijent korelacije (r)** određuje mjeru povezanosti dvaju varijabli, u vrijednosti od `-1` do `1`. **r^2** od `0` do `1` mjeri koliko precizno možeš predviđati vrijednost jedne varijable pomoću druge.

## Eksperimentalna Istraživanja

**Single blind study** je kada ispitanici ne znaju koji treatment dobijaju (jer imaš dobar placebo), a ispitivači znaju. **Double blind study** je kada ni ispitivači ne znaju tko prima tretman, kako be bi mogli podsvjesno utjecati na rezultate.

**Matched pair experiments** su najpoželjniji, jer uspoređuju rezultate različitih tretmana na jako sličnim ispitanicima (npr. blizancima). **Repeated measuers design** je kada daješ jednom te istom ispitaniku različite tretmane.

**Spurious corelation** kada dvije varijable slučajno koreliraju, iako nisu povezane (npr. broj utapanja i filmova Nicholasa Cagea).

## Ankete

Ankete nasumičnim samplingom pokušavaju prikupiti reprezentativne podatke o cijeloj populaciji. Jedan od problema je **response bias**, jer su ljudi koji će pristati ispuniti anketu sustavno različiti od ostatka populacije. Npr. penzioneri imaju više vremena pa će se javljati na telefonske ankete; za ispitivanje zadovoljstva uslugama vjerojatnije će odgovarati korisnici koji imaju vrlo negativno ili vrlo pozitivno mišljenje.

Drugi problem je **underrepresentation**. Zbog nasumičnog samplinga i response biasa, moguće je da neke manjinske skupine (npr. samohrane majke) uopće neće biti zastupljene u uzorku. Da bi se to izbjeglo možemo vrednovati rezultate jednog manjinskog ispitanika kao više njih (**weighted results**) da udio bude ispravan, ali to može dovesti do krivih rezultata ako taj ispitanik dobro ne predstavlja manjinu. Drugi metoda je podijeliti populacije u grupe (**stratified random sampling**) i onda uzimati nasumični sample iz svake.

Provođenje random samplinga na individualnoh razini je skupo (zamisli nasumičnu anketu na razini SAD-a). Zato se koristi **cluster sampling** gdje se stvore clusteri (npr. škole ili gradovi), te se nasumično odabere nekoliko clustera koji će se anketirati. Bitno je da clusteri ne smiju biti sustavno drugačiji od populacije.

Ako je populacija premala (npr. djeca s rijetkim genetskim bolestima ili ovisnici o određenim drogama) anketari ne moraju koristiti nasumični odavbir, nego **snowball sampling**, gdje se ispitanike pita da dovedu svoje poznanike na anketu.

Ako se radi ispitivanje cijele populacije, to se zove **cenzus**, ali to je mega skupo i najčešće se radi svakih 10 godina.

## Inspection Paradox

Ako želiš izmjeriti prosječnu veličinu grupe na fakultetu, možeš anketirati dio studenata i izračunati prosjek tog uzorka. Ali tako izračunati prosjek će uvijek biti veći od stvarnog, jer će nasumičnim odabirom anketirati više studenata iz većih grupa nego iz manjih.

Ovo nije nužno pogreška, jer je ono što anketa zapravo mjeri veličina grupe koji studenti doživljavaju. Ali bitno je znati što pokušavaš mjeriti. Ovaj problem je prisutan u mnogim drugim situacijama.

Zračne kompanije se žale kako je mnogo letova prazno, a putnici da na letovima nikad nema praznog mjesta. Obje strane su u pravu, jer kad su letovi prazni, svega nekoliko ljudi to primjeti, a popunjene letove primjete svi.

Ako autobus dolazi svakih 10 minuta, prosječno vrijeme čekanja bi trebalo biti 5 minuta. Ipak, neki autobusi urane, a neki kasne; ali autobuse koji kasne primjeti više ljudi jer ih više stigne doći na stanicu.

Većina ljudi imaju manje prijatelja nego njihovi prijatelji. Ako odabereš nasumičnog čovjeka, i onda nasumično njegovog prijatelja, u 80% slučajeva će taj prijatelj imati više prijatelja. Također, većina obitelji ima jedno dijete, ali većina djece imaju braću ili sestre.

## Vjerojatnost

Empirijska vjerojatnost - opažamo je u stvarnim podacima, tj. iz uzoraka. Daju nam uvid u teoretsku (stvarnu) vjerojatnost, ali nije uvijek jednaka njoj.

`P(A || B) = P(A) + P(B) - P(A && B)`

`P (A && B) = P(A)*P(B)` ako su A i B neovisni. A i B su neovisni ako se vjerojatnost jednog ne mijenja ovisno o drugom.

`P (A | B) = P(A && B) / P(B)`

## p-values

Imamo lijek za prehladu kojem želimo izmjeriti učinkovitost. Polovici ispitanika damo lijek, a polovici placebo. Ako su ispitanici koji su primili lijek imali u prosjeku kraću prehladu, znači li to da je lijek dobar? Ne nužno. Sve prehlade ne traju jednako drugo, i moguće je da smo igrom slučaja lijek dali pacijentima s kraćom prehladom.

Ako poznajemo distribuciju dužina prehlada, možemo odrediti kolika je vjerojatnost da smo nasumično odabrali pacijente s dužinom manjom od prosjeka. **p-vrijednost** je vjerojatnost da dobijemo izmjereni rezultat ako je naš lijek bez ikakvog djelovanja. Što je p-vrijednost manja, to je veća vjerojatnost da je naš rezultat vjerodostojan. P-vrijednost je manja što su izmjereni efekt i broju ispitanika veći.

Većinom se uzima da je izmjereni efekt značajan ako je p manji od `0.05` (5% šansa da smo rezultat dobili slučajno). Ali mali p ne garantira veličinu efekta, samo njegovu vjerodostojnost. Mali p može biti posljedica velikog efekta, ili malog efekta s velikom sigurnošću.

Statistička moć (**power**) studije je izglednost da će moći razlikovati efekt od čistog slučaja. Znanstvenici su najčešće zadovoljni s moći od 0.8 (80% šansa zaključivanja da efekt postoji), ali velik broj studija ne navodi statističku moć jer radi s premalim grupama.

## Replikacija

Kako bi dobili pouzadnije rezultate, mnoge studije nastoje sakupiti još podataka kroz replikaciju, radeći dodatna mjerenja. Problem koji se često previdi je pseudoreplikacija, kada su dodatna mjerenja zapravo povezana s izvornima. Npr. ako mjerimo iste ispitanike kroz više dana, uzimamo dodatne neurone iz iste životinje, testiramo stanice iz iste kulture, replikacija nije valjana jer takva mjerenja nisu neovisna.

## Surprisingly popular

Metoda za dobivanje mišljenja malog broja eksperata iz populacije. Npr. postavlja se pitanje "Je li Sydney glavni grad Australije?" (da: 60%, ne: 40%) i "Što mislite, što će većina ljudi odgovoriti na to pitanje?" (da: 75%, ne 25%). Računa se razlika u odgovorima između ova dva pitanja:

`da: 60% - 75% = -15%`
`ne: 40% - 25% = 15%`

Odgovor "ne" je iznenađujuće popularan (`15% > -15%`), pa se može vjerovati da je to točan odgovor. Kada ispitanici misle da imaju "insider knowledge", najčešće su u pravu a ne u zabludi.

## Optimal Stopping

Pred tobom je niz od `N` kartica na kojima je različitih brojeva, od 1 do 1^100. Po redu okrećeš jednu po jednu karticu i cilj ti je odabrati najveći broj. Ali možeš odabrati samo karticu koji su posljednju okrenuo. Koje je optimalno pravilo odabira?

Optimalna strategija je odbaciti prvih `N/e` brojeva (koristimo ih za uzorak), i zatim odabrati prvi idući broj koji je veći od svih odbačenih. Vjerojatnost da će on biti najveći je `1/e`, tj. `37%`.

# Literatura

* https://www.statisticsdonewrong.com
