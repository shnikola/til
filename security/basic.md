# Security
TODO:
session hijacking vs CSRF vs clickjacking
dns hijacking
dns spoofing


## DNS Rebinding
http://bouk.co/blog/hacking-developers
Zli server ima registriranu domenu s jako kratkim TTLom, koja se zbog toga ne cacheira. Kada se korisnik prvi put spoji na server, on pomoću `iptables` zamjeni svoju IP adresu s nekom zlobnom, i svi requestovi se onda šalju tamo. Browser misli da i dalje vrijedi same-origin policy.

Kreativni napad je zamjeniti svoju IP adresu s *korisnikovom privatnom*, npr. `127.0.0.1` ili `192.168.0.1`. Nakon toga možeš slati requestove na korisnikove lokalne servise koji se vrta ne nekom portu, npr. `Redis` ili `ElasticSearch`.

Potencijalna obrana od ovog je da browseri uvedu *DNS pinning*, tj. da uvijek koriste IP koji su dobili u prvom requestu.


## DOS
https://www.incapsula.com/ddos/ddos-attacks/denial-of-service.html
Denial Of Service napad šalje pakete na server koji ometaju njegov rad. Distributed DOS šalje pakete s više računala odjednom (tzv. *botnet*). Ometati rad možeš na više načina:
* *Volume Based Attacks*: Pošalji toliko paketa da ih connection ili firewall ne mogu handlati (npr. UDP ili ICMP flood).
* *Protocol Attacks*: Pošalji takve pakete koji će zauzeti resurse servera, load balancera ili firewalla (npr. SYN flood).
* *Application Layer Attacks:* Pošalji legitimne requeste na aplikaciju koji zahtjevaju puno resursa.

Detekcija je otežana jer mog spoofati IP - nije im bitno da response dođe do njih. Štoviše, *Reflection attack* šalje legitimne requestove javno dostupnom serveru, ali s IP-jem žrtve, pa on dobija sve odgovore.

Osnovni problem je: ako napadač šalje 11Gbps na tvoj 10Gbps router, nikakva zaštita unutar servera tu ne može pomoći. Jedina zaštita je imati DOS protection kao CloudFlare s puno većim kapacitetom, koji će detektirati i filtrirati takav promet. Primjeri volume based attackova:
  * *UDP Flood* šalje hrpu UDP paketa na random portove, pri čemu server mora provjeriti postoji li aplikacija na tom portu, i odgovoriti s ICMP error paketom, što troši resurse. *DNS UDP Flood* šalje hrpu requestova na DNS za nepostojeće domene što mu iscrpi resurse i napuni cache nepotrebim recordima.
  * *DNS Amplification* šalje request na DNS resolver sa spoofanom IP adresom servera. Amplification se postiže s određenim opcijama, pa response DNS-a može biti i 70 puta veći nego request napadača. *NTP Amplification* ima isti princip, samo koristi NTP servere.

Neki od protocol attackova:
  * *Ping of Death* šalje ping podijeljen u IP pakete koji zajedno čine paket veći od 65,535 byteova, što uzrokuje buffer overflow na serveru. *Teardrop* šalje fragmente koji se preklapaju, pa greška u izračunu šalje veliki broj u memcpy, uzrokujući veliko zauzeće memorije.
  * *SYN Flood (layer 4)* šalje veliki broj SYN paketa za uspostavu TCP veze, bez da dovrši 3-way handshake. Server ostane bez slobodnih konekcija pa se više nitko ne može spojiti.
  * *Slowloris* šalje dijelove HTTP paketa (npr. headere) ali ih nikad ne dovrši, čime zauzme sve konekcije na serveru.
  * *RUDY* šalje HTTP POST s jako velikim content-legthom, ali jako sporo (npr. byte svakih 10 sekundi).

Kod application (layer 7) napada, napadač jednostavno radi hrpu requestova na skupe endpointe (npr. search), pa se server uspori i ne može handlati ostale requestove. Obrana:
  * Ako dolaze s jednog IP-ja, dodaj rate limiting po IP-ju.
  * Ako dolaze s rezličitih, otkrij koji URL napadaju i cachiraj ga CDN-om. Za stranice koje se dinamički generiraju (npr. thread na forumu) cachiraj na 5 minuta za nelogirane korisnike. Nitko neće primjetiti. Čak se isplati i generirati statički html svaku sekundu ako dobijaš više od 1 req/s.
  * Stranice koje ne možeš cachati (search, login screen), promjeni URL ili dodaj CAPTCHu preko CDNa.
