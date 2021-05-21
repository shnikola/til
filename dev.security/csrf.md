# Cross Site Request Forgery

Napadač na prevaru natjera korisnika da obavi neželjene naredbe na siteu u koji je ulogiran. Pošto napadač ne može vidjeti rezultat svoje akcije, cilj nije krađa informacije, već mijenjanje stanja poput obavljanja financijskih transakcija, mijenjanje email adrese i sl.

Ovaj napad zloupotrebljava povjerenje sitea u korisnikov browser.

## Metoda napada

Napadač pokušava poslati request na endpoint drugog origina koristeći korisnikovu autentifikaciju. Same-origin policy mu ne dopušta da samo napravi ajax request, pa mora biti lukav.

U slučaju da se radi o GET requestu, dovoljno je da iskonstruira URL i pošalje ga kao link, ili još bolje, 0x0 image u emailu. Request će se automatski napraviti čim korisnik otvori email.

POST request se mora poslati kao forma, ali i ona se može submitati bez korisnikove interakcije: `<body onload="document.forms[0].submit()">`.

## Obrana

Ne koristi GET za requeste koji mijenjaju stanje.

Koristi `Origin` i `Referer` headere za provjeru dolazi li request s iste domene. HTTP headeri se inače mogu lažirati, ali nemoguće ih je lažirati sa žrtvinog browsera.

Server treba generirati CSRF token s velikom random vrijednosti koji se dodaje u svaku formu kao hidden input i svaki ajax. Request se odbija ako token nije validan.

*Same site cookies* je feature u novim browserima. Cookiji sa `SameSite=strict` će se slati samo u same-site requestovima. Site je nešto kao origin, samo opuštenije (npr. `www.google.com` i `static.google.com` su isti site, ali `a.github.io` i `b.github.io` nisu; popis je na https://publicsuffix.org).

## Same-origin policy and CORS

Bitna stavka u obrani od CSRF je **same-origin policy**. Browseri dopuštaju da se neki resursi na stranici dohvaćaju s drugog origina: slike, stylesheetovi, skripte, iframeovi i video sadržaj. Dopušta se i slanje POST forme na različiti origin. Origin se definira kao `schema + host + port`, npr. `https://www.gmail.com:80`.

Ali browser neće dopustiti da skripta AJAXom dohvati ili pošalje podatke na drugi origin. Zahvaljujući tome, skripta s `evil.com` ne može dohvatiti tvoje mailove s `gmail.com` APIja koristeći tvoje cookije.

Ima slučajeva kada ipak želimo dopustiti pristup APIju s različitih origina (npr. ako `www.gmail.com` želi dohvatiti nešto s `api.gmail.com`). Kako bi zaobišao same-origin policy, koristi se *Cross Origin Resource Sharing* (CORS). On omogućuje serveru da definira kojim originima dopušta AJAX pozive, a browseru mehanizam da to iskoriti. Svi moderni browseri pri slanju na različiti origin automatski koriste CORS.

Prije slanja samog requesta, browser će poslati preflight `OPTIONS` request koji sadrži origin klijenta, te metode i headere koji se namjeravaju poslati (`Origin`, `Access-Control-Request-Method`, `Access-Control-Request-Headers`). Ukoliko server dopušta CORS za poslani origin, odgovorit će s danim originom ili s `*`, te dopuštenim metodama i headerima (`Access-Control-Allow-Origin`, `Access-Control-Allow-Methods`, `Access-Control-Allow-Headers`). Browser tada zna da je CORS dopušten, te šalje stvarni requst, opet s `Origin` headerom. U suprotnom, trigerirat će AJAX `error` event zbog nedopuštenog cross-origin requesta.

Same-origin policy i CORS postoje da zaštite korisnike browsera, a ne same podatke. Provode se na browseru, a ne na serveru. Koristiš li npr. `curl` da dohvatiš podatke iz APIja, origini s tim nemaju veze.

### Credentials

Za slanje cookija i authentifikacije preko AJAXa, potrebno je dodati `withCredentials = true` flag. Ukoliko server dopušta korištenje credentiala za taj origin, vratit će `Access-Control-Allow-Credentials: true`. Ako ne vrati, browser će odbaciti response.

`Access-Control-Allow-Origin: *` ne dopušta korištenje credentiala, jer bi to potpuno zaobišlo same-origin policy.

### Preflight

Preflight se radi kako bi se zaštitili stari serveri koji ne očekuju da će primiti `PUT` ili `DELETE` s različitog origina. Da im novi browseri ne bi automatski poslali takav request, šalje se `OPTIONS` na koji oni neće odgovoriti, pa će browser prepoznati da ne podržavaju CORS.

Preflight requesti se mogu cachirati, koristeći serverov header `Access-Control-Max-Age`, ali browseri ne dopuštaju cachiranje cijele domene nego na razini requesta, i još dopuštaju maksimalno vrijeme od 10 min. Stoga je cachiranje trenutno prilično beskorisno.

### no-cors

Browser neke resurse (slike, cssovi, scripte i media sadržaj) dohvaća s različitog origina bez CORSa, šaljući s requestom i sve cookije za taj origin. Ovo nije baš dobra stvar, ali jako puno siteova ovisi o tome pa je prekasno da se mijenja.

Iako se tako dohvaćeni podatci ne mogu isčitati iz skripte (pa ne možeš kao `src` slike postaviti JSON endpoint), možeš detektirati jesu li podatci uspješno dohvaćeni ili ne. Na taj način, ako postoji slika koja se prikazuje samo ulogiranim korisnicima, možeš detektirani je li korisnik ulogiran u neki vanjski servis.

## Misconfigured CORS Exploitation

Loše iskonfiguriran CORS se može zloupotrijebiti da se zaobiđe same-origin policy i koristeći korisnikove cookije dohvati, npr. `GET /api_token`, dajući napadaču pristup računu.

Razlog loše konfiguracije je što CORS ne dopušta vraćanje više origina u `Access-Control-Allow-Origin`, niti korištenje wildcard subdomena. Zbog toga mnogi librariji koriste programska rješenja da zaobiđu ta ograničenja, ostavljajući mjesta za zloupotrenu.

Npr. `Access-Control-Allow-Origin: *` ne dopušta korištenje credentiala, ali serveri često programski dopuste bilo koji origin tako da samo vrate onaj koji su dobili, zajedno s credentialima. Ovo je radio i `rack_cors` dugo vremena.

Neki serveri nedovoljno dobro parsiraju origin pa `utorkom.com` dopušta `autorkom.com`, ili `utorkom.com.evil.net`. Dopuštanje `http` origina omogućuje man-in-the-middle napad.

Serveri vrlo često greškom dopuštaju `null` origin (u specifikaciji predviđen za local HTML fileove), kojeg napadač lako emulira unutar sandbox iframea: `<iframe sandbox="allow-scripts allow-top-navigation allow-forms" src='data:text/html,<script>*cors stuff here*</script>’></iframe>`.

## Clickjacking

Napadač na svojoj stranici stavlja nevidiljivi `iframe` na tvoju stranicu, te prevarom natjera da klikneš na njega i npr. obrišeš sve mailove. Odlično mjesto za clickjacking je cookie consent poruka na koju su svi navikli da klikaju bez razmišljanja.

Da bi se obranio, koristi se `X-Frame-Options` header koji će browseru zabraniti da prikazuje tvoju stranicu unutar `iframe`a.
* `X-Frame-Options: deny` ne prikazuje se nikad
* `X-Frame-Options: sameorigin` samo embeddan na stranicama istog origina
* `X-Frame-Options: allow-from: www.example.com` samo embeddan na stranicama dane domene. Nažalost, ne podržava više domena odjednom.

Modernija alternativa je `Content-Security-Policy: frame-ancestors 'self' example.com *.example.net`.

## JSON Hijacking

Varijacija CSRF napada koja dohvaća korisnikove podatke s GET endpointa koji vraćaju JSON. Napadač na svoju stranicu postavlja `<script src="https://mail.google.com/json">` koji radi request s korisnikovim cookijima i vraća korisnikove podatke. Vraćeni JSON nije direktno dostupan napadaču.

Međutim, stariji browseri su dopuštali da napadač overridea `Array()` i `Object()` konstruktore koji se koriste pri inicijalizaciji JSON-a i tako dođe do podataka. Noviji browseri su pokrpali tu rupu, ali GMail i dalje ispred svojih JSON-a za svaki slučaj stavlja `while(1);`
