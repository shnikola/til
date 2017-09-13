# Social

## Phishing

*Phishing* je imitiranje stanice kako bi korisnik pomislio da je na poznatoj stranici u unio osjetljive podatke. Jedini sigurni dio browsera je address bar, jer današnji napadi mogu imitirati skoro sve:
* ime taba i favicon se mogu postaviti na bilo koju vrijednost.
* ime domene se može postaviti na `paypal-accounts.com` ili `account-update.com/paypal.com` i većina korisnika neće primjetiti da to nema veze s `paypal.com`.
* unutar same stranice može se nacrtati novi prozor sa svojim address barom u kojem možeš napisati što želiš.
* browserovi popupi za permissione se lako mogu nacrtati u htmlu.
* Full Screen API može napraviti što želi.

## Homograph attack

DNS podržava ne-ASCII znakove, ali protokoli koje email i browseri koriste često podržavaju samo ASCII. *Internationalized Domain Name* prevodi unicode u ASCII domene koristeći `Punycode`: `Bücher.ch > xn--bcher-kva.ch`

Ovo se može iskoristiti za *IDN homograph attack*: spoofanje domene koristeći npr. ćirilićna slova koja izgledaju isto kao i latinična. Zato bi browseri trebali prikazivati Punycode oblik sumnjivih slova.

Sličan napad može se napraviti imitiranjem nečijeg usernamea koristeći Unicode. Za obranu od toga dobro je normalizirati sve usernameove prije zapisivanja u bazu koristeći `normalize_unicode(:nfkc)`.

# Stories

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


