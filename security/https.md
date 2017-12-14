# HTTPS / TLS / SSL

`TLS` je protokol koji omogućuje sigurniju mrežnu komunikaciju između dvije strane. `SSL` je starija verzija protokola (`TLS 1.0` je `SSL 3.1`). `HTTPS` je naziv za slanje `HTTP` paketa preko `TLS` protokola.

`TLS` pruža tri usluge aplikacijama koje ga koriste:
* autentifikaciju (omogućuje provjeru identiteta obje strane)
* enkripciju (skriva sadržaj poruka koje se šalju)
* integritet (provjerava da li su poruke mijenjane ili lažirane).

## Handshake

1. Nakon uspostave TCP konekcije, klijent započinje TLS handshake. Šalje verziju TLS-a, ciphersuite i compression koje želi koristiti.
2. Server odabire najvišu verziju koju obojica podržavaju. Server šalje svoj certifikat i klijent provjerava vjeruje li certifikatu ili strani koja je potpisala certifikat.
3. Klijent šalje ključ pomoću kojim će simetrično kriptirati komunikaciju. Za razmjenu ključa koristi se RSA ili Diffie-Hellman. Poruka se potpisuje javnim ključem servera.
4. Server provjerava integritet poruke i odgovara enkriptiranom porukom.
5. Klijent je provjerava i počinje slati aplikacijske podatke.

## Man in the Middle

Svi uređaji između klijenta i servera u HTTPS konekciji (pa tako i napadač) mogu vidjeti samo IP i port konekcije, otprilike koliko podataka se šalje, te koja enkripcija se koristi. Mogu i prekinuti konekciju, ali obje strane će znati da je treća strana to učinila.

Ukoliko se spajaš na vanjsku mrežu preko proxija (npr. preko računala firme u kojoj si zaposlen ili ISP-a), proxy u pravilu ne može vidjeti promet koji šalješ preko HTTPS-a. Ali ako na računalo instaliraš certifikat vlasnika proxija, proxy će moći stvoriti 2 HTTPS tunela klijent-proxy i proxy-server, imajući pristup svim podatcima koje šalješ i primaš. Zato budi jako oprezan čije certifikate instaliraš na svoje računalo.

Velik dio nevidljive infrastrukture weba (cache serveri, content filteri, security gatewayi) izgrađeni su pod pretpostavkom HTTP protokola na portu 80. Neki od njih će samo proslijediti promet koji ne prepoznaju, dok će drugi pokušati slijepo dodati svoj sadržaj ili čak odbaciti pakete smatrajući ih malicioznim. Upravo zato svi moderni protokoli (HTTP2, Websocketi) predlažu ili čak zahtjevaju korištenje HTTPS tunela, kako bi se izbjegle potencijalne nekompatibilnosti s nekim random uređajem u mreži.

## Certificate Chain

Kako bi provjerio identitet servera, klijent mora imati njegov public key.  Ali ne želiš čuvati bazu public keyeva svih servera na klijentu. Zato svaki browser i OS dolaze s listom *Certified Authorities* kojima vjeruju. Server klijentu šalje svoj certifikat, u njemu se nalaze public key, ime i informacije o serveru, te potpis CA kojeg klijent može provjeriti i uvjeriti se da vjeruje serverovom certifikatu.

SSL Certifikati koriste X.509 format i obično se čuvaju u `.cert` fileovima. Potpisuj ga koristeći neku od `SHA-2` funkcija - moderni browseri će se buniti ako je korišten `SHA-1` ili nešto slabije.

*DV (Domain Validation)* certifikat se danas može nabaviti besplatno putem *Let's Encrypt* servisa - potrebno je samo dokazati vlasništvo domene u DNS postavkama. *EV (Extended Validation* certifikati dodatno garantiraju povezanost ustanove ili kompanije s domenom. U browseru se za njih prikazuje label s imenom vlasnika (npr. "PayPal Inc.") umjesto samo "Secure". Imaj na umu da certifikati služe samo za identifikaciju - skuplji certifikat nudi istu kriptografsku zaštitu kao i besplatni.

Kako bi se kontroliralo ponašanje CA institucija, Google je uveo *Certificate Transparency*. To je append-only log u kojem se objavljuje svaki certifikat kojeg CA potpiše.

## Certificate Revocation

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

## Napadi na HTTPS

*POODLE* je napad na `SSL 3.0` koji zloupotrebljava dodavanje proizvoljnog paddinga u CBC modu kako bi pročitao sadržaj requesta. Moderni browseri su otporni na njega.

*BEAST* napad omogućuje man-in-the-middle prisluškivanje prometa `TLS 1.0` protokola. Napad koristi slabost protokola koji koristi CBC i reusa IV. Moderni browseri su otporni na njega.

*CRIME* napad omogućuje napadaču s XSS-om i sniffanjem prometa da otkrije podatak iz requesta, npr. vrijednost `Cookie: secret=x7h1ta` headera. Napad koristi TLS kompresiju headera, šaljući requeste koji sadržavaju dodatni string `Cookie: secret=1`, `Cookie: secret=2`, itd. mjereći veličinu paketa. Zbog kompresije, najmanji paket će biti onaj koji sadrži string jednak onom iz headera. Iz tog je razloga preporučljivo ne koristiti TLS kompresiju, i svi moderni browseri i serveri je disablaju.

Sličan napad, *BREACH*, koristi HTTP kompresiju da dohvati podatak iz bodyja. Nažalost, HTTP kompresija se ne može praktično isključiti, pa od njega nema definitivne obrane.

## Tools

* https://www.ssllabs.com/ssltest
* https://www.ssllabs.com/ssl-pulse/
