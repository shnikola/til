# Puzzles

## Thinking of a number

Zamislio sam broj: 1, 2 ili 3. Da ga otkriješ, smiješ mi postaviti jedno pitanje na koje ću ti odgovoriti s da ili ne.

"Bacam onoliko novčića jednak broju koji si zamislio. Hoću li dobiti dva ista rezultata?". Za 1 - odgovor je ne; za 2 - možda; za 3 - da.

## Istina ili laž

**Ispred dvoja vrata stoji čovjek koji ili uvijek govori istinu ili uvijek govori laž.** Što ga trebaš pitati da bi saznao iza kojih vrata je WC?

Treba postaviti pitanje tako da lažljivce pretvori u istinu, a poštenjake ostavi istinitim. Ako pitaš "Je li WC iza lijevih vrata?", lažljivci će ti reći krivo. Ali ako pitaš: `Da te pitam 'Je li WC iza lijevih vrata?', da li bi rekao 'da'?`, lažljivac će dvaput slagati i poništiti laž.

**Isti taj čovjek odgovara na nepoznatom jeziku, gdje 'ja' i 'oui' znače 'da' i 'ne', ali ne znaš koje je koje.** Što ga sad trebaš pitati.

Ako pitaš "Da te pitam Q, da li bi rekao 'da'?", i poštenjak i lažljivac će reći 'da' ako je Q istinit. Isto vrijedi i ako pitaš "Da te pitam Q, da li bi rekao 'ne'?", i poštenjak i lažljivac će reći 'ne' ako je Q istinit. Što znači da nije bitno koristiš li 'ja' ili 'oui' - ako ti čovjek odgovori isto što si upotrijebio, znači da je Q istinit.

**Stoje tri čovjeka od koja 2 uvijek govore istinu, a 1 govori nasumično istine i laži. U dva pitanja otkrij koji je nasumični.**

Najteže je postaviti prvo pitanje - ako slučajno pitaš nasumičnu osobu, njeni odgovori su beskorisni. Zato je bitno pronaći osobu koja nije nasumična. Metoda je iduća: Osobu B pitaš: "Je li osoba A nasumična?".
- Ako odgovori da, ili je B nasumična, ili je poštena pa je A nasumična - u oba slučaja, C nije nasumična.
- Ako odgovori ne, ili je B nasumična, ili je poštena pa A nije nasumična - u oba slučaja, A nije nasumična.
Jednom kad znamo osobu koja je sigurno poštena, možemo je pitati za identitet drugih osoba.

**Stoje tri čovjeka, jedan uvijek govori istinu, jedan uvijek laže, a jedan je nasumičan. U tri pitanja saznaj koji je koji ako odgovaraju na nepoznatom jeziku.**

Kombinacijom gore navedenih metoda.

## Zatvorenici

**100 imena zatvorenika stavljena su u 100 kutija**, po jedno ime u svaku kutiju. Zatvorenici ulaze u sobu jedan po jedan, i smiju otvoriti najviše 50 kutija. Moraju ostaviti sve kako su našli i ne smiju komunicirati s ostalima. Zatvorenici će biti pošteđeni samo ako svi pronađu svoje ime. Koja je strategija s najvećom vjerojatnošću uspjeha?

Zatvorenici "dogovorno" svaku kutiju "označe" po jednim imenom. Kada zatvorenik ulazi u sobu, prvo otvara kutiju "označenu" svojim imenom. Ime koje nađe unutra koristi kao sljedeću kutiju će otvoriti, i tako dalje. Vjerojatnost uspjeha ove strategije je malo iznad `30%`.

Zašto? Praćenje imena u kutijama ga mora dovesti do kutije u kojem je njegovo ime jer ga ta vodi na kutiju od koje je počeo, zatvarajući "krug". Zatvorenik će uspjeti ako je njegov "krug" manji od 50 koraka. Permutacija kutija se zapravo sastoji od jednog ili više takvih krugova. Zatvorenici neće uspjeti samo ako u njihovoj permutaciji postoji krug veći od 50 koraka.

**100 zarobljenika su poredani jedan iza drugog i svakome se stavi crvena ili bijela kapa na glavu**. Počevši od zadnjeg, krvnik pita koje boje je njegova kapa. Ukoliko odgovori točno, pušta ga na slobodu, u suprotnom ga ubije. Koliko ih se najviše može spasiti?

99 sigurno. Prvi izbroji crvene kape ispred sebe. Ako je broj paran, kaže "bijelo", ako je neparan, kaže "crveno". Idući vidi koliko je crvenih ispred njega, ako je parnost različita, on ima crvenu kapu. Idući također i tako sve do zadnjeg.

**Varijanta**: Što ako ima 50 različitih kapa?

Dogovore se da je svaka boja kape jednaka broju 1-50. Prvi zbroji sve kape i kaže boju od `suma mod 50`. Idući također zbroji kape ispred sebe i oduzme od izgovorenog broja da dobije svoju.

**23 zatvorenika su zatvoreni u ćelijama, a stražar ih nasumično dovodi u sobu s 2 prekidača**. Oba prekidača su na početku spuštena. Kada dođe u sobu, svaki zatvorenik mora flipati točno jedan prekidač. Stražar će birati zatvorenike nasumično, pa neki mogu biti više puta u sobi prije nego drugi dođu. Zatvorenici će biti slobodni kad netko izjavi da su svi bili u sobi, ali ako pogriješi, svi će biti pogubljeni. Koja je strategija?

Jedan se zatvorenik odabere kao "brojač". Lijevi prekidač će se koristiti za brojanje - svaki zatvorenik ga mora jednom flipati iz 0 u 1, a brojač će ga flipati nazad u 0, i zabilježiti +1 u broj ljudi koji je bio u sobi. Drugi prekidač je lažnjak, kojeg će svi flipati ako nemaju što drugo za napraviti. Kada brojač izbroji do 22, znači da su svi bili.

**Što ako je početno stanje prekidača nepoznato?** Ako brojač uđe u prostoriju i vidi da je lijevi prekidač na 1, neće znati je li netko već bio unutra, ili je to početno stanje. Zato će, za svaki slučaj, svatko morati dvaput flipati lijevi prekidač, a brojač broji do 44.

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

## Čuvanje informacija

**Želiš poslati paket prijatelju, a obojica imate svoj ključ i lokot.** Kako mu poslati pošiljku bez da zli poštari mogu vidjeti što je u njoj?

Ti staviš svoj lokot na paket, pošalješ ga njemu. On doda svoj lokot, pošalje paket nazad tebi. Ti makneš svoj lokot, pošalješ ga nazad njemu. To je Diffie-Hellman algoritam!

**Kako 5 zaposlenika mogu izračunati prosječnu plaću, bez da itko sazna kolika je nečija plaća?**

Prvi zaposlenik zapiše na papir svoju plaću plus neki nasumični broj X. Zatim ga proslijedi idućem, koji na novi papir zapiše taj broj uvećan za svoju plaću, i tako dalje. Na kraju ukupna suma dođe do prvog, koji samo oduzme X iz nje, i podijeli s 5.

## Lanci i konopi

**Imaš četiri lanca od 4, svaki od 3 povezane karike. Koliko puta moraš rezati i variti karike, da bi od njih stvorio zatvoreni prsten od 12 karika?**

Na prvu se čini da ćeš morati rezati i variti 4 puta (za svaki kraj lanca), ali zapravo je dovoljno 3: jednom lancu odrežeš svaku kariku, i te tri iskoristiš da spojiš ostala 3 lanca u prsten.

**Konop je simetrično namotan oko štapa opsega 4cm i dužine 12cm, tako da od jednog kraja do drugog ide točno 4 puta oko njega. Kolika je dužina konopa?**

Ako promatramo četvrtinu štapa, primjetit ćemo da konop zapravo ide po dijagonali plašta valjka, koja je duga 5cm. Znači ukupno 20cm.

**Imaš dva jednaka komada fitilja. Svakom komadu treba sat vremena da izgori, ali ne gori jednolično (tj. pola fitilja neće nužno goriti pola sata). Kako izmjeriti 15 minuta?**

Da bi izmjerio 30 minuta: zapališ fitilj s obje strane. Gorit će duplo brže, i cijeli će izgoriti za 30 minuta. Da bi izmjerio 15 minuta: zapališ prvi fitilj da mjeri 30 minuta. Zapališ prvi da gori jednostrano, a kad prođe 30 minuta (tj. kad mu je preostalo još 30 minuta) zapališ mu i drugu stranu - ostatak će goriti točno 15 minuta.

## Vjerojatnosti

**100 osoba ulazi u avion. Prva osoba je izgubila kartu, pa sjedne na nasumično mjesto. Svaka iduća osoba sjedne na svoje mjesto, a ako je ono zauzeto, sjedne na nasumično mjesto. Ti si posljednji putnik koji ulazi - koja je vjerojatnost da ćeš sjesti na svoje mjesto?**

Vjerojatnost je 1/2. Zbog jednostavnosti zamislimo da svaki putnik koji nađe zauzeto mjesto, umjesto traženja novog, izbaci osobu sa svog mjesta. U tom slučaju će se prvi putnik samo seliti s mjesta u mjesto sve dok ne dođe ili do svoga, ili do tvoga mjesta. Vjerojatnosti za to su jednake.

**Mr. Black, Mr. Brown and Mr. White stoje u trokutu i po redu pucaju jedan na drugog. Prvo puca Mr. White, koji pogađa u 1/3 pokušaja, pa Mr. Brown koji pogađa u 2/3, pa Mr. Black koji pogađa svaki put. Koja je strategija Mr. Whitea?**

Mr. White treba pucati u zrak. Mr. Brown će tada pucati u Mr. Blacka (s 2/3 šanse da ga ubije), ili će Mr. Black ubiti Mr. Browna. Zatim će imati šansu 1/3 da preživi, ako ubije jedinog preostalog.

**Na tri papira napisani su različiti brojevi. Možeš uzeti jednog, pogledati broj, i odlučiti se da li ćeš ga zadržati. Ako ne, uzimaš idućeg i tako dalje. Koja je najbolja strategija da zadržiš najveći broj?**

Nasumičnim odabirom i zadržavanjem vjerojatnost da ćeš odabrati najveći je 1/3. Ali ako odbaciš prvi, a drugi zadržiš ako je veći od prvog, šansa ti se povećava na 1/2.

**U vreći su tri kartice: crvena s obje strane, zelena s obje strane, i crveno-zelena. Izvučem jednu i stavim je na stol - s jedne strane je crvena. Koja je vjerojatnost da je crvena i s druge strane?**

Kartica na stolu može biti crvena-crvena ili crvena-zelena. Vjerojatnost je 2/3, jer se dvije crvene strane nalaze na crvena-crvena kartici.

# Literatura

* https://www.sciencenews.org/article/when-intuition-and-math-probably-look-wrong


