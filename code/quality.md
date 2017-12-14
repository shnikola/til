# Code Quality

## Responsibility

Za nered u aplikaciji nisu krivi manageri koji forsiraju nemoguć ritam dodavanja featura. Kriv si ti, jer si odgovoran da managerima pružiš povratnu informaciju - da se ne nešto ne stigne ili se ne može dodati. Njihov posao je da brane zahtjeve i raspored, tvoj posao je da braniš kvalitetu koda.

Perhaps you thought that “getting it working” was the first order of business for a professional developer. The functionality that you create today has a good chance of changing in the next release, but the readability of your code will have a profound effect on all the changes that will ever be made. The coding style and readability set precedents that continue to affect maintainability and extensibility long after the original code has been changed beyond recognition. Your style and discipline survives, even though your code does not.

## 10 Modern Software Over-Engineering Mistakes

Business uvijek pobjeđuje development. Zahtjevi uvijek divergiraju.

Preferiraj izolirane akcije umjesto reusanja business logike, koja će se sigurno mijenjati.

Prije dodavanja featura razmisli hoće li se koristiti (npr. je li stvarno potreban CMS ili OAuth login)

### Meaningful names

Avoid words like Manager, Processor, Data, or Info in the name of a class.

Pick One Word per Concept: it’s confusing to have fetch, retrieve, and get as equivalent methods of different classes. Likewise, it’s confusing to have a controller and a manager and a driver in the same code base. What is the essential difference between a DeviceManager and a Protocol- Controller?

### Functions

Miješanje razina apstrakcije zbunjuje. Čitatelju neće biti jasno je li određeni izraz esencijalni koncept ili samo detalj. Svaka funkcija bi se trebala baviti samo jednom razinom apstrakcije: high level odluka da li dodati meta tag u html i low-level zapisivanje `\n` u string ne bi smjeli biti u istoj funkciji.

Što više argumenata funkcija prima, to ju je teže razumjeti. Izbjegavaj funkcije s više od 3 argumenta. Više povezanih argumenata zamijeni objektom.

Izbjegavaj output argumente, tj. funkcije koje mijenjaju stanje argumenata. Umjesto `appendFooter(report)` radije koristi jasniji `report.appendFooter()`.

Izbjegavaj selector argumente, npr. boolean koji mijenja funkcionalnost funkcije. Bolje imati dvije funkcije nego funkciju koja prima vrijednost koja opisuje njeno ponašanje.

Funkcija bi trebala ili napraviti nešto ili odgovoriti na neko pitanje - nikad oboje. Npr. `if (set("username", "unclebob"))` je zbunjujuća naredba jer nije jasno u kojem slučaju daje koji rezultat.

Kad napišeš funkciju koja vraća `null`, stvaraš dodatni posao onome koji tu funkciju poziva da svaki put mora provjeriti što je dobio nazad. Umjesto toga, baci exception ili vrati null-objekt. `null` se nikad ne bi smio slati ni kao argument funkcije.

Ako želiš naglasiti da se funkcije smiju pozivati samo određenim redoslijedom (npr. `calculatePay`, `emailPaychecks`), proslijedi rezultat izvršene funkcije u sljedeću.

## Comments

Jedina namjena komentara je da pomognu kada se ne možeš jasno izraziti pomoću koda. U tom smislu, komentari su uvijek znak neuspjeha - što je manje komentara u kodu, to je kod jasniji. Ako je kod loš, umjesto da napišeš komentar, učini ga boljim.

Što je komentar stariji ili udaljeniji od koda na koji se odnosi, to je veća vjerojatnost da postane neistinit. Istina je jedino u kodu, sve ostalo je šum.

Komentari ne bi smjeli sadržavati meta podatke poput autora, datuma posljednje promjene i changeloga - za to postoji version control sustav.

Komentirani kod je najgori od svih jer s vremenom postaje sve nerelevantniji, a nitko ga se ne usudi obrisati misleći da ga netko planira koristiti. Samo ga obriši bez straha, version control će ga zapamtiti.

## Classes

Klase bi trebale biti dovoljno male da se njihovo ponašanje može opisati pomoću dvadesetak riječi, bez korištenja "i", "ili", "ali" i "ako".

*Single responsibility principle* kaže da svaka klasa ili modul smije imati samo jednu odgovornost, tj. samo jedan razlog da se mijenja. Developeri često krše to pravilo jer im se čini je teško shvatiti sustav s mnogo malih komponenata. Međutim, imao puno malih klasa ili par golemih, jednaka je količina koda koju moraš shvatiti - pitanje je samo je li lakše imati više malih, dobro označenih ladica ili dvije velike ladice u kojima držiš sve živo.

Bazna klasa ne bi smjela ovisiti o svojim podklasama. Poanta stvaranja bazne klase je da odvoji high level koncept od implementacija, a ne da ovisi o njima.

# Concurrency

Concurrency decoupla "što se izvršava" i "kada se izvršava". Dizajn programa se znatno mijenja i postaje složeniji. Zato je bitno održati single responsibility principle i odvojiti concurrency logiku od ostatka koda.

Svaki thread bi trebao biti neovisan od ostalih. Pristup dijeljenim podacima izbjegavaj koliko god je moguće, a umjesto toga koristi kopije podataka.

Što ranije počni razmišljati o shut-down strategiji, jer se tu stvaraju najsloženiji rubni slučajevi.

## Refactoring

Jedini razlog refaktoriranja koda je kako bi ga se lakše mijenjalo u budućnosti.

Počni od duplikacije pa pojednostavni. Duplikacija je jeftinija od pogrešne apstrakcije.

Refactoring treba biti "safe and boring" - puno testova koje nakon svake pojedine promjene pokrećeš.

Napiši najbolji mogući kod sada, ali budi spreman da ga obrišeš sutra.

## Simple Made Easy

https://www.infoq.com/presentations/Simple-Made-Easy

Teži jednostavnosti (*simple*): da sve što radiš ima jednu ulogu, predstavlja jedan koncept i da stoji samostalno.

Ne miješaj to s lakoćom (*easy*): da se može brzo instalirati, da ti je poznato ili da će svima biti jasno isprve.

Modularnost ne znači jednostavnost - nemoj da te organizacija koda prevari. Ako samo odvojiš dva sustava, oni su i dalje kompleksni.

"Information is simple. Don't ruin it."

# Literatura

* Clean Code by Robert C. Martin - Odlična knjiga pisana čitkim i razgovornim ugodnim tonom. Iako fokusirana na Javu, sadrži mnogo korisnih primjera iz stvarnog svijeta i dokaza da se kod uvijek može popraviti.
* https://medium.com/@rdsubhas/10-modern-software-engineering-mistakes-bc67fbef4fc8

