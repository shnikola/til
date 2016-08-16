# HTML

## Viewport meta tag
Kad mobile browser otvara stranicu, pretpostavlja da želimo vidjeti čitavu u desktop veličini,
pa postavlja viewport width na 960px. I onda sve izgleda sitno i odzumirano.

`<meta name="viewport" content="width=320">` postavlja tu širinu na 320px.
`<meta name="viewport" content="width=device-width">` još bolje, postavlja da prati širinu uređaja.
`<meta name="viewport" content="initial-scale=1">` postavlja početni scale na 1:1 - nema zoomiranja.
`<meta name="viewport" content="maximum-scale=1">` disabla zoom.


## <details> i <summary>
```
<details>
  <summary> More </summary>
  Blah blah
</details>
```
Chrome, Safari i Android nativno prikažu exapandable "> More". Firefox i IE, još ne :/


## Optimiziranje imagea
http://blog.pamelafox.org/2014/01/improving-front-page-performance.html
1. tinypng za optimiziranje veličine
2. data-uri za manje slike koje se samo jednom pojavljuju
3. Spriteovi/font za ikonice
4. Delayed load za veće slike, videove i iframeove.


## <a> attributes
**Basic:**
`href` - link na resource. Može biti URL ili URL fragment (počinje s `#`). Specijalni fragment `#top` uvijek vraća na vrh stranice.
`target` - gdje prikazati resource. Vrijednost je ime browsing contexta (taba, windowa ili framea). Specijalni targeti su:
  *_self* (isti context, default),
  *_blank* (novi context),
  *_parent* (parent context, ili self, ako parent ne postoji),
  *_top* (vršni parent context ili self)

**SEO:**
`hreflang` - jezik resourca. Dobro za SEO.
`rel` - relationship linka i resourca. Dobro za SEO.
  *nofollow*: link nema povezanosti sa siteom, SEO neka ga ignorira (npr. linkovi u komentarima na blogu)
  *noopener*: browser neće dati `window.opener` pristup pri otvaranju resourca
  *noreferrer*: browser neće poslati `Referer` header pri dohvaćanju resourca
`type` - MIME type resourca. Nije nužno staviti, ali browser možda to jednom bude koristio.

**Experimental:**
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
