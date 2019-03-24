# Puzzles

## Thinking of a number

Zamislio sam broj: 1, 2 ili 3. Da ga otkriješ, smiješ mi postaviti jedno pitanje na koje ću ti odgovoriti s da ili ne, ako mogu.

"Bacam onoliko novčića jednak broju koji si zamislio. Hoću li dobiti dva ista rezultata?". Za 1 - odgovor je ne; za 2 - možda; za 3 - da.

## Names in Boxes

100 imena zatvorenika stavljena su u 100 kutija, po jedno ime u svaku kutiju. Zatvorenici ulaze u sobu jedan po jedan, i smiju otvoriti najviše 50 kutija. Moraju ostaviti sve kako su našli i ne smiju komunicirati s ostalima. Zatvorenici će biti pošteđeni samo ako svi pronađu svoje ime. Koja je strategija s najvećom vjerojatnošću uspjeha?

Zatvorenici "dogovorno" svaku kutiju "označe" po jednim imenom. Kada zatvorenik ulazi u sobu, prvo otvara kutiju "označenu" svojim imenom. Ime koje nađe unutra koristi kao sljedeću kutiju će otvoriti, i tako dalje. Vjerojatnost uspjeha ove strategije je malo iznad `30%`.

Zašto? Praćenje imena u kutijama ga mora dovesti do kutije u kojem je njegovo ime jer ga ta vodi na kutiju od koje je počeo, zatvarajući "krug". Zatvorenik će uspjeti ako je njegov "krug" manji od 50 koraka. Permutacija kutija se zapravo sastoji od jednog ili više takvih krugova. Zatvorenici neće uspjeti samo ako u njihovoj permutaciji postoji krug veći od 50 koraka.

## Boy-Boy Paradox

Ako ti nepoznati čovjek kaže: "Imam dvoje djece. Jedno od njih je dječak", koja je vjerojatnost da je i drugo dijete dječak?

Uzmimo u obzir da se jedno dijete rodilo prije, a drugo kasnije. Mogućnosti su sljedeće: ŽŽ, MŽ, ŽM, MM. ŽŽ otpada jer znamo da mora biti bar jedan dječak. Od ostalih, jednako vjerojatnih mogućnosti, 1/3 ima oba dječaka.

**Twist:** Ako čovjek kaže: "Stariji je dječak", koja je vjerojatnost da je i drugo dijete dječak?

Mogućnosti su ŽM i MM. Vjerojatnost da su oba dječaka je tada 1/2.

**Twist:** Ako čovjek kaže "Jedno od njih je dječak rođen u utorak", koja je vjerojatnost da je i drugo dijete dječak?

Raspišemo sve kombinacije mlađeg i starijeg djetata po danima rođenja. Ispast će da od 27 jednako vjerojatnih mogućnosti, 13 sadrže dva dječaka. Vjerojatnost je 13/27.

S ovakvim zadatcima pomaže ako ne počneš s podacima koje imaš, nego zamisliš cjelokupan prostor problema (sve jednako vjerojatne kombinacije), pa eliminiraš slučajeve koji se ne slažu s danim podatkom.

**Twist:** Znaš da g. Horvat ima dvoje djece, i sretneš ga na ulici sa sinom. Koja je vjerojatnost da je i drugo dijete dječak?

Ovo je iz nekog razloga 1/2. Možda. Nisam siguran.

## Slanje poštom

Poštari ukradu sadržaj svakog paketa koji nije zaključan lokotom. Želiš poslati nešto prijatelju, ali nijedno nemate ključ ovog drugoga.

Ti staviš svoj lokot na paket, pošalješ ga njemu. On doda svoj lokot, pošalje paket nazad tebi. Ti makneš svoj lokot, pošalješ ga nazad njem u. To je Diffie-Hellman algoritam!

## Secretary Problem

Problem: Na intervju ti dolazi `N` osoba, jedna po jedna. Odmah nakon svakog intervjua moraš odlučiti hoćeš li tu osobu odbiti ili zaposliti. Ako je odbiješ, ona je trajno eliminirana. Ako je zaposliš, proces je gotov i osobe koje još nisu stigle na intervju su odbijene. Koja je optimalno pravilo odabira (tj. *stopping rule*)?

Optimalna strategija je odbaciti prvih `N/e` kandidata, i zatim zaposliti prvu osobu koja je bolja od svih odbačenih. Vjerojatnost da ovako odabereš najboljeg je `1/e`, tj. `37%`.

## Zarobljenici i kape

100 zarobljenika su poredani jedan iza drugog i svakome se stavi crvena ili bijela kapa na glavu. Počevši od zadnjeg, krvnik pita koje boje je njegova kapa. Ukoliko odgovori točno, pušta ga na slobodu, u suprotnom ga ubije. Koliko ih se najviše može spasiti?

99 sigurno. Prvi izbroji crvene kape ispred sebe. Ako je broj paran, kaže "bijelo", ako je neparan, kaže "crveno". Idući vidi koliko je crvenih ispred njega, ako je parnost različita, on ima crvenu kapu. Idući također i tako sve do zadnjeg.

**Varijanta**: Što ako ima 50 različitih kapa?

Dogovore se da je svaka boja kape jednaka broju 1-50. Prvi zbroji sve kape i kaže boju od `suma mod 50`. Idući također zbroji kape ispred sebe i oduzme od izgovorenog broja da dobije svoju.

# Literatura

* https://www.sciencenews.org/article/when-intuition-and-math-probably-look-wrong


