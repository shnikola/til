# Code Quality

Pisanje software nije građevina, nego vrtlarstvo - mnogo je više organsko nego druge inženjerske grane.

## Kompleksnost

Temeljni problem u programiranju je problem dekompozicije: kako kompleksan zadatak podijeliti na manje zadatke koji se mogu neovisno riješiti.

Kompleksnost je najveći neprijatelj programera. Dva su načina borbe protiv kompleksnosti: pojednostavljivanje koda (npr. eliminiranje posebnih slučajeva, dobro imenovanje varijabli) ili enkapsuliranje (skrivanje kompleksnosti u modulu).

## Simple vs Easy

Ne miješaj jednostavnost (*simple*) s lakoćom korištenja (*easy*). Teži jednostavnosti, a tek onda lakoći korištenja.

**Jednostavnost** znači da komponenta ima jednu ulogu, predstavlja jedan koncept i stoji samostalno.

**Lakoća korištenja** znači da se komponenta može brzo instalirati, da ti je poznata ili da će svima biti jasno isprve.

Modularnost ne znači jednostavnost - nemoj da te organizacija koda prevari. Ako samo odvojiš dva sustava, oni su i dalje kompleksni.

## Kvaliteta koda u stvarnom svijetu

Za nered u aplikaciji nisu krivi project manageri koji forsiraju nemoguć ritam dodavanja featura. Kriv si ti, jer si odgovoran da managerima pružiš povratnu informaciju - da se ne nešto ne stigne ili se ne može dodati. Njihov posao je da brane zahtjeve i raspored, tvoj posao je da braniš kvalitetu koda.

Suprotno čestom mišljenju, glavna dužnost profesionalnog programera nije natjerati program da radi. Današnja funkcionalnost će se vjerojatno promijeniti u sljedećoj verziji, ali čitljivost koda će utjecati na sve promjene koje će se u budućnosti raditi. Čak i kad i izvornog koda više nema, njegova struktura i kvaliteta ostaju.

**Business uvijek pobjeđuje development**. Zahtjevi uvijek divergiraju. Zato preferiraj izolirane akcije umjesto reusanja business logike, koja će se sigurno mijenjati.

Svejedno, kao developeru zadatak ti je da budeš sanity check. Prije dodavanja featurea dobro razmisli hoće li se koristiti (npr. je li stvarno potreban CMS ili OAuth login).

## Duplikacija vs apstrakcija

Duplikacija je naporna i čest izvor bugova. Ali ako duplikaciju zamijeniš pogrešnom apstrakcijom, uvođenje promjena i novih featurea će postati puno teže. Ekstrakciju zajedničkog koda radi samo ako će taj kod sada i zauvijek ostati nepromijenjen.

U ranim fazama razvoja, cijeli projekt je u stalnoj promjeni. Zato uvijek na kreni s duplikacijom. Duplikacija je mnogo jeftinija od pogrešne apstrakcije.

Treba naglasiti da se DRY koncept ne odnosi na proceduralni kod, nego na znanje. Da li će negdje dvaput pisati `if` je nebitno. Bitno je da se business logika ne duplicira, jer se često mijenja i njena duplikacija često dovodi do bugova.

## Refactoring

Jedini razlog refaktoriranja koda je kako bi ga se lakše mijenjalo u budućnosti. Refactoring treba biti "safe and boring" - puno testova koje nakon svake pojedine promjene pokrećeš.

Napiši najbolji mogući kod sada, ali budi spreman da ga obrišeš sutra.

## Imenovanje

Izbjegavaj riječi poput `Manager`, `Processor`, `Data`, i `Info` u imenima klasa.

Odluči se za jednu riječ po konceptu: zbunjujuće je imati `fetch`, `retrieve`, i `get` kao ekvivalentne metode u različitim klasama, ili `controller`, `manager` i `driver` kao različite klase.

## Metode

Miješanje razina apstrakcije zbunjuje. Čitatelju neće biti jasno je li određeni izraz esencijalni koncept ili samo detalj. Svaka metoda bi se trebala baviti samo jednom razinom apstrakcije: high level odluka da li dodati meta tag u html i low-level zapisivanje `\n` u string ne bi smjeli biti u istoj metodi.

Što više argumenata metoda prima, to ju je teže razumjeti. Izbjegavaj metode s više od 3 argumenta. Više povezanih argumenata zamijeni objektom.

Izbjegavaj defenzivno kodiranje, tj. provjeravanje argumenata koje si primio. Argumenti bi se trebali provjeravati na jednom mjestu, kada ulaze u sistem. Ostatak koda bi trebao raditi pod pretpostavkom da su argumenti valjani.

Izbjegavaj output argumente, tj. metode koje mijenjaju stanje argumenata. Umjesto `appendFooter(report)` radije koristi jasniji `report.appendFooter()`.

Izbjegavaj selector argumente, npr. boolean koji mijenja funkcionalnost funkcije. Bolje imati dvije funkcije nego funkciju s parametrom koji opisuje njeno ponašanje.

Metoda bi trebala ili napraviti nešto ili odgovoriti na neko pitanje - nikad oboje. Npr. `if (set("username", "unclebob"))` je zbunjujuća naredba jer nije jasno u kojem slučaju daje koji rezultat.

Kad napišeš metodu koja vraća `null`, stvaraš dodatni posao onome koji tu metodu poziva da svaki put mora provjeriti što je dobio nazad. Umjesto toga, baci exception ili vrati null-objekt. `null` se nikad ne bi smio slati ni kao argument metode.

Ako želiš naglasiti da se metode smiju pozivati samo određenim redoslijedom (npr. `calculatePay`, `emailPaychecks`), proslijedi rezultat izvršene funkcije u sljedeću.

Izbjegavaj razbijati kod na previše sitnih metoda, jer to uvodi dodatna sučelja i povećava kompleksnost. Sučelje svake metode bi trebalo biti jednostavnije od njene implementacije.

## Komentari

Jedina namjena komentara je da pomognu kada se ne možeš jasno izraziti pomoću koda. U tom smislu, komentari su uvijek znak neuspjeha - što je manje komentara u kodu, to je kod jasniji. Ako je kod loš, umjesto da napišeš komentar, učini ga boljim.

Što je komentar stariji ili udaljeniji od koda na koji se odnosi, to je veća vjerojatnost da postane neistinit. Istina je jedino u kodu, sve ostalo je šum.

Komentari ne bi smjeli sadržavati metapodatke poput autora, datuma posljednje promjene i changeloga - za to postoji version control sustav.

Zakomentirani kod je najgori od svih jer s vremenom postaje sve nerelevantniji, a nitko ga se ne usudi obrisati misleći da ga netko planira koristiti. Samo ga obriši bez straha, version control će ga zapamtiti.

## Klase

Klase bi trebale biti dovoljno male da se njihovo ponašanje može opisati pomoću dvadesetak riječi, bez korištenja "i", "ili", "ali" i "ako".

*Single responsibility principle* kaže da svaka klasa ili modul smije imati samo jednu odgovornost, tj. samo jedan razlog da se mijenja. Developeri često krše to pravilo jer im se čini je teško shvatiti sustav s mnogo malih komponenata. Međutim, imao puno malih klasa ili par golemih, jednaka je količina koda koju moraš shvatiti - pitanje je samo je li lakše imati više malih, dobro označenih ladica ili dvije velike ladice u kojima držiš sve živo.

Bazna klasa ne bi smjela ovisiti o svojim podklasama. Poanta stvaranja bazne klase je da odvoji high level koncept od implementacija, a ne da ovisi o njima.

## Concurrency

Concurrency decoupla "što se izvršava" i "kada se izvršava". Dizajn programa se znatno mijenja i postaje složeniji. Zato je bitno održati single responsibility principle i odvojiti concurrency logiku od ostatka koda.

Svaki thread bi trebao biti neovisan od ostalih. Pristup dijeljenim podacima izbjegavaj koliko god je moguće, a umjesto toga koristi kopije podataka.

Što ranije počni razmišljati o shut-down strategiji, jer se tu stvaraju najsloženiji rubni slučajevi.

## Exceptioni

Posebni slučajevi uvijek čine kod kompleksnijim, a exceptioni su najčešći izvor posebnih slučajeva.

Prvo pokušaj redefinirati problem da eliminiraš slučajeve koji stvaraju exceptione. Ako to ne možeš, pokušaj ih maskirati u nižim razinama da ograničiš njihov utjecaj. Više exceptiona agregiraj u jedan zajednički.

# Literatura

* Clean Code by Robert C. Martin - Odlična knjiga pisana čitkim i razgovornim ugodnim tonom. Iako fokusirana na Javu, sadrži mnogo korisnih primjera iz stvarnog svijeta i dokaza da se kod uvijek može popraviti.
* https://www.infoq.com/presentations/Simple-Made-Easy
* https://medium.com/@rdsubhas/10-modern-software-engineering-mistakes-bc67fbef4fc8

