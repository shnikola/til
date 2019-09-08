# Social

## How to hack a friend

https://defaultnamehere.tumblr.com/post/163734466355/operation-luigi-how-i-hacked-my-friend-without

Nakon što je dobio dozvolu da hakira prijateljicu, na netu je pronašao njen email (hotmail) i broj mobitela. Na haveibeenpwned.com pronašao je da za taj email leakan password Tumblr računa 2013. Tumblr je tada koristio salt (isti za sve račune), pa nije mogao upotrijebiti dictionary napad, ali je mogao pronaći druge račune s istim hashem. Neke od tih računa je našao u LinkedIn dumpu gdje su passwordi čuvani bez salta, i pokazalo se da većina njih imaju isti password. Nažalost, taj password nije radio na njenom mailu - promijenila ga je prije nekog vremena.

Onda se odlučio na phishing. Poslao je mail u kojem glumi da će joj se mail deaktivirati s linkom na lažnu formu za login. Taj je ignorirala. Onda je poslao mail glumeći predstavnika kompanije koji traži potvrdu reference za nekoga, s linkom na OneDrive Docs (zapravo phishing stranica hostana na aerobatic.io). Pokušaj je uspio, ali password koji je žtrva unijela je stari password koji ne radi - izgleda da ni ona ne zna koji password koristi. Umjesto toga, šalje novi mail, ovaj put s login formom koja uvijek odbije password, nadajući se da će žrtva isprobati sve svoje passworde. To upali i napadač dolazi do passworda za mail koji radi.

Ulogirava se noću dok žrtva spaja u slučaju da Hotmail dojavljuje kada se netko novi ulogira, ali Hotmail ne napravi ništa. Pretražuje mail za druge passworde, a da sakrije trag nakon toga pretraži za prethodnih top 5 searcheva da se "password" ne pojavi u autocompletu. Postavlja da se svi mailovi redirectaju na njegov mail. Gmail inače stavi jako veliko upozorenje kada se tako nešto napravi. Hotmail ne napravi ništa.

## Gmail Phishing

https://mobile.twitter.com/tomscott/status/812265182646927361

Slika koja izgleda kao gmail attachemnt je zapravo link koji vodi na login phishing stranicu.

Url stranice je data/html protocola i počinje s www.google.com/login, te ima puno spaceova prije `<script>` dijela, kako se on ne bi vidio u url-u browsera.

Svatko živ bi pao na ovo.

**Pouka:** Dobro provjeri url kad te gmail pita da se ulogiraš.

## On Phone Numbers and Identity

https://medium.com/the-coinbase-blog/on-phone-numbers-and-identity-423db8577e58

Napadač je došao u posjed Verizon telefonskog računa (koristio je osobne podatke pronađene na netu).
Zatim je prebacio broj s Verizona na VOIP, i tako dobio pristup broju. Sada je mogao koristiti broj za
recover Facebook accounta i glumiti vlasnika preko SMS-a.

**Pouka:** Posjedovanje kontrole nad telefonskim brojem ne može biti garancija identiteta. Za 2-factor autentifikaciju koristi U2F, Push-based ili TOTP. Sami SMS više nije dovoljan.

## Airplane Boarding Passes

https://media.ccc.de/v/33c3-7964-where_in_the_world_is_carmen_sandiego

Kada netko posta sliku svoje avionske karte na instagram, iz nje se vrlo lako može izvući booking reference (PNR) ili frequent flyer number. Ponekad su otisnuti na karti, ali uvijek su sadržani u bar kodu koji je formata: `M1<prezime>/<ime> E<6-digit booking no> ... UA <frequent flier no>`. Isti podatci se mogu lako saznati iz odbačenih karata u smeću, pa čak i naljepnica što se stavljaju na prtljagu.

Zračne kompanije tretiraju booking reference i frequent flyer number kao tajni podatak i koriste ih kao osnovnu autentifikaciju na svojim stranicama. Pomoću njih možeš se ulogirati i viditi većinu podataka o putniku i putovanju. Neke kompanije (npr. United Airlines) čak dopuštaju da uz par dodatnih lako nabavljivih podataka (prezime, datum rođenja) možeš promijeniti podatke (npr. staviti broj putovnice traženog kriminalca), promijeniti sjedala ili čak otkazati checkin.

**Pouka:** Ne stavljaj slike avionskih karata na društvene mreže.

