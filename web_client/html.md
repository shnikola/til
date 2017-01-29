# HTML

## Isprika

https://html.spec.whatwg.org/multipage/introduction.html#design-notes

"It must be admitted that many aspects of HTML appear at first glance to be nonsensical and inconsistent.

"HTML, its supporting DOM APIs, as well as many of its supporting technologies, have been developed over a period of several decades by a wide array of people with different priorities who, in many cases, did not know of each other's existence.

"Features have thus arisen from many sources, and have not always been designed in especially consistent ways. Furthermore, because of the unique characteristics of the Web, implementation bugs have often become de-facto, and now de-jure, standards, as content is often unintentionally written in ways that rely on them before they can be fixed."

## <head>

Must-have tagovi koji idu na sam početak heada:
* `<meta charset="utf-8">` definira encoding dokumenta koji slijedi.
* `<meta name="viewport" content="width=device-width, initial-scale=1">` prilagođava viewport za mobilne uređaje.
* `<meta http-equiv="x-ua-compatible" content="ie=edge">` ako želiš podržavati stare IE, ovo ih tjera da se ponašaju što modernije mogu.

`<title> Page Title </title>` je naslov stranice koji se prikazuje u tabu i na search rezultatima.

`<meta name="description" content="A description of the page">` opis stranice do 150 znakova. Možda se prikaže kod search resulta (Google to odlučuje).

`<base href="https://example.com/admin/">` postavlja bazu na koju idu svi relativni pathovi u dokumentu. Nažalost, to vrijedi i za `href='#id'` linkove, što može biti problematično, pa ga izbjegavaj.
`<base target="_blank">` postavlja defaultni `target` za sve linkove.

## <link>

`<link rel="canonical" href="https://example.com/how-to-dance.html">` koristi u slučaju objavljivanja stranice pod različitim URLovima, pa crawleri neće misliti da imaš duplicirani sadržaj.

`<link rel="alternate" hreflang="es" href="http://es.example.com/">` za stranicu na drugom jeziku.

`<link rel="manifest" href="/manifest.webmanifest">` manifest za web aplikacije.

`<link rel="author" href="humans.txt">` podatci o autorima stranice.

Za seriju dokumenata koristi `<link rel="first">`, `rel="prev"`, `rel="next"`, `rel="last"`. `rel="index"` za sadržaj.

`<link rel="icon">` služi za postavljanje favicona. Treba ih više nego što misliš. Koristi https://realfavicongenerator.net

## <meta>

`<meta http-equiv="Header-Name" content="Header value">` se može koristiti kao ekvivalent nekih HTTP headera u slučaju da nemaš pristup nad konfiguracijom servera. Npr. `<meta http-equiv="Content-Security-Policy" content="default-src 'self'">`.

Indeksiranje search enginea je po defaultu uključeno, ali može se isključiti s `<meta name="robots" content="noindex">`:
* `noindex` neće indexirati stranicu.
* `nofollow` neće pratiti linkove na stranici.
* `noimageindex` neće prikazivati stranicu kao referring za image u rezultatima searcha.
* `noarchive` neće prikazivati "Cached" link u rezultatima searcha.
* `nosnippet` neće prikazivati description u rezultatima searcha.

`<meta name="google" content="nositelinkssearchbox">` u rezultatima searcha neće prikazivati linkove na druge stranice tvog sitea.
`<meta name="google" content="notranslate">` neće ponuditi prijevod ove stranice.

`<meta name="format-detection" content="telephone=no">` isključuje automatsku detekciju i formatiranje telefonskih brojeva.

Za geolokaciju, koristi:
* `<meta name="ICBM" content="50.167958, -97.133185">`
* `<meta name="geo.position" content="50.167958;-97.133185">`
* `<meta name="geo.placename" content="Rockwood Municipality, Manitoba, Canada">`
* `<meta name="geo.region" content="ca-mb">`

## <a>

`href` definira link na resource. Može biti URL ili URL fragment (počinje s `#`). Specijalni fragment `#top` uvijek vraća na vrh stranice.

`target` definira gdje prikazati resource. Vrijednost je ime browsing contexta (taba, windowa ili framea). Specijalni targeti su:
* `_self` isti context, default.
* `_blank` novi context.
* `_parent` parent context, ili self, ako parent ne postoji.
* `_top` vršni parent context ili self.

`hreflang` definira jezik resourca. Dobro za SEO. `type` definira MIME type resourca. Nije nužno staviti, ali browser možda to jednom bude koristio.

`rel=nofollow` znači da link nema povezanosti sa siteom, SEO neka ga ignorira. Dodaj na korisničke linkove u komentarima.
`rel=noopener` znači da browser neće dati `window.opener` pristup pri otvaranju resourca. Koristi za korisničke linkove zbog sigurnosti.
`rel=noreferrer` znači da browser neće poslati `Referer` header pri dohvaćanju resourca.

`download` znači da će klik na link izazvati download. Value je ime pod kojim će biti savean. Vrijedi samo za same-origin. Može se koristiti i ako je href data-uri. _EDGE 13+, Safari ne podržava_

`ping` klik na link će poslati POST na URL u valueu. Može se koristiti za tracking klikova bez dodatnog js-a. _Samo Chrome_

## <iframe>

`src` definira URL resourca. Alternativno, `srcdoc` sadrži content koji će se prikazati. Overridea `src`. _Chrome i FF_

`name` dopušta targetiranje iframea s `target=`.

`allowfullscreen` dozvoljava korištenje full screen API-ja. _IE9+_

`sandbox` definira što je dopušteno u iframeu. Ako je dodan na iframe, ništa nije dopušteno dok se eksplicitno ne dopusti. _IE 11+_
* `allow-forms` (submitting)
* `allow-modals` (alert)
* `allow-scripts` (dopušta js),
* `allow-top-navigation` (možeš koristiti `window.top`).
* `allow-orientation-lock`, `allow-pointer-lock`, `allow-popups`, `allow-popups-to-escape-sandbox` (dopušta popupe koji ne nasljeđuju sandbox ograničenja),

Iframe, čak i cross-origin, može redirectati tab u kojem je embeddan pomoću `window.top.location.href=`. Koristi `sandbox` da to spriječiš.

XSS na jednoj stranici može pomoću `iframe`a pristupiti svim stranicama na toj domeni. `X-Frame-Options: DENY` da spriječiš.

## <img>

Nakon što je parsirao HTML, browser automatski skida sve `<img src>` slike, čak i one koje nisu vidljive korisniku. Ako želiš to izbjeći (jer imaš tisuću slika na stranici), koristi *lazy load* - js koji će postaviti `src` tek kad element uđe u viewport.

Ako želiš imati responzivan image, koristi `srcset` s listom verzija imagea. Browser će odlučiti koju da upotrijebi. `src` atribut koristi se kao fallback.

`alt` je obavezan atribut, pa makar i prazan. Ako slika ima ikakvo značenje, stavi njen tekstualni opis.

## <button>

Ako nešto treba biti klikabilno, koristi `button`. Ozbiljno. Super je `button`.

## Inputs

`<select>`

option, optgroup

`<datalist>` definira moguće vrijednosti za `<input>` elemente. Npr.
`<datalist id="email-list">` napunjen `<option>` elementima s emailovima može se iskoristiti pomoću `<input type="email" list="email-list"`> koji će se prikazati kao dropdown u kojeg se može pisati.
Pripazi samo jer je bugovit još na dosta browsera.

## Content sectioning

`<section>` je generična sekcija dokumenta, najčešće s headingom. Koristi za strukturiranje umjesto `<div>`.

`<h1>`, `<h2>`, ... `<h6>` predstavlja naslove unutar dokumenta. Koristi ih po redu.

`<article>` je cjeloviti dio teksta unutar dokumenta, npr. članak, post na forumu ili blogu.

`<header>` je uvodni dio dokumenta koji može sadržavati logo, naslove, navigaciju, search form i sl. `<footer>` je završni dio koji najčešće sadrži informacije o autoru, linkove na vezane članke, copyright i sl.

`<nav>` je dio dokumenta s linkovima na ostale stranice.

`<aside>` je dio dokumenta usputno vezan uz sadržaj, npr. sidebar.

## Tekst

`<q>` je kratki inline citation.
`<blockquote>` je duži citirani komad teksta. Njegov `cite` atribut koristi za URL resourca odakle je preuzet, a `<cite>` element unutar njega za ime autora ili djela.

`<details> <summary> Just this </summary> Blah blah </details>` browser defaultno skriva sadržaj detailsa, a summary prikazuje s klikabilnom strelicom. _Osim IE_

`<strong>` je tekst velike važnosti.
`<em>` je tekst čije naglašavanje mijenja značenje rečenice.
`<mark>` je tekst relevantan za usera, npr. hightlight search termova.
`<small>` sadržaj manje važnosti, npr. copyright u footeru.

`<ins>` je tekst koji se dodao u nekoj verziji. Atributi `cite` za URL i `timestamp` za vrijeme dodavanja.
`<del>` za tekst koji je obrisan, i `<s>` za tekst koji je zamijenjen nekim drugim. `<del>` i `<s>` možeš CSS-om sakriti, ili precrtati.



## <dfn> i ostali (TODO)

https://developer.mozilla.org/en/docs/Web/HTML/Element/dfn

`<kbd>` predstavlja keyboard input, npr. `Press <kbd>A<kbd> to continue`. Može biti tipka ili riječ koja se može upisati.
`<samp>` predstavlja output nekog programa, npr. `The screen will say <samp> An error has occured </samp>`.

`<pre>` i `<code>`


## Cross origin

Elementi `img`, `video`, `audio`, `link` i `script` po defaultu neće koristiti CORS za dohvaćanje resourca. Ako imaju atribut `crossorigin="anonymous"` koristit će CORS bez credentialsa (cookija, basic autheticationa). Uz atribut `crossorigin="use-credentials"` koristit će CORS s credentialsima.

## Referrer

Browser provjerava hoće li slati referrera ovim redom:
1. `Referrer-Policy` http header
2. `<meta name="referrer">` u headu
3. `referrerpolicy` html atribut
4. `noreferrer` html atribut
5. nasljeđivanje od parent contexta

`<meta name="referrer content=...>` i `<a referrerpolicy>` primaju iste vrijednosti:
* `no-referrer` neće se ništa slati.
* `no-referrer-when-downgrade` neće slati s HTTPS na HTTP. (default)
* `same-origin` slati će se samo unutar istog origina.
* `origin` svima se šalje samo origin, ne i path.
* `origin-with-cross-origin` unutar istog origina šalje se path, inače samo origin.
* `unsafe-url` šalje se cijeli URL svima. Nije safe, jer šalje i na HTTP.

# Literatura:

* Kul predavanja o elementima: https://vimeo.com/webconferences/videos
