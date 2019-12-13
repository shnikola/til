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

## Form and inputs

`<form>` sakuplja inpute i definira gdje i kako će se njihove vrijednosti poslati.

`action` definira URL na koji se podatci šalju.
`method` definira HTTP metodu (GET, POST, itd.)
`enctype` definira MIME type podataka (`application/x-www-form-urlencoded` defaltno, `multipart/form-data` za fileove, `text/plain` za tekst)

`target` ime browsing contexta u kojem će prikazati rezultat sa servera. Kao i za `<a>`, može biti `_self`, `_blank`, `_parent` i `_top`.

`autocomplete="on"` poručuje browseru da prikaže dropdown s već unesenim vrijednostima za sve inpute formi. Može se definirati i za pojedinačne inpute.
`autocomplete="off"` poručuje browseru da ne prikazuje taj dropdown.
Čak i uz isključen autocomplete, browser password će i dalje ponuditi pamćenje podataka za login i autofillati ih ako korisnik to dopusti.

`novalidate` poručuje browseru da ne validira vrijednosti inputa u formi.

`<input>` se koristi za različite vrste unosa podataka. Najčešće se nalazi unutar forme, ali može biti i izvan ako koristi `form` atribut koji prima `id` forma s kojim će biti submitan. `name` je ime pod kojim će biti poslan na server.

`required` zahtjeva da se unese neka vrijednost prije submita.
`readonly` korisnik ne može mijenjati vrijednost inputa.
`disabled` korisnik ne može mijenjati vrijednost i vrijednost se neće submitati.

`autofocus` input na koji će browser fokusirati pri loadanju stranice. Smije biti samo jedan po dokumentu.
`autocomplete="off"` poručuje browseru da ne predlaže vrijednosti tijekom korisničkog unosa.

`<input type="hidden">` se ne prikazuje korisniku, ali se šalje na server.

### Checked inputs

`<input type="checkbox">` ima unaprijed zadani `value` koji će se poslati samo ako je checkbox označen. `checked` čini input unaprijed označenim.

`<input type="radio">` omogućuje odabir jednog inputa unutar grupe. Radio inputi koji imaju isti `name` stvaraju jednu grupu. `checked` čini input unaprijed odabranim.

### Text inputs

`<input type="text">` za generični tekst.
`<input type="number">` za unos brojeva. Browser prikaže strelice za inkrement/dekrement, a mobilni uređaji posebnu tipkovnicu.
`<input type="password">` korisnikov unos se prikazuje zvijezdicama.
`<input type="email">`,
`<input type="url">`,
`<input type="tel">` automatski validiraju unos, a na mobilnim uređajima prikazuju prilagođenu tipkovnicu (npr. `@` za email).
`<input type="search">` je isti kao `text` osim što nekad prikaže malu search ikonicu i keyboard enter preimenuje u "Search".

`<textarea>` za dulji unos teksta. `cols` i `rows` za definiranje broja stupaca i redova.

`minlength` i `maxlength` ograničavaju duljinu unosa u input.
`min` i `max` ograničavaju vrijednost broja za `number`. `step` definira korak inkrementa.

`pattern` prima regex koji će se validirati prije submita, npr. `[0-9]{4}`.
`inputmode` predlaže browseru koju tipkovnicu da koristi, npr. `numeric`.

`placeholder` prikazuje hint kako bi unos trebao izgledati.

### Selection inputs

`<select>` omogućuje odabir među listom opcija. `multiple` omogućuje odabir više opcija odjednom. `size` definira broj redaka u listi multiple opcija.

`<option>` elementi predstavljaju jednu opciju.
`label` ili tekst unutar elementa će se prikazati kao naziv opcije.
`value` definira vrijednost koja će se poslati na server.
`selected` određuje unaprijed definiranu opciju.
`disabled` onemogućuje odabir opcije.

`<optgroup>` se može koristiti za grupiranje opcija. `label` definira naziv grupe.

`<input type="range">` prikazuje slider od `min` do `max` sa `step` koracima.

`<input type="date">` unos `yyyy-mm-dd`. Chrome prikazuje nativni datapicker, mobilni browseri prilagođuju keyboard.
`<input type="month">` unos `yyyy-mm`.
`<input type="week">` unos `yyyy-Www`, godine i tjedna.
`<input type="time">` unos `HH:MM`.
`<input type="datetime-local">` unos `yyyy-mm-ddTHH:MM:SS.S`.

`<input type="color">` unos hex oblika, npr. `#ff0000`. Neki browseri prikazuju nativni picker.

`<datalist>` definira moguće vrijednosti za bilo koji `<input>` element. Npr.
`<datalist id="email-list">` napunjen `<option>` elementima s emailovima može se iskoristiti pomoću `<input type="email" list="email-list"`> koji će se prikazati kao dropdown u kojeg se može pisati.
Pripazi samo jer je bugovit još na dosta browsera.

### File input

`<input type="file">` prikazuje button za odabir filea. Najčešće se ne može stilizirati, ali može ga se sakriti i stilizirati njegov `<label>`.

`multiple` omogućuje odabit više fileova odjednom.
`accept` definira vrste fileova koje server prihvaća, npr. `.jpg` ili `audio/*`.
`capture` će u mobilnim browserima otvoriti kameru i koristiti snimljene podatke.

### Submitting

Za submitanje forme koristi `<input type="submit>` ili `<button>`. Oba elementa mogu overrideati postavke za slanje koristeći `formaction`, `formmethod`, `formenctype`, i `formtarget` atribute.

`<input type="reset">` prikazuje button koji klikom vraća sve inpute forme na defaultnu vrijednost.

Općenito, ako nešto treba biti klikabilno, a nije link, koristi `<button>` umjesto da stiliziraš `<a>`.

### Labels

`<label>` predstavlja opis inputa. Label se s inputom povezuje preko atributa `for` koji sadrži `id` inputa, ili tako što je input unutar label elementa.
Klikom na label prebacuju se fokus na pripadajući input, ili se toggla ako je checkbox ili radio.

Input se mogu grupirati pomoću `<fieldset>`, a svaka grupa može imati svoj `<legend>` kao prvi element fieldseta.

`<progress>` prikazuje postotak dovršenosti neke radnje. `value` i `max` za definiranje. Browseri ga prikazuju grafički.
`<meter>` prikazuje mjeru, npr. ocjenu ili temperaturu. Chrome ga prikazuje grafički

## Content sectioning

`<section>` je generična sekcija dokumenta, najčešće s headingom. Koristi za strukturiranje umjesto `<div>`.

`<h1>`, `<h2>`, ... `<h6>` predstavlja naslove unutar dokumenta. Koristi ih po redu.

`<article>` je cjeloviti dio teksta unutar dokumenta, npr. članak, post na forumu ili blogu.

`<header>` je uvodni dio dokumenta koji može sadržavati logo, naslove, navigaciju, search form i sl. `<footer>` je završni dio koji najčešće sadrži informacije o autoru, linkove na vezane članke, copyright i sl.

`<nav>` je dio dokumenta s linkovima na ostale stranice.

`<aside>` je dio dokumenta usputno vezan uz sadržaj, npr. sidebar.

## Text content

`<ol>` je ordered lista, `<ul>` je unordered. `<li>` je item unutar liste.
`<ol start=3>` počinje brojati od 3.
`<ol reversed>` ide unazad.

`<dl>` je lista termova i descriptiona. Sastoji se od pojmova `<dt>` iza kojih ide jedan ili više opisa `<dd>`.

`<pre>` predformatirani tekst, prikazuje se sa točno kako je napisan u HTML-u (sa spaceovima i svim). Defaultno je u monospace fontu.

`<figure>` predstavlja sliku ili ilustracij u s opisom, sadrži `<img>` i `<figcaption>` unutar sebe.

## Inline text

`<strong>` je tekst velike važnosti.
`<em>` je tekst čije naglašavanje mijenja značenje rečenice.
`<mark>` je tekst relevantan za usera, npr. hightlight search termova.
`<small>` sadržaj manje važnosti, npr. copyright u footeru.

`<wbr>` je word break opportunity, odnosno oznaka browseru gdje je u redu da prelomi riječ.

`<q>` je kratki inline citation.
`<blockquote>` je duži citirani komad teksta. Njegov `cite` atribut koristi za URL resourca odakle je preuzet, a `<cite>` element unutar njega za ime autora ili djela.

`<abbr>` je kratica, atribut `title` može sadržavati puni naziv.
`<dfn>` označava pojam koji se definira unutar `<section>` ili `<p>`
`<time>` označava sat ili datum. Prima `datetime=1929-11-13T19:00Z` atribut.

`<kbd>` predstavlja keyboard input, npr. `Press <kbd>A<kbd> to continue`. Može biti tipka ili riječ koja se može upisati.
`<code>` je računalni kod. Koristi samostalno za inline, ili unutar `<pre>` za duže snippete. Defaultno je u monospace fontu.
`<samp>` predstavlja output nekog programa, npr. `The screen will say <samp> An error has occured </samp>`.
`<var>` predstavlja matematički izraz, npr. `<var> x + y </var>`.

`<ins>` je tekst koji se dodao u nekoj verziji. Atributi `cite` za URL i `timestamp` za vrijeme dodavanja.
`<del>` za tekst koji je obrisan, i `<s>` za tekst koji je zamijenjen nekim drugim. `<del>` i `<s>` možeš CSS-om sakriti, ili precrtati.

## Interactive

`<details> <summary> Just this </summary> Blah blah </details>` browser defaultno skriva sadržaj detailsa, a summary prikazuje s klikabilnom strelicom. _Osim IE_

`<menu>` predstavlja grupu naredbi koje korisnik može upotrijebiti.

`<menu type="context" id="a">` je popup menu koji iskoči kad klikneš na `button` koji ima definiran `menu="a"`, ili desnim klikom na element koji ima definiran `contextmenu="m"`. Sastoji se od `<menuitem>` elemenata s akcijama i `<hr>` separatora. _FF, Edge_

`<menu type="toolbar">` je neki normalan toolbar valjda. Nitko ga još nije implementirao.

`<dialog>` predstavlja dialog box ili sličan interaktivan prozor. Ako ima atribut `open` bit će prikzan, u suprotnom neće. _Chrome_

## <iframe>

`src` definira URL resourca. Alternativno, `srcdoc` sadrži content koji će se prikazati. Overridea `src`. _Chrome i FF_

`name` dopušta targetiranje iframea s `target=`.

`allowfullscreen` dozvoljava korištenje full screen API-ja. _IE9+_

## Images

`<img>` predstavlja sliku u dokumentu. `src` definira URL resourca.

`alt` je obavezan atribut, pa makar i prazan. Ako slika ima ikakvo značenje, stavi njen tekstualni opis.

`ismap` označava da je slika karta. Ako je slika u `<a>` elementu, klik na nju će poslati na server i koordinate klika. Alternativno, `usemap="#world"` može referencirati `<map name="world">` element s `<area>` linkovima.

Ako želiš imati responzivan image, koristi `srcset` s listom urlova s različitim veličinama. Browser će odlučiti koju da upotrijebi. Za fallback koristi `src` atribut.

`<picture>` omogućuje definiranje više različith `<source>` elemenata unutar sebe od kojih browser odabire najprikladniji za prikazati pomoću `srcset` i `media` atributa. Koristi ga kada želiš imati različite verzije slike, a ne samo veličine. Za fallback stavi `<img>` element u njega.

## Multimedia

`<audio>` pušta zvučni sadržaj iz `src` atributa ili iz `<source>` elemenata unutar sebe. Po defaultu je nevidljiv, ali atribut `controls` prikazuje jednostavan player kojim korisnik može upravljati.

`preload` definira da li će se sadržaj unaprijed učitati (`none`, `metadata`, ili defaultno hoće).
`autoplay` atribut pušta sadržaj čim to može učiniti.
`loop` čini da se sadržaj pušta u loopu.
`volume` definira glasnoću od `0.0` do `1.0`.
`muted` mutea zvuk.

`buffered` je read-only atribut koji vraća `TimeRanges` koji su se učitali.
`played` je read-only atribut koji vraća `TimeRanges` koji su pušteni.

`<video>` pušta video sadržaj sadržaj iz `src` atributa ili iz `<source>` elemenata unutar sebe. `controls` atribut prikazuje kontrole za upravljanje videom.

Koristi iste atribute kao i `<audio>`, i još neke dodatne.
`poster` definira URL imagea koji će se prikazati dok se video ne počne puštati.
`playsinline` neće defaultno puštati video u fullscreenu na nekim uređajima.

`<video>` može u sebi imati više `<track>` elemenata koje sadrže subtitle u `.vtt` formatu.

## Embedded Objects

`<object>` koristi za embedanje vanjskih resursa poput PDF-a, flasha i sl. Obvezni su `data` atribut s urlom i `type` s vrstom resursa. Prije se koristio `<embed>` element, ali `<object>` je bolji jer unutar njega možeš definirati fallback.

`<object>` može u sebi imati više `<param>` za prosljeđivanje parametara.

## data-uri _IE8+_

URI koji sadrži inline data. Koriste se kako bi se uštedio request.
Format je `data:[<media type>][;base64],<data>`. (npr. `data:image/png;base64,iVBORw0KGgoAA...`)

# Literatura:

* http://htmlreference.io/
* https://google.github.io/styleguide/htmlcssguide.xml
* Kul predavanja o elementima: https://vimeo.com/webconferences/videos
