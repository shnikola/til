# Javascript

## Array Functions

Callback uvijek dobija argumente redom: item, index, list.

`array.find(x => x > 0)` vraća prvi element koji ispunjava uvjet.
`array.findIndex(x => x > 0)` vraća index prvog elementa koji ispunjava uvjet.

`array.every(x => x > 0)` vraća `true` ako svi elementi koji ispunjavaju uvjet.
`array.some(x => x > 0)` vraća `true` ako postoje elementi koji ispunjavaju uvjet.

`array.filter(x => x > 0)` vraća subarray elemenata koji odgovaraju uvjetu.
`array.map(x => x.name)` vraća array s rezultatima operacije.
`array.sort((a, b) => a > b ? -1 : 1`) vraća sortirani array.
`array.reduce((sum, current) => sum + current, 0)` agregira array u prvu vrijednost.

`...array` je spread operator za rastavljanje arraya na argumente, kao ruby splash (`*`) operator, npr. `[...arr, 1, 2, 3]`.

## Date

`new Date()` vraća Date objekt s metodama za dohvaćanje dijelova datuma: `date.getFullYear`, `date.getMonth`, `date.getDate`, `date.getHours`, `date.getMinutes`, `date.getSeconds`.
`Date.now()` vraća timestamp u milisekundama. _IE 9+_

## Debounce i Throttle

Na eventove koji se triggeriraju mnogo puta u sekundi (npr. `scroll` ili `mousemove`) nikad nemoj attachati funkciju direktno na njih. Umjesto toga, koristi:
* `debounce(400)` dok god se event događa, funkcija neće biti izvršena, tek *nakon* `400ms` u kojima se event ne dogodi izvršit će funkciju. Korisno za prepoznati kad je korisnik prestao tipkati ili dovršio `resize`.
* `throttle(400)` doke god se event događa, izvršavat će funkciju *maksimalno jednom* u `400ms`. Korisni za praćenje kontinuiranih eventova, npr. infinite scrolling.
* `requestAnimationFrame` kao throttle, ali radi nativno i cilja na izvršavanje svakih `16ms` (za 60fps). Korisno za funkcije koje crtaju ili animiraju.

# Literatura

* https://github.com/airbnb/javascript style guide
* http://bonsaiden.github.io/JavaScript-Garden quirks
* https://medium.com/the-node-js-collection/modern-javascript-explained-for-dinosaurs-f695e9747b70
