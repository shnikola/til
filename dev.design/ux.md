# User Experience

## Animation in UI

Animacija se uglavnom uzima kao negativna stvar jer dodaje još stvari koje se bore za korisnikovu pažnju. Ali ako se potrudiš da je učiniš konzistentnom i diskretnom, može pridonijeti funkcionalnosti stranice.
* Ljudi ne vole stvari koje dolijeću (npr. popupi i modali), koristi stvari koje se morphaju (npr. button koji se pretvori u modal)
* Koristi custom loadere, ljudi će duplo duže zadržati pažnju.

## Validacija formi

Ne želiš da error iskoči čim user počne tipkati - validiraj nakon što user napusti field, ili kad unese zadanu veličinu teksta (npr. za kreditnu karticu).

S druge strane, ako na fieldu već postoji error postoji, ukloni ga čim user promijeni input u validan - nemoj čekati da napusti field.

Pozitivna validacija (zeleni check) pomaže.

## Validacija emaila

Nijedna regex validacija te ne može zaštiti od toga da korisnik ukuca pogrešni email. Umjesto toga, provjeri da li email sadrži `@` i, bitnije, zahtjevaj da korisnik potvrdi primitak emaila.

## target=_blank

Otvaranje novog taba skoro uvijek treba prepustiti korisniku. Jedini scenariji kad je opravdano su slučaj kada korisnik radi na nečem ili je pokrenuo audio/video, gdje bi otvaranje linka u istom tabu prekinulo taj rad.

## Content Shifting

Prilikom početnog loada stranice, dinamični elementi (npr. adovi ili partiali koji se dodaju ajaxom) mogu pomicati sadržaj ispod sebe što je nezgodno za korisnika. Zato uvijek im uvijek definiraj `height` (ako znaš visinu) ili `min-height`.

## Responzivna navigacija

Ako imaš samo par linkova u headeru, super, samo ih ostavi gore.

Ako imaš više linkova, dodaj button koji će ili otvoriti full screen menu ili slideati sidebar s menijem.

Ako imaš kompliciranu navigaciju, analitikom probaj odrediti koji linkovi su ti nebitni (npr. terms ne trebaju biti u glavnog navigaciji). Ako imaš milijun sekcija i stranica, istakni search.

## Literatura

* https://developers.google.com/web/fundamentals/design-and-ui/ux-basics/
