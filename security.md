# Security

## Enough With the Salts: Updates on Secure Password Schemes
https://www.nccgroup.trust/us/about-us/newsroom-and-events/blog/2015/march/enough-with-the-salts-updates-on-secure-password-schemes/

Salting passworda je nužan, ali nije ni približno dovoljan da budeš siguran danas. Ono što je nužnije je spora hash funkcija. Preporuča se **bcrypt** ili **scrypt**. Također im treba odrediti valjani work factor (http://security.stackexchange.com/questions/3959/recommended-of-iterations-when-using-pkbdf2-sha256/3993#3993) kako bi bile dovoljno brze za tvoj server a dovoljno spore za napadača.

## Timing Attacks
Zaključivanje prema koliko dugo treba serveru da odgovori (npr. usporedba stringova, treba koristiti `Rack::Utils.secure_compare` koji uspoređuje
uvijek u konstantom vremenu). Iako, zbog šuma u networku ovo je jako teško izvesti u praksi.
