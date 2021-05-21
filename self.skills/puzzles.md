# Puzzle hunt

## Sastavljanje

Cilj puzzle hunta je da se igrači **zabave**. Kompetitivnim ekipama je možda pobjeda zabavna, dok je drugima zabavno riješiti par zagonetki i pratiti priču. U oba slučaja, pri sastavljanju hunta glavni cilj treba biti omogućiti pozitivno iskustvo svakom tipu igrača.

Za svaku zagonetku koji kreiraš, zapitaj se - hoće li rješavaču ovo biti zabavno?

Ako je zagonetka dvoboj između sastavljača i rješavača, onda je zadatak sastavljača da pruži izazov, ali u konačnici **izgubi**.

Dobri puzzli imaju hook koji će privući igrača i dati mu nešto od čega može početi. Također je dobro imati način na koji igrač može tijekom rješavanja znati da je na pravom putu (npr. standardna križaljka je puna mini-provjera).

Ukloni sve **nepotrebne informacije**. Rješavači će pokušavati naći uzorke i značenja u svemu što ima daš. Ako su clueovi zadani nasumičnim redom, pokušat će shvatiti redoslijed, ili još gore, slučajno u njemu pronaći nešto. Ako ih zadaš clueove abecednim redom, to će im dati do znanja da redoslijed nije bitan.

Igrači se frustiraju kada ne uspjevaju postići nikakav napredak. Najčešće je to jer ne znaju što iduće napraviti, ili nakon što su napravili puno posla bez razumljivog rezultata. Laka zagonetka je bolja od frustirajuće zagonetke. Zato griješi uvijek na stranu ostavljanja dodatnih clueova.


## Rješavanje

Obrati pozornost na čudne riječi ili nespretne fraze u tekstu zagonetke. Velika je vjerojatnost da su ključne za riješavanje.

Traži uzorke. Izbroj elemente i probaj naći isti broj na nekom drugom mjestu.

Česte mehanike:
* skrivena riječ, anagramiranje, slovo viška
* abecede: braille, morse, pigpen, semaphore
* šifre: A1Z26, rot-13, caesar, substitution

## Spreadsheets

Koristi monospace font.

Korisne excel naredbe:
`MID(A1, B1, 1)` vraća slovo na mjestu B1.
`CHAR(64+A1)` pretara broj u slovo (A1Z26 sub).
`REGEXREPLACE(A9, "[^A-Za-z]", "")` ostavlja samo slova.

`INDEX(B, 5)` vraća zapis iz B5.
`MATCH(B1, A:A, 0)` vraća indeks stupca A koji ima vrijednost B1.

Vodi zapisnik o svim metodama koje si isprobao.
Umjesto komentara stavljaj notes da izbjegneš email spam.

## Identify, Sort, Index, Solve

Klasični oblik zagonetke u huntovima. Dobiješ listu rečenica/slika/snimki, koju trebaš redom:
1) Identificirati što su, pronađi zajednički atribut (npr. opisuju simpson couch gagove)
2) Sortiraj po nekom redu (npr. po broju epizode)
3) Indeksiraj po nekom brojčanom atributu (npr. po broju likova koji se spominju)
4) Extraktiraj poruku da dobiješ rješenje ili novi clue.

Ako ne postoji atribut za indeksiranje, probaj s: prvim slovima, zadnjim slovima, dijagonalom, brojem riječi.

ISIS puzzli su fleksibilni i laki za sastavljanje, ali iskusnijim zagonetačima znaju biti dosadni. Najzabavniji dio rješavanje je korak identificiranja, pa se potrudi da clueovi budu zanimljivi i ne nešto što se može lako googlati. Potrudi se i oko prezentacije (npr. umjesto "prepoznaj Game of Thrones lika po citatu", napravi "Crow facts" servis).

# Words Only

Ako imaš zadane same riječi bez usmjerenja, pokušaj ih oblikovati na neki način. Provjeri jesu li jednake duljine, ili ako ih možeš ulančati sa zajedničkim slovima.

## US Crossword

Osnovna pravila za klasičnu 15x15 križaljku:

**Grid** mora biti simetričan, dobro povezan (dijelovi ne smiju biti izolirani), i s manje od 17% crnih polja.

**Riječi** smije biti do 78 pojmova, 72 ako ne postoji tema. Što je manje pojmova, križaljka je veći izazov za složiti i riješiti. Riječi se ne smiju ponavljati unutar iste križaljke.

**Teme** uobičajeno povezuju najduže odgovore na neki način. Odgovori npr. mogu biti naslovi određenog autora, ili nekakva igra riječi.

Kada slažeš križaljku, imaj na umu simetriju - ako imaš tematsku riječ dugu 11 slova, morat ćeš imati još jednu, ili će ta riječ ići u sredinu grida. Nakon pozicioniranja tematskih pojmova, postavi crna polja gdje su potrebna i ispuni ostatak.

## Cryptic Crossword

Svaki cryptic se sastoji od "straight" dijela (standardnog crossword cluea) i "cryptic" cijela (neka komplicirana igra riječi koja isto dovodi do odgovora).

Neke od cryptic metoda su:

**Anagrami:** korištenje destruktivnih riječi ("runied", "destroyed"), izmijenjenog stanja ("loony", "drunk") ili rekonfiguracije ("redesigned", "novel"). Npr. `<Honestly crazy>, in secret` -> `On the sly`.

**Hidden word:** riječ skrivena u samom izrazu ("includes", "entertains", "has"). Npr. `Error <concealed by city police>` -> `Typo`.

**Homophone:** riječ koja zvuči kao rješenje ("heard", "to an audience", "said"). Npr. `Stringed instrument <untruthful person heard>` -> `Lyre`.

**Double definition:** dane su dvije definicije koje imaju isto rješenje. Npr. `Wear out an important part of a car` -> `Tire`.

**Assemblage:** dijeli rješenje na djelove i svaki posebno cluea. Npr. `<Put down prosecutor’s> animals` -> `Pandas`

**Deletion:** uklanja se dio riječi ("headless", "first off", "half", "endlessly"). Npr. `Creed of Christianity is <75% niceness>` -> `Nicene`.

**Reversal:** riječ se piše unazad ("turned back", "in reverse", "going up"). Npr. `<Returned beer> of kings` -> `Regal`.

**Container:** riječ se stavlja u drugu riječ ("within", "going into", "surrounding", "wrapping"). Npr. `<Horse in South Dakota> is covered with spangles` -> `Sequined`.

**Bits and Pieces:** uzima se akronim riječi. Npr. `<Starts of many a yearly> month` -> `May`.

Cryptic je popularan UK-u, ne toliko u SAD-u.

# Literatura

* http://web.mit.edu/dwilson/www/puzzles/puzzlewriting.html
