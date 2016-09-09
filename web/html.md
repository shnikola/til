# HTML

## Viewport meta tag
Viewport je vidljivi dio dokumenta, odnosno browserov prozor (minus toolbari i sl).
Kad mobile browser otvara stranicu, pretpostavlja da želimo vidjeti čitavu u desktop veličini,
pa postavlja viewport width na `960px`. I onda sve izgleda sitno i odzumirano.
  * `<meta name="viewport" content="width=320">` postavlja tu širinu na 320px.
  * `<meta name="viewport" content="width=device-width">` još bolje, postavlja da prati širinu uređaja.
  * `<meta name="viewport" content="initial-scale=1">` postavlja početni scale na 1:1 - nema zoomiranja.
  * `<meta name="viewport" content="maximum-scale=1">` disabla zoom. Nemoj koristiti.


## <a>
**Attributes:**
`href` - link na resource. Može biti URL ili URL fragment (počinje s `#`). Specijalni fragment `#top` uvijek vraća na vrh stranice.
`target` - gdje prikazati resource. Vrijednost je ime browsing contexta (taba, windowa ili framea). Specijalni targeti su:
  *_self* (isti context, default),
  *_blank* (novi context),
  *_parent* (parent context, ili self, ako parent ne postoji),
  *_top* (vršni parent context ili self)

`hreflang` - jezik resourca. Dobro za SEO.
`rel` - relationship linka i resourca. Dobro za SEO.
  *nofollow*: link nema povezanosti sa siteom, SEO neka ga ignorira (npr. linkovi u komentarima na blogu)
  *noopener*: browser neće dati `window.opener` pristup pri otvaranju resourca
  *noreferrer*: browser neće poslati `Referer` header pri dohvaćanju resourca
`type` - MIME type resourca. Nije nužno staviti, ali browser možda to jednom bude koristio.

`download` - klik na link će izazvati download. Value je ime pod kojim će biti savean. Vrijedi samo za same-origin. Može se koristiti i ako je href data-uri. _EDGE 13+, Safari ne podržava_
`ping` - klik na link će poslati POST na URL u valueu. Može se koristiti za tracking klikova bez dodatnoj js-a. _Samo Chrome_
`referrerpolicy` - definira koji `Referer` header slati pri dohvaćanju resourca. _Chrome i FF iza flaga_
  * `no-referrer` nikad ne šalji ništa
  * `no-referrer-when-downgrade` default, šalji samo na HTTPS
  * `origin` šalji samo domenu
  * `origin-when-cross-origin` šalji samo domenu kad je cross-origin, inače i path
  * `unsafe-url` šalji domenu i path

**Kada koristiti target=_blank:**
https://css-tricks.com/use-target_blank/
Otvaranje novog taba skoro uvijek treba prepustiti korisniku. Jedini scenariji kad je opravdano su slučaj kada korisnik radi na nečem ili je pokrenuo audio/video, gdje bi otvaranje linka u istom tabu prekinulo taj rad.


## <iframe>
**Attributes:**
`src` URL resourca
`srcdoc` sadrži content koji će se prikazati. Overridea `src`. _Chrome i FF_
`name` dopušta targetiranje iframea s `target=`

`allowfullscreen` dozvoljava korištenje full screen API _IE9+_
`referrerpolicy` definira koji `Referer` header slati pri dohvaćanju resourca. _Chrome i FF iza flaga_
`sandbox` definira što dopušta u iframeu. Ako je dodan na iframe, ništa nije dopušteno dok se eksplicitno ne doupusti. _IE 11+_
  * `allow-forms` (submitting), `allow-modals` (alert), `allow-orientation-lock`, `allow-pointer-lock`, `allow-popups`, `allow-popups-to-escape-sandbox` (dopušta popupe koji ne nasljeđuju sandbox ograničenja), `allow-scripts` (dopušta js), `allow-top-navigation` (možeš koristiti `window.top`).

**Security:**
* `iframe`, čak i cross-origin, može redirectati tab u kojem je embeddan. Koristi `sandbox` da spriječiš.
* *XSS* na jednoj stranici može pomoću `iframe`a pristupiti svim stranicama na toj domeni. `X-Frame-Options: DENY` da spriječiš.
* *Clickjacking* je kad napadač na svojoj stranici stavi nevidiljivi `iframe` na tvoju stranicu, te prevarom natjera da klikneš na njega i npr. obrišeš sve mailove.


## <button>
Ako nešto treba biti klikabilno, koristi `button`. Ozbiljno. Super je `button`.


## <details> i <summary>
```
<details>
  <summary> More </summary>
  Blah blah
</details>
```
Chrome, Safari i Android nativno prikažu exapandable "> More". Firefox i IE, još ne :/


# Literatura:
  * Kul predavanja o elementima: https://vimeo.com/webconferences/videos
