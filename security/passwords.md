# Passwords

## Enough With the Salts: Updates on Secure Password Schemes
https://www.nccgroup.trust/us/about-us/newsroom-and-events/blog/2015/march/enough-with-the-salts-updates-on-secure-password-schemes
Salting passworda je nužan, ali nije ni približno dovoljan da budeš siguran danas. Ono što je nužnije je spora hash funkcija. Preporuča se **bcrypt** ili **scrypt**. Također im treba odrediti valjani work factor (http://security.stackexchange.com/questions/3959/recommended-of-iterations-when-using-pkbdf2-sha256/3993#3993) kako bi bile dovoljno brze za tvoj server a dovoljno spore za napadača.


## Pasting in password fields
https://www.troyhunt.com/the-cobra-effect-that-is-disabling
Jedini sigurni password je onaj kojeg ne možeš zapamtiti. Zato je nužno omogućiti paste iz nekog password managera.
Nijedan argument protiv pasteanja ne drži vodu:
 - Brute force napadi se jednako lako mogu izvesti bez pasteanja.
 - Malware koji ti krade clipboard ti može jednako pratiti koje tipke ukucavaš.
 - Zabraniti paste zbog limita duljine - nema smisla ograničavati duljinu passworda. Password bilo koje duljine uvijek će se pretvoriti u hash iste duljine.


## Timing Attacks
Zaključivanje prema koliko dugo treba serveru da odgovori (npr. usporedba stringova, treba koristiti `Rack::Utils.secure_compare` koji uspoređuje uvijek u konstantom vremenu). Iako, zbog šuma u networku ovo je jako teško izvesti u praksi.


## Password cracking
http://hashcat.net/hashcat/ - alat za sve žive hasheve.
