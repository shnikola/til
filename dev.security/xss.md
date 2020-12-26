# XSS (Cross-Site Scripting)

Napadač umeće javascript naredbu u stranicu kojoj korisnik vjeruje. S njime može očitati korisnikov cookie (ukrasti njegov session id) ili učitati bilo kakvu dodatnu skriptu (npr. za keylogging).

Ovaj napad zloupotrebljava korisnikovo povjerenje u site na kojem se nalazi.

## Vrste XSS-a

*Reflected XSS* šalje naredbu kroz URL parametar koji se renderira na stranici. Napadač mora natjerati korisnika da ode na URL s tim parametrom (može biti skriven shortenerom), ili posjeti napadačevu stranicu na kojoj je iframe s tim URL-om.

*Stored XSS* napadač šalje naredbu na server (npr. kao komentar na post) gdje se on perzistira i renderira za sve posjetitelje.

*DOM XSS* napadač šalje naredbu koja ne ide do servera (npr. `window.location.hash` + `innerHtml`).

*Self XSS* napadač uvjeri korisnika da pokrene naredbu kroz Developer Tools konzolu.

## Metoda napada

`<script> evil() </script>` ako ubacuješ unutar elementa (s tim da browser neće poketati `<script>` nakon što je stranica loadana).

`<img onerror=evil>` ili `<p onmouseover>` ako ubacuješ unutar taga.

`<a href=javascript:evil()>` ako dopuštamo da korisnik odabere URL linka.

Email aplikacije često urlove automatski pretvaraju u linkove, pa ih se tu da zbuniti, poput: `https://www.domain.com/abc#<script></script>https://domain.com/abc`

Aplikacije koje sanitiziraju HTML se daju zbuniti s polovičnim tagovima poput:
`<style></sty</style> <style>le><script>alert(1)</script></style>`

## Obrana

Browseri imaju ugrađene XSS filtere (npr. prepoznaju ako je source skripte u URLu) i automatski ga blokiraju. Ako si lud, možeš disablati to ponašanje s `X-XSS-Protection: 0` headerom.

Za sve ostalo, escapeaj korisničke podatke pri renderiranju. Pripazi da različito escapaš ako se ubacuje u HTML element, atribut, `<style>` ili `<script>` tag.

Ako koristiš URL od korisnika, provjeri da počinje s `http`, a ne s `javascript:` ili `data:`.

Nemoj koristiti nikakve korisničke podatke za internu pohranu (npr. S3 file nameove) - uvijek generiraj UUID.

## Content Security Policy

*Content Security Policy* je najbolji način za smanjiti rizik od XSSa. Browser prima `Content-Security-Policy: script-src 'self' http://apis.google.com` header s instrukcijama koje sadržaje smije prihvatiti s kojih origina. Neke od direktiva su:
* `script-src`, `style-src` definira origine za skripte i css.
* `img-src`, `media-src` origini za slike, audio i video.
* `child-src` origini za workere i embedded frameove.
* `connect-src` origini na koje se možeš spojiti (XHR, WebSockets, EventSource)
* `default-src` definira dopuštene origine za sve.

Instrukcije mogu imati vrijednost u obliku origina (`https://host1.com`), primati wildcardove (`*://*.google.com:*`) ili keyworde: `'none'` ne dopušta ništa, `'self'` dopušta s trenutnog origina, ali ne i poddomena.

Ali najveća sigurnost koju CSP pruža je da zabranjuje sav inline scripting: to je jedini efektivan način za obraniti se od inline XSS-a. To uključuje `<script>` elemente, `javascript:` urlove i inline event handlere poput `onclick`. Isto vrijedi i za inline `<style>`. Ako baš moraš dozvoliti inline script i style, koristi `unsafe-inline` keyword u `script-src` i `style-src`.

CSP također brani od napada koji koriste `eval()`, `new Function()`, `setTimeout(string)` i `setInterval(string)` tako što ih u potpunosti blokira.

Možeš podesiti CSP da šalje report s blokiranim resourcom koristeći direktivu `report-uri /my-report-parser`. Browser će poslati POST s JSONom na dani url.
Ukoliko tek postavljaš CSP i želiš samo primati reporte, ali ne i blokirati resource, koristi `Content-Security-Policy-Report-Only` header.

## Iframe sigurnost

Ako napadač uspije napraviti XSS na samo jednoj tvojoj stranici, može na njoj kreirati iframe s bilo kojom drugom stranicom na istoj domeni. Ako ti ne treba podrška za iframe, najjednostavnija obrana je `X-Frame-Options: DENY`.

Ako koristiš iframe tagove, svoje ili tuđe, imaj na umu da imaju pristup parent stranici. Čak i cross-origin iframe može redirectati parent stranicu pomoću `window.top.location.href=`.

Kako bi to spriječio, dodaj `sandbox` atribut na `<iframe>`. Ako je dodan na iframe, ne dopušta ništa što se eksplicitno ne dopusti:
* `allow-forms` (submitting)
* `allow-modals` (alert)
* `allow-scripts` (dopušta js),
* `allow-top-navigation` (možeš koristiti `window.top`),
* `allow-orientation-lock`, `allow-pointer-lock`, `allow-popups`, `allow-popups-to-escape-sandbox` (dopušta popupe koji ne nasljeđuju sandbox ograničenja).

## Username XSS

https://twitter.com/EdOverflow/status/954093588362809345

Neki siteovi za svakog usera generiraju url, npr. `www.twitter.com/nikola`. Čovjek je napravio račun pod imenom `keybase.txt`, na profilni ekran pasteao konfirmacijski kod, i time dobio ownership nad siteom u Keybaseu.

## Pastejacking

*Pastejacking* je napad gdje korisnik kopira neki tekst sa weba, (npr. shell naredbu), a napadač u njegov clipboard umjesto tog teksta stavlja nešto drugo.

Napadač na svojoj stranici (ili pomoću XSS-a) osluškuje `copy` ili `keydown` event, pri čemu pomoću `document.execCommand('copy')` ili `e.clipboardData.setData` stavlja svoj sadržaj u korisnikov clipboard.

Ako se radi o shell naredbi, napadač može dodati `\r\n` na kraj kako bi se ona izvela odmah pri pasteanju, te dodati `clear` i `echo <origalna naredba>`, ne dajući priliku korisniku da uopće primjeti da se zla naredba izvela.

Nema nekog pametnog načina za obraniti se, osim da vrlo pažljivo provjeriš cliboard prije nego pasteaš nešto u terminal (npr. koristeći Jumpcut).

## npm kao vektor napada

https://hackernoon.com/im-harvesting-credit-card-numbers-and-passwords-from-your-site-here-s-how-9a8cb347c5b5

- teoreteski, library za ispis logova u boji. Ne treba biti ni popularan, dovoljno je napraviti pull requestove na popularnije librarije u kojem se dodaje logiranje.
- Submita sve forme s password ili credit card inputima na neku fake analytics domenu.

Kako izbjeći da se primjete requestovi: kod ne šalje podatke kad su dev toolsi otvoreni, ili kad se pokreće na localhost ili dev okruženjima. Podatci se trebaju poslati samo jednom, pa je dovoljno da ih spremi u local storage i pošalje kad nitko ne gleda. Url na koji se šalje izgleda kao bilo koji drugi od 100 ad networka koje uključuješ na site.

Kod može biti neprimjećen na githubu: kod koji se šalje na `npm publish` uopće ne mora odgovarati onom na githubu, a usto može biti i minificiran tako da nitko ne može jednostavno provjeriti. Vrlo je lako obfuscirati kod tako da se `fetch` ni `XMLHttpRequest` ne pojavljuju nigdje.

Content Security Policy ovdje pomaže (osim što Chrome propušta `<link rel=prefetch>` da prođe mimo CSP pravila), ali skoro nitko ga ne koristi ispravno. Amazon i Ebay ga nemaju uopće, Google nema definiran `default-src`, Twitter i PayPal nemaju `form-action`

Poanta je: na bilo kojoj stranici na kojoj uzimaš privatne korisničke podatke, ne koristi npm module, analitiku, ad networke, niti bilo kakav kod koji nije tvoj. Ako treba, integriraj ga kao iframe na drugoj domeni (kako mu parent ne bi imao pristup zbog same-origin policy)

