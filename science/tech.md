# Tech

## Bitcoin

Bitcoin sustav ne koristi serijske brojeve kao papirnati novac, već prati transakcije. Za dohvaćanje stanje nečijeg računa potrebno je proći kroz sve postojeće transakcije i od njih izračunati konačno stanje.

Kako se osigurati da nitko ne krivotvori transakciju koja dodaje milijun bitcoina na svoj račun? Ne postoji centralno tijelo, već svi korisnici bitcoina sudjeluju u validaciji transakcija.

Validacija se obavlja tako da prvi korisnik koji nađe broj čiji je hash jednak zadanom rezultatu ima pravo odobriti transakciju. Traženje hasha (mining) je izuzetno zahtjevan posao. Usto, to odobrenje mora validirati još 5 različitih korisnika, čineći krivotvorenje transakcija praktički nemogućim.

Trenutna nagrada za validaciju je 12 bitcoina, što je oko $25.000.

## Time, technology and leaping seconds

https://googleblog.blogspot.hr/2011/09/time-technology-and-leaping-seconds.html

Zbog raznih fluktuacija u zemljinoj rotaciji, i najprecizniji satovi moraju se povremeno prilagoditi da se poklapaju sa "solarnim" vremenom. Takva prilagođavanja, tzv. *leap seconds* dogodila su se 24 puta od 1972.

U Googlu su to riješili tako da tijekom dana u kojem se *leap second* treba dodati, NTP server postupno dodaje po nekoliko milisekunda, pa se svi serveri pomalo prilagode bez da treba svaki posebno mijenjati.
