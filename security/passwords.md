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
 - Brute force napadi se jednako lako mogu izvesti bez pasteanja.
 - Malware koji ti krade clipboard ti može jednako pratiti koje tipke ukucavaš.
 - Zabraniti paste zbog limita duljine - nema smisla ograničavati duljinu passworda. Password bilo koje duljine uvijek će se pretvoriti u hash iste duljine.


## Password Reset
* Kada napraviš password reset, invalidiraj sve postojeće sessione
* Ako user može promijeniti email, zahtjevaj da ponovno unese password. U suprotnom može napraviti password reset bez da zna stari.


## Timing Attacks
Zaključivanje prema koliko dugo treba serveru da odgovori (npr. usporedba stringova, treba koristiti `Rack::Utils.secure_compare` koji uspoređuje uvijek u konstantom vremenu). Iako, zbog šuma u networku ovo je jako teško izvesti u praksi.


## Password cracking
http://hashcat.net/hashcat/ - alat za sve žive hasheve.
