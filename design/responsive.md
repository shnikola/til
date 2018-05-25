# Responsive Design

## Responsive Images

https://cloudfour.com/thinks/responsive-images-101-definitions
https://cloudfour.com/thinks/responsive-images-201-client-hints

Dva su slučaja za korištenje responzivnih imagea:
* *Resolution switching*: kada želiš imati više veličina iste slike (npr, za različite dimenzije ili pixel density ekrana)
* *Art Direction*: kada želiš imati različite verzije iste slike (npr. manja veličina koja je cropana, a ne samo skalirana, ili prebacivanje iz landscapea u portrait za uže ekrane).

`<img src="normal.jpg">` je dovoljan ako je razlika između veličina slika neznatna, ili ako koristiš SVG.

`<img srcset="normal.jpg 1x, retina.jpg 2x">` ako je veličina slike fiksna i želiš samo imati različite pixel density.

`<img srcset="cat-640.jpg 640w, cat-1280.jpg 1280w" sizes="(max-width: 480px) 100vw, 254px">` ako je veličina slike responzivna.

`srcset` je lista verzija imagea i njihovih stvarnih širina. Browser počinje loadati slike prije nego zna njihove širine (CSS se loada asinkrono), pa je potreban i `sizes` atribut koji opisuje širinu slike u odnosu na viewport kao dodatan hint. Da, šugavo je imati prezentacijske detalje u HTMLu, ali nema drugog rješenja.

`<picture>` ako želiš koristiti različite slike (art direction). Za razliku od `srcset`, browser ih *mora* koristiti.
* `<source>` unutar elementa ima `media` atribut s media querijem i `srcset` s imageima. Browser će odabrati `source` s prvim matchanim media querijem.
* `type` za korištenje različitih novih encodinga (`Web-P`, `JPEG-2000`), browser će odabrati onaj koji podržava.
* `<img>` unutar elementa je obavezan za fallback starijih browsera.
* `Picturefill` polyfill za stare browsere.

Za CSS (npr. `background-image`):
* `image-set("normal.jpg" 1x, "retina.jpg" 2x)` je odličan za hintanje, ali ga nitko živ ne podržava još _Chrome_
* `@media (min-resolution: 2dppx)` u međuvremenu, koristi media querije (fallback s `-webkit-min-device-pixel-ratio: 2`)

Kako odabrati breakpointove? Eh, dobro pitanje. Recimo da odrediš koliki ti je performance budget (npr. `20 KB`) i onda generiraš slike u koracima po 20.

Alternativa definiranju u HTMLu je koristiti *Client Hints*.
Pri dohvaćanju imagea browser šalje `DPR` (device pixel ratio) i `Width` headere. Server odlučuje o veličini.
`Accept` header za type slike, `src=normal` (bez ekstenzije). Server odlučuju o typeu.
Nažalost, browseri još ne podržavaju. Dok ne budu, koristi puni `srcset`.

## Responsive Typography

http://typecast.com/blog/a-more-modern-scale-for-web-typography

Omjer headera, teksta i line-heighta ne može biti isti za desktop i za mobile.
Npr. ako su linije duže, line-height mora biti veći. Na linku gore su predloženi omjeri za različite breakpointe.

## Intrinsic Ratio

http://alistapart.com/article/creating-intrinsic-ratios-for-video

Ako želiš održati ratio širine i visine elementa (npr. 16:9 videa), ovo je metoda. Prvo, izračunaj omjer, npr. `9:16` = `56.25%`

Wrapper elementu postavi `position: relative; height: 0; padding-bottom: 56.25%;`. `padding` je definiran postotkom u odnosu na `width` pa će ovo raditi.

Unutrašnjem elementu (videu) postavi `position: absolute; top: 0; left: 0; height: 100%; width: 100%;` da se stretcha.

## CSS Locks

http://fvsch.com/code/css-locks

Ako želiš responzivno mijenjati property poput `font-size` za koje postotci ne gledaju `width`, moraš koristiti breakpointe.

Ako želiš da se kontinuirano i fluidno mijenja veličina, možeš koristiti `vw`.

Ako usto još želiš da imaš definirani minimum, maksimum i linerni rast između, možeš koristito ovu kompliciranu metodu s `calc`.

## Responsive Email Design

https://litmus.com/blog/understanding-responsive-and-hybrid-email-design

Koristi tablice s `width: 100%` i `max-width`.

Ako trebaš podržavati Outlook, dodaj condition comente s fiksnom širinom `[if (gte mso 9)|(IE)]`

Za složenije slučajeve koristi media querije, ali imaj na umu da ih pola email clienta ne podržava (npr. Gmail).

## Responsive Navigation Patterns

http://bradfrost.com/blog/web/responsive-nav-patterns

Kako napraviti responsivnu navigaciju:
* *Top Nav*: samo je ostavi na vrhu stranice. Dobra za malo linkova, ali loše ako zauzima više redova.
* *Footer Anchor*: prebačena u footer, s anchorom na vrhu stranice. Ostavlja više mjesta na vrhu, ali nije baš elegantno i skakanje može zbuniti korisnika.
* *The Toggle*: button na vrhu stranice iz kojeg ispadne navigacija. Elegantno i jednostavno, samo pazi da animacija bude glatka.
* *Left Nav*: klik na button slidea navigaciju s lijeve strane. Daje puno prostora, ali je zeznuta za dobro izvesti.

Ako imaš kompliciranu navigaciju:
* Koristi analyticse da odrediš koji linkovi su ti nebitni (npr. terms ne trebaju biti u glavnog navigaciji).
* Ako imaš milijun sekcija i stranica, istakni *search*.

* *The Multi Toggle*: nested accordion meniji na vrhu. Nije pretjerano fancy, ali je dovoljno dobro.
* *Right-To-Left*: klik na kategoriju slidea lijevo na podmeni. Fancy, ali komplicirano.
* *Skip-the-subnav*: Klik na nad-kategoriju otvara novu stranicu s podnavigacijom. Zahtjeva reload, ali user najčešće želi ići tamo ako je već kliknuo.

## Feature detection

https://codeburst.io/the-only-way-to-detect-touch-with-javascript-7791a3346685

Ne prilagođavaj stranicu onome što korisnikov uređaj *može*, nego onome što korisnik radi. Npr. ne detektiraj da li uređaj podržava touch (što je teško već samo po sebi) nego da li se dogodio `touchstart` event.
