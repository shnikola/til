# Cross Site Request Forgery

Napadač na prevaru natjera korisnika da obavi neželjene naredbe na siteu u koji je ulogiran. Pošto napadač ne može vidjeti rezultat svoje akcije, cilj nije krađa informacije, već mijenjanje stanja poput obavljanja financijskih transakcija, mijenjanje email adrese i sl.

Ovaj napad zloupotrebljava povjerenje sitea u korisnikov browser.

## Metoda napada

Napadač konstruira URL ili skriptu koja će izvesti naredbu, te mora natjerati korisnika koji je autentificiran da tu naredbu izvrši.

U slučaju da se radi o GET requestu, dovoljno je da iskonstruira URL i pošalje ga kao link, ili još bolje, 0x0 image u emailu. Request će se automatski napraviti čim korisnik otvori email.

POST request se mora poslati kao forma, ali i ona se može submitati bez korisnikove interakcije: `<body onload="document.forms[0].submit()">`.

Ajax requesti su srećom neupotrebljivi zbog same-origin policy restrikcije.

## Obrana

Ne koristi GET za requeste koji mijenjaju stanje.

Koristi `Origin` i `Referer` headere za provjeru dolazi li request s iste domene. HTTP headeri se inače mogu lažirati, ali nemoguće ih je lažirati sa žrtvinog browsera.

Server treba generirati CSRF token s velikom random vrijednosti koji se dodaje u svaku formu kao hidden input i svaki ajax. Request se odbija ako token nije validan.

*Same site cookies* su feature u novim browserima koji se šalju samo sa same origin requestovima.

## Clickjacking

*Clickjacking* je kad napadač na svojoj stranici stavi nevidiljivi `iframe` na tvoju stranicu, te prevarom natjera da klikneš na njega i npr. obrišeš sve mailove.

Da bi se obranio, koristi se `X-Frame-Options` header koji će browseru zabraniti da prikazuje tvoju stranicu unutar `iframe`a.
* `X-Frame-Options: deny` ne prikazuje se nikad
* `X-Frame-Options: sameorigin` samo embeddan na stranicama istog origina
* `X-Frame-Options: allow-from: www.example.com` samo embeddan na stranicama dane domene. Nažalost, ne podržava više domena odjednom.

Modernija alternativa je `Content-Security-Policy: frame-ancestors 'self' example.com *.example.net` _IE 10+_.

## JSON Hijacking

Varijacija CSRF napada koja dohvaća korisnikove podatke s GET endpointa koji vraćaju JSON. Napadač na svoju stranicu postavlja `<script src="https://mail.google.com/json">` koji radi request s korisnikovim cookijima i vraća korisnikove podatke. Vraćeni JSON nije direktno dostupan napadaču.

Međutim, stariji browseri su dopuštali da napadač overridea `Array()` i `Object()` konstruktore koji se koriste pri inicijalizaciji JSON-a i tako dođe do podataka. Noviji browseri su pokrpali tu rupu, ali GMail i dalje ispred svojih JSON-a za svaki slučaj stavlja `while(1);`
