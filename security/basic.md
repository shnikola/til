# Security
TODO:
session hijacking vs CSRF vs clickjacking
dns hijacking
dns spoofing


## Enough With the Salts: Updates on Secure Password Schemes
https://www.nccgroup.trust/us/about-us/newsroom-and-events/blog/2015/march/enough-with-the-salts-updates-on-secure-password-schemes
Salting passworda je nužan, ali nije ni približno dovoljan da budeš siguran danas. Ono što je nužnije je spora hash funkcija. Preporuča se **bcrypt** ili **scrypt**. Također im treba odrediti valjani work factor (http://security.stackexchange.com/questions/3959/recommended-of-iterations-when-using-pkbdf2-sha256/3993#3993) kako bi bile dovoljno brze za tvoj server a dovoljno spore za napadača.


## DNS Rebinding
http://bouk.co/blog/hacking-developers
Zli server ima registriranu domenu s jako kratkim TTLom, koja se zbog toga ne cacheira. Kada se korisnik prvi put spoji na server, on pomoću `iptables` zamjeni svoju IP adresu s nekom zlobnom, i svi requestovi se onda šalju tamo. Browser misli da i dalje vrijedi same-origin policy.

Kreativni napad je zamjeniti svoju IP adresu s *korisnikovom privatnom*, npr. `127.0.0.1` ili `192.168.0.1`. Nakon toga možeš slati requestove na korisnikove lokalne servise koji se vrta ne nekom portu, npr. `Redis` ili `ElasticSearch`.

Potencijalna obrana od ovog je da browseri uvedu *DNS pinning*, tj. da uvijek koriste IP koji su dobili u prvom requestu.


## Timing Attacks
Zaključivanje prema koliko dugo treba serveru da odgovori (npr. usporedba stringova, treba koristiti `Rack::Utils.secure_compare` koji uspoređuje uvijek u konstantom vremenu). Iako, zbog šuma u networku ovo je jako teško izvesti u praksi.
