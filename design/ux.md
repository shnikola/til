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

## Kada koristiti target=_blank

https://css-tricks.com/use-target_blank/

Otvaranje novog taba skoro uvijek treba prepustiti korisniku. Jedini scenariji kad je opravdano su slučaj kada korisnik radi na nečem ili je pokrenuo audio/video, gdje bi otvaranje linka u istom tabu prekinulo taj rad.

## Reduce Content Shifting On Page Load

https://www.smashingmagazine.com/2016/08/ways-to-reduce-content-shifting-on-page-load

Za elemente kojima znaš visinu, definiraj `height`, u media querijima ako treba.

Za elemente kojima znaš ratio (npr. `16:9`), koristi *intrinsic ratio* s `padding-bottom: <h/w*100>%` (kao za vimeo na sh&t siteu).

Za elemente kojima ne znaš visinu, nađi `min-height` da minimiziraš skakanje.

## Literatura

* https://developers.google.com/web/fundamentals/design-and-ui/ux-basics/
