# Tools

## Chrome Devtools

* `$$(".class")` za dohvaćanje elemenata bez jquerija. Search bar u *Elements* također prima CSS selektore.
* `$0` u konzoli vraća trenutno selektirani element.
* `$_` rezultat zadnje linije.

* `console.log()` za obične; `console.warn()` i `console.err()` za obojane poruke.
* `console.group()` i `console.groupEnd()` za grupiranje poruka.
* `console.log("Name: %s ", name)` za interpolaciju
* `console.time('fetching data')` i `console.timeEnd('fetching data')` za mjerenje trajanja.
* `copy("...")` u konzoli dodaje argument u clipboard.

* `getEventListeners(el)` vraća array svih bindanih evenata na elementu.
* `monitorEvents(el)` u konzolu ispusuje sve evente koji se dogode elementu. `monitorEvents(el, ['click'])` za samo određene evente.

* `document.designMode = 'on'` čini sav tekst na siteu editabilnim.
* klik na device frame pa capture screenshot.
* *Network* tab, *Filmstrip* mode za prikaz kako korisnik vidi loadanje.
* *Sources* tab, uključi *Asnyc* kako bi stack traceovi bili potpuni čak i za callbackove. Ovo troši dosta memorije, pa ga isključi kad ti ne treba.

## Google HTML/CSS Styleguide

https://google.github.io/styleguide/htmlcssguide.xml

* Koristi relativni protokol (`//www.google.com`) za slike, stylesheete i skripte. Tako izbjegavaš probleme s mixed contentom.
* Ne koristi entity reference (`&mdash;`) osim za `<`, `>` i `&`, te nevidljive znakove.
* Ne treba ti `type` atribut za skripte i stylesheete.
