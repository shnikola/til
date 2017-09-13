# Passwords

## Password keeping

U trenutku kada napadač dođe do passworda tvojih usera, tvoj sustav je popustio napadu. Ali i dalje želiš zaštiti što više korisničkih podataka, poput kreditnih kartica (koje nikako nećeš držati u svojoj bazi) i passworda koje možda reusaju na svojim drugim računima.

Passworde zato čuvaj hashane sporom hash funkcijom (`bcrypt` ili `scrypt`). One u sebi uključuju i salt koji će prisiliti napadača da razbija hash po hash, a ne sve hasheve odjednom (pomoću *rainbow tablica*).

Nemoj stavljati nikakva ograničenja osim minimalne duljine passworda. Eventualno dodaj sanity check na, npr. `1000` maksimalnih znakova kako netko ne bi poslao 1GB podataka tvojoj hash funkciji.

Ograniči broj pokušaja logina na neki broj (npr. 100) u intervalu od 24 sata. Ograniči *po lokaciji* - ne želiš zaključati korisnika zato što mu je račun napadnut s drugog IP-a. Sve pokušaje bilježi i omogući korisniku da ih vidi, ili pošalji email tipa "bilo je X neuspjelih pokušaja logina na tvoj račun".

Za osjetljive račune, dodaj 2 Factor Authentication, ili barem email verification ako se korisnik spaja s nepoznate lokacije.

## Pasting in password fields

https://www.troyhunt.com/the-cobra-effect-that-is-disabling

Jedini sigurni password je onaj kojeg ne možeš zapamtiti. Zato je nužno omogućiti paste iz nekog password managera.

Nijedan argument protiv pasteanja ne drži vodu:
* Brute force napadi se jednako lako mogu izvesti bez pasteanja.
* Malware koji ti krade clipboard ti može jednako pratiti koje tipke ukucavaš.
* Zabraniti paste zbog limita duljine - nema smisla ograničavati duljinu passworda. Password bilo koje duljine uvijek će se pretvoriti u hash iste duljine.

## Forgotten Password

Token koji generiraš za reset passworda prefiksaj s user id, kako bi smanjio šansu nasumičnog pogađanja.

## Password change

Kada korisnik promjeni password, invalidiraj mu sve postojeće sessione.

Pošalji korisniku mail kada je promijenjen password, kako bi saznao u slučaju da je to napravio napadač.

Ako user može promijeniti email, zahtjevaj da ponovno unese password. U suprotnom može napraviti password reset bez da zna stari.

## Timing Attacks

Doznavanje informacija prema tome koliko dugo treba serveru da odgovori. Čest primjer je enumeracija korisničkih emailova - napraviš li puno login pokušaja, primjetit ćeš da emailovi koji ne postoje traju kraće od onih kojih postoje, jer se za njih ne treba provjeravati ispravnost passworda.

Iako je zbog latencije u mreži ovo teško izvesti u praksi, Rack dodaje `X-Runtime` header s vremenom izvođenja na serveru koji uvelike pomaže napadaču.

Kako bi se otežala vremenska procjena koristi `Rack::Utils.secure_compare` koji uspoređuje uvijek u konstantom vremenu.

# Tools

* http://hashcat.net/hashcat/ - alat za sve crackiranje password hasheva.

