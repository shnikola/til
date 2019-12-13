# Responzivnost

## Responsive Images

Dva su slučaja za korištenje responzivnih imagea:

**Resolution switching** je kada želiš imati više veličina iste slike (npr, za različite dimenzije ili pixel density ekrana)

`<img src="normal.jpg">` je dovoljan ako je razlika između veličina slika neznatna, ili ako koristiš SVG.

`<img srcset="normal.jpg 1x, retina.jpg 2x">` ako je veličina slike fiksna i želiš samo imati različite pixel density.

`<img srcset="cat-640.jpg 640w, cat-1280.jpg 1280w" sizes="(max-width: 480px) 100vw, 254px">` ako je veličina slike responzivna.

`srcset` je lista verzija imagea i njihovih stvarnih širina. Browser počinje loadati slike prije nego zna njihove širine (CSS se loada asinkrono), pa je potreban i `sizes` atribut koji opisuje širinu slike u odnosu na viewport kao dodatan hint. Da, šugavo je imati prezentacijske detalje u HTMLu, ali nema drugog rješenja.

**Art Direction** je kada želiš imati različite verzije iste slike (npr. manja veličina koja je cropana, a ne samo skalirana, ili prebacivanje iz landscapea u portrait za uže ekrane).

`<picture>` element koristi ako želiš imati različite slike. Za razliku od `srcset`, browser ih *mora* koristiti.
* `<source>` unutar elementa ima `media` atribut s media querijem i `srcset` s imageima. Browser će odabrati `source` s prvim matchanim media querijem.
* `type` za korištenje različitih novih encodinga (`Web-P`, `JPEG-2000`), browser će odabrati onaj koji podržava.
* `<img>` unutar elementa je obavezan za fallback starijih browsera.
* `Picturefill` polyfill za stare browsere.

Za slike u CSS-u, službeno rješenje je
`background-image: image-set("normal.jpg" 1x, "retina.jpg" 2x)`, ali ga nijedan browser osim Chromea još ne podržava. Dok se to ne dogodi, koristi media query tipa `@media (min-resolution: 2dppx)`.

**Client hints** je metoda u kojoj umjesto klijenta, server odlučuje koju sliku poslati. Browser pri dohvaćanju imagea serveru šalje `DPR` (device pixel ratio) i `Width` headere, a server šalje nazad najbolju sliku za te parametre. Nažalost, browseri još ne podržavaju.

## Responsive Typography

Heading s `font-size: 3em` će izgledati dobro na desktopu, ali na mobilnom ekranu će zauzimati previše mjesta. Zato pripazi da headingsi za mobilne uređaje budu manji (`h1: 2em`, `h2: 1.625em`), ako i line height (`1.25em` za body).

## Responsive Emails

Nema tu neke pomoći, većina klijenata ne podržava media querije. Koristi tablice s `width: 100%` i `max-width`.

Ako trebaš podržavati Outlook, dodaj condition commente s fiksnom širinom `[if (gte mso 9)|(IE)]`

## Pixel Density

**Pixel Density** je broj piksela po inchu (**ppi**). Prvi Mac je imao `72ppi`, moderniji ekrani imali su `160ppi`, a prva Retina oko `300ppi`.

Retina je odvostručila broj piksela koji se mogu prikazati, pa je slika turbo kristalna. Ali pošto su pikseli postali upola manji, uvedena je apstraktna mjerna jedinica point (**pt**) kako bi se lakše komuniciralo. Na normalnim ekranima `1pt = 1px`, na retina ekranima `1pt = 2px`.

## Intrinsic Ratio

Za održati omjer širine i visine responzivnog elementa (npr. 16:9 videa), izračunaj omjer (npr. `9:16` = `56.25%`).

Wrapper elementu postavi `position: relative; height: 0; padding-bottom: 56.25%;`. Štos je da je `padding` definiran postotkom u odnosu na `width`, što je inače blesavo ali ovdje nam koristi.

Unutrašnjem elementu postavi `position: absolute; top: 0; left: 0; height: 100%; width: 100%;` da se stretcha.

## Full background video

Koristi `video` tag sa sljedećim propertijima: `muted` (ne želimo smetati korisniku), `autoplay`, `loop`, i `playsinline` (da radi na iOS).

Da ispuni cijelu stranicu u CSS dodaj `video { object-fit: cover; width: 100vw; height: 100vh; position: fixed; top: 0; left: 0; }`.

Za accessability: dodaj keyboard accessible pause button na stranicu. Osiguraj da tekst preko videa bude čitljiv. Pripazi na bandwith.

# Literatura

* https://cloudfour.com/thinks/responsive-images-101-definitions
* https://cloudfour.com/thinks/responsive-images-201-client-hints
