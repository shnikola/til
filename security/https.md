# HTTPS

`TLS` (i stara verzija, `SSL`) je protokol iznad `TCP`-a koji omogućava sigurnu konekciju. `HTTP` paket unutar njega je nepromijenjen. Napadač u mreži može vidjeti samo IP i port konekcije, otprilike koliko podataka šalješ, te koju enkripciju koristiš. Može i prekinuti konekciju, ali obje strane će znati da je treća strana to učinila.

Protokol:
1. Nakon uspostave TCP konekcije, klijent započinje SSL handshake. Šalje verziju SSL-a, ciphersuite i compression koje želi koristiti. Server odabira najvišu verziju koju obojica podržavaju.
2. Server šalje svoj certifikat i klijent provjerava vjeruje li certifikatu ili strani koja je potpisala certifikat.
3. Klijent šalje ključ pomoću kojim će simetrično kriptirati komunikaciju. Za razmjenu ključa koristi se RSA ili Diffie-Hellman. Poruka se potpisuje javnim ključem servera.
4. Server provjerava integritet poruke i odgovara enkriptiranom porukom.
5. Klijent je provjerava i počinje slati aplikacijske podatke.

## Certificate Chain

Kako bi provjerio identitet servera, moraš imati njegov public key. Ali ne želiš čuvati bazu public keyeva svih servera na klijentu. Zato svaki browser i OS dolaze s listom *Certified Authorities* kojima vjeruju. Kada server pošalje svoj certifikat, pisat će da ga je potpisao i CA, što možeš provjeriti.

Ponekad se certifikat treba povući (*revoke*) jer mu je privatni ključ ili CA kompromitiran. Za taj slučaj, svaki certifikat u sebi sadrži upute kako provjeriti je li povučen.

Jedan način provjere je preko *Certificate Revocation List* koju svaki CA objavljuje. Problem je što takve liste postaju samo veće i sporo ih se dohvaća, pa ih klijenti cachiraju, što znači da im updateovi dolaze tek kad im cache istekne. Drugi način je *Online Certificate Status Protocol* koji omogućuje provjeru u stvarnom vremenu. Problem je što CA moraju handlati puno veći request load, a klijenti moraju čekati na odgovor svaki put kad žele provjeriti ispravnost certifikata.

Rješenje je *OCSP Stapling*. Umjesto klijenta, server periodički dohvaća potpisani OCSP odgovor od CA, te ga dodaje (*stapla*) kao dio TLS handshakea. Na taj način klijent istovremeno validira certifikat i OCSP revocation.

## RSA vs Diffie-Hellman

*RSA* koristi serverov public key da pošalje simetrični ključ. U slučaju da napadač sazna serverov private key, ili ga nekad u budućnosti nabavi, moći će dekriptirati svu komunikaciju između klijenta i servera.

*Diffie-Hellman* omogućuje uspostavu simetričnog ključa bez da ga se šalje u mrežu. Čak i ako napadač posjeduje serverov private key, ne može presresti simetrični ključ. Još bolje, klijent i server uspostavljaju novi ključ pri svakom sessionu, pa ako napadač dođe do serverovog private keya i dobije pristup trenutnom simetričnom ključu, neće moći dekriptirati promet prethodnih sessiona. Ovo se naziva *perfect forward secrecy*.

## Server Name Indication (SNI)

Klijentu treba samo IP adresa servera da bi obavio TLS handshake. Ali ako server ima više hostanih siteova s različitim SSL certifikatima na istom IP-u, protokol ne može znati na koji se pokušava spojiti. Zato je uvedena ekstenzija protokola, *Server Name Indication* koja šalje i hostname servera na koji se spajaš.

Nažalost stariji klijenti (IE na Windows XP, Android 2.2) ne podržavaju SNI. Ako ih želiš podržavati, morat ćeš imati dedicirani IP za svaki host.

## HSTS

HTTP Strict Transport Security (HSTS) nalaže browseru da obavlja promet s domenom isključivo preko HTTPS-a.

Postavlja se pomoću headera: `Strict-Transport-Security: max-age=31536000; includeSubDomains; preload`. `max-age` je broj sekundi koliko će dugo browser koristiti HTTPS, `includeSubDomains` uzima u obzir i sve poddomene sitea, `preload` uključuje site u listu HSTS siteova hardkodiranu u Chrome.

Od trenutka kada primi header, browser će sav promet usmjeren na domenu slati koristeći `https://`.

Prednost ovog pristupa u odnosu na `301` redirect na HTTPS je što *man-in-the-middle* napad može presresti prvi HTTP request i umjesto redirecta vratiti lažnu stranicu. U slučaju HSTS-a, ako je korisnik ikad prije posjetio site, browser će automatski slati HTTPS bez da šalje HTTP request.

## Public Key Pinning

Certificate Authority može izdati certifikat za bilo čiji server bez dozvole vlasnika servera. Napadač na CA tako može generirati lažni ali validan certifikat za `gmail.com` i koristiti ga na svom phishing siteu. Upravo to se dogodilo s jednim Root CA, DigiNotarom.

Public key pinning omogućuje vlasnicima sitea da kažu koje certifikate odobravaju. Static pinning je ugrađen u browsere, a postoji i dynamic pinning koji koristi `Public-Key-Pins` header. Ali postavljanje vlastitog pinninga je komplicirano i može vrlo lako brickati site ako ne znaš što radiš.

## Tools

* Za testiranje SSL-a na svom serveru, koristi https://www.ssllabs.com/ssltest.
* https://istlsfastyet.com
