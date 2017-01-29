# Promises

Ako želimo pozvati funkciju kad se `img` loada, možemo koristiti `img.addEventListener('load', onload)`. Problem s ovim pristupom je što se u trenutku kad dodajemo listener, `img` možda već loadao, pa se event nikad neće trigerirati.

`addEventListener` je super za eventove koji se događaju više puta, npr. `keyup` i `click`. Ali želimo li asinkrono doznati o uspjehu ili neuspjehu neke radnje, koristimo *promise*: `promise.then(successCallback, failCallback)`.

Promise može uspjeti ili failati samo jednom. Ako mu dodamo callback nakon što je uspio ili failao, ispravni callback će biti pozvan. To je vrlo korisno u slučajevima kad nam nije bitan trenutak kad se nešto dogodilo, nego samo rezultat.

Promise može biti:
* `fulfilled` kada je akcija uspjela.
* `rejected` kada akcija nije uspjela.
* `pending` kada akcija još nije dovršena.
* `settled` kada je akcija dovršena, uspješno ili neuspješno.

Promise objekt stvaraš pomoću `new Promise(function(resolve, reject) { ... })`. Unutar funkcije obavljaš asinkroni posao, te pozivaš `resolve("Success")` po uspjehu ili `reject(Error("Failed"))` po neuspjehu. Promise će biti rejectan i ako se dogodi exception unutar promise konstruktora.

Osnovni način zvanja je `promise.then(success, fail)`, pri čemu će biti pozvana ili `success` ili `fail` funkcija, nikad oboje.

Ljepši način pisanja je `promise.then(success).catch(fail)`. On ima malo drugačije ponašanje jer handla i slučaj da promise prođe, `success` se izvodi i ne uspije, što će onda `catch` uloviti.

Funkcija `then` vraća novi promise pa se može chainati za sekvencijaranje asinkronih poziva koji ovise jedan o drugome:
`fetch('story.json').then(JSON.parse).catch(errorHappened).then(finished)`. Ako koji korak faila, pozvat će se `errorHappened`, a u svakom slučaju će se pozvati `finished`.


## jQuery

jQuery promisei su suptilno drugačiji, ali možeš ih castati u nativni promise koristeći `Promise.resolve($.ajax('/whatever.json'))`.


## Async Functions _Chrome, Edge (flag)_

Alternativan način pisanja promisea je `async func() { ... }`. Funkcija definirana s `async` omogućava da koristiš `await promise` unutar nje, npr. `await fetch(url)`.

`await` će pauzirati funkciju dok ne dobije odgovor, bez da blokira glavni thread. Asinkrone naredbe tako možeš pisati sekvencijalno, a ne se gnjaviti s promisima.


# Literatura

* https://developers.google.com/web/fundamentals/getting-started/primers/promises
* https://developers.google.com/web/fundamentals/getting-started/primers/async-functions
