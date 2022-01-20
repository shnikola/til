# User Experience

## Animation in UI

Animacija se uglavnom uzima kao negativna stvar jer dodaje još stvari koje se bore za korisnikovu pažnju. Ali ako se potrudiš da je učiniš konzistentnom i diskretnom, može pridonijeti funkcionalnosti stranice.
* Ljudi ne vole stvari koje dolijeću (npr. popupi i modali), koristi stvari koje se morphaju (npr. button koji se pretvori u modal)
* Koristi custom loadere, ljudi će duplo duže zadržati pažnju.

## Error messaging

Kada u aplikaciji nešto pođe po zlu, ima smisla prikazivati pogrešku samo ako korisnik može napraviti nešto po tom pitanju. U suprotnom, bolje je problem samo zapisati u log i poslati na server.

Dobar error message treba:
1) Ispričati se zbog pogreške (ako je problem u aplikaciji)
2) Jednostavnim riječnikom objasniti što je pošlo po zlu
3) Predložiti što korisnik može napraviti.

Umjesto: `We are currently unable to check for updates.`, mnogo bolje je:
`For security reasons, we couldn’t check if an update is available. This can happen when your phone’s time and date isn’t correct. Check your time and date settings and try again.`

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
