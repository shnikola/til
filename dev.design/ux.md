# User Experience

## Animation in UI

Animacija se uglavnom uzima kao negativna stvar jer dodaje još stvari koje se bore za korisnikovu pažnju. Ali ako se potrudiš da je učiniš konzistentnom i diskretnom, može pridonijeti funkcionalnosti stranice.
* Ljudi ne vole stvari koje dolijeću (npr. popupi i modali), koristi stvari koje se morphaju (npr. button koji se pretvori u modal)
* Koristi custom loadere, ljudi će duplo duže zadržati pažnju.

## Inline Form Validation

https://baymard.com/blog/inline-form-validation

* Ne želiš da error iskoči čim user počne tipkati - validiraj nakon što user napusti field, ili kad unese zadanu veličinu teksta (npr. za kreditnu karticu)
* Međutim, kad error postoji na fieldu, ukloni ga čim user promijeni input u validan - nemoj čekati da napusti field.
* Pozitivna validacija (zeleni check) pomaže.

## Email validation

Nijedna regex validacija te ne može zaštiti od toga da korisnik ukuca pogrešni email. Umjesto toga, provjeri da li email sadrži `@` i, bitnije, zahtjevaj da korisnik potvrdi primitak emaila.

## Kada koristiti target=_blank

https://css-tricks.com/use-target_blank/

Otvaranje novog taba skoro uvijek treba prepustiti korisniku. Jedini scenariji kad je opravdano su slučaj kada korisnik radi na nečem ili je pokrenuo audio/video, gdje bi otvaranje linka u istom tabu prekinulo taj rad.

## Reduce Content Shifting On Page Load

https://www.smashingmagazine.com/2016/08/ways-to-reduce-content-shifting-on-page-load

Za elemente kojima znaš visinu, definiraj `height`, u media querijima ako treba.

Za elemente kojima znaš ratio (npr. `16:9`), koristi *intrinsic ratio* s `padding-bottom: <h/w*100>%` (kao za vimeo na sh&t siteu).

Za elemente kojima ne znaš visinu, nađi `min-height` da minimiziraš skakanje.

## Tekst

`font-size: 16px` i `line-height: 1.5` su prilično siguran default za body tekst. Još bolje `font-size: 18px` i `line-height: 16px`. Boja `#333` ili tamnija.

Širina stupca teksta neka bude max `700px` (nešto što bi Wikipedija mogla uzeti u obzir).

All caps mogu biti teški za čitanje, koristi `letter-spacing: 1.4` da mu daš oduška.

## Shadows

Bijelom tekstu na svijetloj pozadini dodaj lagani shadow (`text-shadow: 0 1px 2px rgba(0, 0, 0, 0.2)`) za bolju čitljivost i da iskoči.

Za hover na button podigni za 1px (čini se kao da iskoči) i povećaj drop shadow spread.

Box shadow izgleda prirodnije s vertikalnim offsetom i manjim spreadom (umjesto `box-shadow: 0px 0px 8px` bolje je `box-shadow: 0px 2px 4px`).

## Colors

Gradient tamnjenja (npr. `hsl(194, 100%, 100%)` do `hsl(194, 100%, 80%)`) će biti življi ako im pomakneš hue za 10-20 stupnjeva (npr. `hsl(194, 100%, 100%)` do `hsl(208, 100%, 80%)`).

Čisti sivi tekst izgleda čudno na obojenoj pozadini. Umjesto toga, koristi hue pozadine (npr. ako je pozadina `hsl(192, 56%, 56%)`, boja teksta neka bude `hsl(192, 17%, 99%)`).

Ako su ikone deblje od teksta pokraj njih, koristi boju svijetliju od teksta za neaktivni state.

Obojena linija na vrhu stranice debljine 4-6px dobro izgleda. Isto vrijedi za modale i panele.

Umjesto razdvajanja naslova panela od ostatka linijom, koristi tamniju nijansu za ostatak.

## Literatura

* https://developers.google.com/web/fundamentals/design-and-ui/ux-basics/
* https://twitter.com/i/moments/880688233641848832
