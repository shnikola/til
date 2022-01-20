# Lisp

Lisp ima legendarnu reputaciju među programskim jezicima. Jedan je od najstarijih high level jezika i zaslužan je za nevjerojatno mnogo ideja koje danas uzimamo zdravo za gotovo:
* funkcija kao `first-class object`,
* rekurzija,
* `if/else` conditionali umjesto `goto`,
* svaka linija koda je expression, nema statementa,
* garbage collection,
* REPL - ne postoje read-time, compile-time i runtime

Ključna karakteristika Lispa je njegova elegancija. Sintaksna pravila su minimalna, gotovo nepostojeća. Sve je **lista**: podatci `'(1 2 3)`, pozivi funkcija `(+ 1 3 5)`, conditioni `(if (evenp x) :even :odd)`, itd. Nema keyworda, blokova niti flow controla. Čitav LISP interpreter stane na jednu A4 stranicu. Zbog toga je izrazito fleksibilan, pa se naziva i programabilnim programskim jezikom.

Ova ideja da su kod i podatci semantički jednaki omogućuje mnoga napredna rješenja koje mnogi drugi jezici ne podržavaju, poput proslijeđivanja funkcija s lazy evaluacijom. S druge strane, na prvi pogled je nemoguće reći da li određena linija predstavlja podatke ili naredbu. Da ne spominjem legendarni problem beskrajnih zatvorenih zagrada (npr. `(/= 15 (+ 4 10)))`)

**CommonLisp** dijalekt je baš kriptično-arhaičan. True i false su `t` i `nil`. Postoji deset različitih `eq` i `eql` i `equal`-ova. Imena funkcija su potpuno nerazumljiva: `car`, `cdr`, `setq`... Zapisivanje vrijednosti u hash je `(setf (gethash 1 users) "Nikola")`.

**Racket** je moderniji dijalekt s velikim standard librarijem i konzistentnijom sintaksom, ali i dalje se teško izboriti sa svim tim zagradama.

Ali cijenim postojanje Lispa kao alternativni način razmišljanja o computationu. Većina imperativnih jezika su apstrakcije strojnog jezika i upravljanja memorijom, dok Lisp stavlja matematičku suštinu računarstva u prvi plan, ignorirajući implementaciju. To često nije pristup prilagođen rješavanju svakodnevnih zadataka, ali ponekad zna iznenaditi.
