# HTML

## Isprika
https://html.spec.whatwg.org/multipage/introduction.html#design-notes
"It must be admitted that many aspects of HTML appear at first glance to be nonsensical and inconsistent.

"HTML, its supporting DOM APIs, as well as many of its supporting technologies, have been developed over a period of several decades by a wide array of people with different priorities who, in many cases, did not know of each other's existence.

"Features have thus arisen from many sources, and have not always been designed in especially consistent ways. Furthermore, because of the unique characteristics of the Web, implementation bugs have often become de-facto, and now de-jure, standards, as content is often unintentionally written in ways that rely on them before they can be fixed."


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


## <img>
Nakon što je parsirao HTML, browser automatski skida sve `<img src>` slike, čak i one koje nisu vidljive korisniku. Ako želiš to izbjeći (jer imaš tisuću slika na stranici), koristi *lazy load* - js koji će postaviti `src` tek kad element uđe u viewport.

Ako želiš imati responzivan image, koristi `srcset` s listom verzija imagea. Browser će odlučiti koju da upotrijebi. `src` atribut koristi se kao fallback.

`alt` je obavezan atribut, pa makar i prazan. Ako slika ima ikakvo značenje, stavi njen tekstualni opis.


## <button>
Ako nešto treba biti klikabilno, koristi `button`. Ozbiljno. Super je `button`.


## <details> i <summary>
`<details>
  <summary> More </summary>
  Blah blah
</details>`
Chrome, Safari i Android nativno prikažu exapandable "> More". Firefox i IE, još ne :/


## favicon.ico
Treba ih više nego što misliš. Koristi https://realfavicongenerator.net


# Literatura:
  * Kul predavanja o elementima: https://vimeo.com/webconferences/videos
