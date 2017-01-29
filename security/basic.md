# Security
TODO:
session hijacking vs CSRF vs clickjacking
dns hijacking
dns spoofing
uploads

## HTTPS

`TLS` (i stara verzija, `SSL`) je protokol iznad `TCP`-a koji omogućava sigurnu konekciju. `HTTP` paket unutar njega je nepromijenjen. Napadač u mreži može vidjeti samo IP i port konekcije, otprilike koliko podataka šalješ, te koju enkripciju koristiš. Može i prekinuti konekciju, ali obje strane će znati da je treća strana to učinila.

Protokol:
* Nakon uspostave TCP konekcije, klijent započinje SSL handshake. Šalje verziju SSL-a, ciphersuite i compression koje želi koristiti. Server odabira najvišu verzije koje obojica podržavaju.
* Server šalje svoj certifikat i klijent provjerava vjeruje li certifikatu ili strani koja je potpisala certifikat.
* Klijent šalje ključ (kriptiran public-keyem servera) pomoću kojim će simetrično kriptirati komunikaciju.
* Klijent šalje kriptiranu poruku, server je provjerava i šalje svoju.

Kako bi provjerio identitet servera, moraš imati njegov public key. Ali ne želiš čuvati bazu public keyeva svih servera na klijentu. Zato svaki Browser dolazi s listom *Certified Authorities* kojima vjeruje. Kada server pošalje svoj certifikat, pisat će da ga je potpisao i CA, što možeš provjeriti.

Za testiranje SSL-a na svom serveru, koristi https://www.ssllabs.com/ssltest

## HSTS

HTTP Strict Transport Security (HSTS) nalaže browseru da obavlja promet s domenom isključivo preko HTTPS-a.

Postavlja se pomoću headera: `Strict-Transport-Security: max-age=31536000; includeSubDomains; preload`. `max-age` je broj sekundi koliko će dugo browser koristiti HTTPS, `includeSubDomains` uzima u obzir i sve poddomene sitea, `preload` uključuje site u listu HSTS siteova hardkodiranu u Chrome.

Od trenutka kada primi header, browser će sav promet usmjeren na domenu slati koristeći `https://`.

Prednost ovog pristupa u odnosu na `301` redirect na HTTPS je što *man-in-the-middle* napad može presresti prvi HTTP request i umjesto redirecta vratiti zlu stranicu. U slučaju HSTS-a, ako je korisnik ikad prije posjetio site, browser će automatski slati HTTPS bez da šalje HTTP request.

## Public Key Pinning

Bilo koji Certificate Authority može izdati certifikat za bilo koji site, bez dozvole vlasnika sitea. Tako se mogu izdati i lažni certifikati.

Public key pinning omogućuje vlasnicima sitea da kažu čije certifikate odobravaju. Static pinning je ugrađen u browsere, a postoji i dynamic pinning koji koristi `Public-Key-Pins` header. Ali postavljanje vlastitog pinninga je komplicirano i može vrlo lako brickati site ako ne znaš što radiš.

## DNS Rebinding

http://bouk.co/blog/hacking-developers

Zli server ima registriranu domenu s jako kratkim TTLom, koja se zbog toga ne cacheira. Kada se korisnik prvi put spoji na taj server, on pomoću `iptables` zamjeni svoju IP adresu s nekom zlobnom, i svi idući requestovi se šalju na nju, a browser misli da i dalje vrijedi same-origin policy.

Kreativni napad je zamjeniti serverovu IP adresu s *korisnikovom privatnom*, npr. `127.0.0.1` ili `192.168.0.1`. Nakon toga napadač može slati requestove na korisnikove lokalne servise koji se vrta ne nekom portu, npr. `Redis` ili `ElasticSearch`.

Potencijalna obrana od ovog je da browseri uvedu *DNS pinning*, tj. da uvijek koriste IP koji su dobili u prvom requestu.

## DOS

https://www.incapsula.com/ddos/ddos-attacks/denial-of-service.html

Denial Of Service napad šalje pakete na server koji ometaju njegov rad. Distributed DOS šalje pakete s više računala odjednom (tzv. *botnet*). Ometati rad možeš na više načina:
* *Volume Based Attacks*: Pošalji toliko paketa da ih connection ili firewall ne mogu handlati (npr. UDP ili ICMP flood).
* *Protocol Attacks*: Pošalji takve pakete koji će zauzeti resurse servera, load balancera ili firewalla (npr. SYN flood).
* *Application Layer Attacks:* Pošalji legitimne requeste na aplikaciju koji zahtjevaju puno resursa.

Detekcija je otežana jer mog spoofati IP - nije im bitno da response dođe do njih. Štoviše, *Reflection attack* šalje legitimne requestove javno dostupnom serveru, ali s IP-jem žrtve, pa on dobija sve odgovore.

Osnovni problem je: ako napadač šalje 11Gbps na tvoj 10Gbps router, nikakva zaštita unutar servera tu ne može pomoći. Jedina zaštita je imati DOS protection kao CloudFlare s puno većim kapacitetom, koji će detektirati i filtrirati takav promet. Primjeri volume based attackova:

*UDP Flood* šalje hrpu UDP paketa na random portove, pri čemu server mora provjeriti postoji li aplikacija na tom portu, i odgovoriti s ICMP error paketom, što troši resurse. *DNS UDP Flood* šalje hrpu requestova na DNS za nepostojeće domene što mu iscrpi resurse i napuni cache nepotrebim recordima.

*DNS Amplification* šalje request na DNS resolver sa spoofanom IP adresom servera. Amplification se postiže s određenim opcijama, pa response DNS-a može biti i 70 puta veći nego request napadača. *NTP Amplification* ima isti princip, samo koristi NTP servere.

Neki od protocol attackova:

*Ping of Death* šalje ping podijeljen u IP pakete koji zajedno čine paket veći od 65,535 byteova, što uzrokuje buffer overflow na serveru. *Teardrop* šalje fragmente koji se preklapaju, pa greška u izračunu šalje veliki broj u memcpy, uzrokujući veliko zauzeće memorije.

*SYN Flood (layer 4)* šalje veliki broj SYN paketa za uspostavu TCP veze, bez da dovrši 3-way handshake. Server ostane bez slobodnih konekcija pa se više nitko ne može spojiti.

*Slowloris* šalje dijelove HTTP paketa (npr. headere) ali ih nikad ne dovrši, čime zauzme sve konekcije na serveru.

*RUDY* šalje HTTP POST s jako velikim content-legthom, ali jako sporo (npr. byte svakih 10 sekundi).

Kod application (layer 7) napada, napadač jednostavno radi hrpu requestova na skupe endpointe (npr. search), pa se server uspori i ne može handlati ostale requestove. Obrana:
* Ako dolaze s jednog IP-ja, dodaj rate limiting po IP-ju.
* Ako dolaze s rezličitih, otkrij koji URL napadaju i cachiraj ga CDN-om. Za stranice koje se dinamički generiraju (npr. thread na forumu) cachiraj na 5 minuta za nelogirane korisnike. Nitko neće primjetiti. Čak se isplati i generirati statički html svaku sekundu ako dobijaš više od 1 req/s.
* Stranice koje ne možeš cachati (search, login screen), promjeni URL ili dodaj CAPTCHu preko CDNa.

## SQL injection metode

https://www.troyhunt.com/everything-you-wanted-to-know-about-sql

Tehnike SQL injectiona:
* `WHERE id = 1 OR 1=1` dohvaća sve podatke.
* `WHERE id = 1 UNION ALL SELECT password FROM user` dohvaća iz druge tablice.
* `WHERE id = 1 and select top 1 substring(name, 1, 1) from sysobjects ... = 'a'` pogađa prvo slovo imena tablica
* `WHERE id = 1; SHUTDOWN` gasi server

## Clickjacking

*Clickjacking* je kad napadač na svojoj stranici stavi nevidiljivi `iframe` na tvoju stranicu, te prevarom natjera da klikneš na njega i npr. obrišeš sve mailove.

Da bi se obranio, koristi se `X-Frame-Options` header koji će browseru zabraniti da prikazuje tvoju stranicu unutar `iframe`a.
* `X-Frame-Options: deny` ne prikazuje se nikad
* `X-Frame-Options: sameorigin` samo embeddan na stranicama istog origina
* `X-Frame-Options: allow-from: www.example.com` samo embeddan na stranicama dane domene. Nažalost, ne podržava više domena odjednom.

Modernija alternativa je `Content-Security-Policy: frame-ancestors 'self' example.com *.example.net` _IE 10+_.

## Phishing

*Phishing* je imitiranje stanice kako bi korisnik pomislio da je na poznatoj stranici u unio osjetljive podatke. Jedini sigurni dio browsera je address bar, jer današnji napadi mogu imitirati skoro sve:
* ime taba i favicon se mogu postaviti na bilo koju vrijednost.
* ime domene se može postaviti na `paypal-accounts.com` ili `account-update.com/paypal.com` i većina korisnika neće primjetiti da to nema veze s `paypal.com`.
* unutar same stranice može se nacrtati novi prozor sa svojim address barom u kojem možeš napisati što želiš.
* browserovi popupi za permissione se lako mogu nacrtati u htmlu.
* Full Screen API može napraviti što želi.

## Upload Security
- ne dopuštaj da korisnik bira ime ili putanju filea
- provjeri maksimalnu veličinu
- whitelista file typeova


## Literatura

* https://www.owasp.org/index.php/Category:Attack
