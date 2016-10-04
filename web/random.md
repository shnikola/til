# Random

## Chrome Devtools
* `$$(".class")` za dohvaćanje elemenata bez jquerija. Search bar pod *Elements* također prima CSS selektore.
* `$0` u konzoli vraća trenutno selektirani element.
* `$_` rezultat zadnje linije.

* `getEventListeners(el)` vraća array svih bindanih evenata na elementu.
* `monitorEvents(el)` u konzolu ispusuje sve evente koji se dogode elementu. `monitorEvents(el, ['click'])` za samo određene evente.

* `document.designMode = 'on'` čini sav tekst na siteu editabilnim.
* klik na device frame pa capture screenshot.
* *Network* tab, *Filmstrip* mode za prikaz kako korisnik vidi loadanje.


## Google HTML/CSS Styleguide
https://google.github.io/styleguide/htmlcssguide.xml
* Koristi relativni protokol (`//www.google.com`) za slike, stylesheete i skripte. Tako izbjegavaš probleme s mixed contentom.
* Ne koristi entity reference (`&mdash;`) osim za `<`, `>` i `&`, te nevidljive znakove.
* Ne treba ti `type` atribut za skripte i stylesheete.


## data-uri
URI koji sadrži inline data. Koriste se kako bi se uštedio request.
Formata je `data:[<media type>][;base64],<data>`. (npr. `data:image/png;base64,iVBORw0KGgoAA...`)


## Hoće li se poslati Referrer?
Browser gleda ovim redom:
1. `Referrer-Policy` http header
2. `<meta name="referrer" content="unsafe-url">` u headu
3. `referrerpolicy` html atribut
4. `noreferrer` html atribut
5. nasljeđivanje od parent contexta


## Continuous Integration
https://circleci.com/
Kad pushash kod, on automatski povuče to na svoj server, i pokrene sve testove.
Čak možeš podesiti da ga deploya na Heroku ako su svi testovi prošli. Jer si lijena guzica.


## Offline (TODO)
Za URL addressable resurse, koristi `Cache API` (dio service workera)
Za sve ostalo, koristi `IndexedDB` (s Promises wrapperom)

Detekcija:
`navigator.onLine`
window.applicationCache.addEventListener("error")

http://www.html5rocks.com/en/mobile/workingoffthegrid/
https://medium.com/dev-channel/offline-storage-for-progressive-web-apps-70d52695513c#.hkvzsc54w
