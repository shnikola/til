# Object Oriented Programming

## Data Structures vs Objects

Objekti skrivaju podatke iza apstrakcija i pružaju funkcije koje rade s podatcima. Data strukture otkrivaju podatke i nemaju funkcija.

U proceduralnom kodu (koji koristi data strukture) funkcije su zadužene za prepoznavanje i manipuliranje tipovima podataka. Lako je dodati nove funkcije jer se postojeće strukture ne moraju mijenjati, ali teško je dodati nove strukture jer treba mijenjati sve postojeće funkcije.

U OO kodu svaka klasa sadrži vlastitu implementaciju funkcije. Lako je dodati novi tip podatka jer se ostale klase ne moraju mijenjati, ali teško je dodati nove funkcije jer ih trebaš dodati u sve klase.

Koristi jedne ili druge ovisno o promjenama koje očekuješ.

## Desing Patterns

### Template Method

Kompleksna metoda mora vratiti HTML u jednom slučaju, a TXT u drugom. Možemo proslijediti parametru i koristiti `if`ove, ali to će učiniti kod složenijim.

Umjesto toga, zajedničko ponašanje metode stavi se u apstraktnu klasu `Report` koju onda nasljeđuju `HtmlReport` i `TxtReport` u kojima se implementiraju razlike.

### Strategy

Opet, metoda mora vratiti HTML u jednom slučaju, a TXT u drugom. Umjesto nasljeđivanja, metodi prosljeđujemo strategiju, npr. klase `HtmlFormatter` ili `TxtFormater` koje imaju isti interface. Njih će metoda koristiti da dobije dio koji se razlikuje. Ovime se `Report` klasa pojednostavljuje.

### Command

Imamo `SaveButton` klasu koja se bavi iscrtavanjem buttona u UI. Na click bi trebali saveati dokument, ali ne želimo tu kompleksnu logiku držati u buttonu.

Save dokumenta stavljamo u `SaveCommand` klasu pod `execute` metodu. Button inicijaliziramo s `SaveButton.new(SaveCommand.new)` gdje on samo koristi  `execute` interface ne morajući znati o kojoj se klasi radi. Usto, u `SaveCommand` možemo lako dodati undo funkcionalnost s `unexecute`.

### Observer

Imamo model `Employee` i želimo poslati notifikaciju `Payroll` sistemu svaki put kad mu se promjeni plaća. Ako dodamo `Payroll` referencu u model, `Employee` će ovisiti o njegovom interfaceu. Dodatno, za svaki sustav koji budemo kasnije htijeli notificirati po promijeni plaće, morat ćemo opet mijenjati model.

Umjesto toga, u `Employee` dodajemo array observera s istim interfaceom (`notify(self)`) koji ćemo zvati kada se plaća promjeni. Na taj način možemo jednostavno dodavati i brisati observere ne brinući o kojim se klasama radi.

### Proxy

Imamo `BankAccount` klasu s metodama za transakcije i želimo dodati provjeru je li korisnik ispravno logiran prije nego dopustimo ijednu od tih metoda. Umjesto da dodajemo autentifikacijsku logiku u klasu, možemo napraviti *Protection Proxy* klasu koja provjerava login i onda prosljeđuje metode u `BankAccount`.

Ako se objekt treba dohvatiti s mreže, možemo napraviti *Remote Proxy* koji će handlati konekciju i dohvaćanje podataka, i proslijeđivati ostale metode objektu.

### Adapter

Metoda prima IO objekt, a mi joj želimo poslati string. Stoga wrapamo String u `StringIOAdapter` koji implementira IO interface koje će metoda zvati i prosljeđuje ih Stringu.

### Decorator

Imamo klasu kojoj ne želimo dodavati nove funkcionalnosti. Wrapamo je u Decorator klasu koja ima isti interface, ali izvrši dodatne naredbe prije ili nakon što proslijedi poziv metode osnovnoj klasi.

