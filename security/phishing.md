# Phishing

*Phishing* je imitiranje stanice kako bi korisnik pomislio da je na poznatoj stranici u unio osjetljive podatke. Jedini sigurni dio browsera je address bar, jer današnji napadi mogu imitirati skoro sve:
* ime taba i favicon se mogu postaviti na bilo koju vrijednost.
* ime domene se može postaviti na `paypal-accounts.com` ili `account-update.com/paypal.com` i većina korisnika neće primjetiti da to nema veze s `paypal.com`.
* unutar same stranice može se nacrtati novi prozor sa svojim address barom u kojem možeš napisati što želiš.
* browserovi popupi za permissione se lako mogu nacrtati u htmlu.
* Full Screen API može napraviti što želi.

## window.opener

https://mathiasbynens.github.io/rel-noopener/

Svaki put kada otvaraš link u novom tabu, taj prozor ima pristup tvom windowu s `window.opener`. Ovo radi čak i ako je novi prozor na različitoj domeni od tvoje! Napadač time može redirectati tvoj prozor na phishing site bez da primjetiš.

Uvijek koristi `rel=noopener` na linkovima. On osigurava da će `window.opener` biti null. Usto i primjetno poboljšava performanse browsera (novi tab ne mora dijeliti isti thread.) Usto, ne koristi `target=_blank` na linkovima koje unose useri.

## Homograph attack

DNS podržava ne-ASCII znakove, ali protokoli koje email i browseri koriste često podržavaju samo ASCII. *Internationalized Domain Name* prevodi unicode u ASCII domene koristeći `Punycode`: `Bücher.ch > xn--bcher-kva.ch`

Ovo se može iskoristiti za *IDN homograph attack*: spoofanje domene koristeći npr. ćirilićna slova koja izgledaju isto kao i latinična. Zato bi browseri trebali prikazivati Punycode oblik sumnjivih slova.

Sličan napad može se napraviti imitiranjem nečijeg usernamea koristeći Unicode. Za obranu od toga dobro je normalizirati sve usernameove prije zapisivanja u bazu koristeći `normalize_unicode(:nfkc)`.
