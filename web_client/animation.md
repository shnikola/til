# Animation

## CSS Transitions _IE 10+_
CSS Transitions služe za animiranje prijelaza propertija iz jedne vrijednosti u drugo.
* `transition-property: height, width` lista propertija čija će se promjena animirati. Defaultno `all`. Ne mogu se svi propertiji animirati, samo oni kojima se može izračunati sredina između dvije vrijednosti (npr. pikseli ili boje).
* `transition-duration: 1s` duljina trajanja animacije.
* `transition-timing-function` speed curve animacije: `linear`, `ease-in`, `ease-out`, `ease-in-out`.
* `transition-delay: 1s` delay prije početka animacije.

* `transition: background .2s linear` je shorthand za gornje propertije.


## CSS Animations _IE 10+_
CSS animacije su korisne za preciznije upravljanje animacijom prijelaza. Sastoje od dvije komponente: *keyframes* i *animation properties*.

`@keyframes bounce { ` definira ime animacije.
  * `0% { transform: scale(0.1); opacity: 0; }` stil na početku animacije.
  * `60% { transform: scale(1.2); opacity: 1; }` stil na 60% animacije.
  * `100% { transform: scale(1); }` stil na kraju animacije. Browser će

Propertiji na elementu:
`animation-name: bounce` elementu pridružuje definiranu animaciju.
`animation-duration: 1s` koliko animacija traje.
`animation-timing-function:` speed curve animacije: `ease`, `linear`, `ease-in`, `ease-out`, `ease-in-out`.
`animation-delay: 1s` delay prije početka animacije. `-2s` započinje animaciju odmah od druge sekunde.
`animation-iteration-count: 2` koliko puta će se animacija ponoviti. `infinite` za zauvijek.
`animation-direction: normal` smjer izvođenja animacije. `reverse` ide unazad. `alternate` ide do 100% pa unazad do 0%.
`animation-fill-mode:` primjena stilova animacije.
  * `normal` stilovi se primjenjuju samo tijekom animacije.
  * `backwards` prije animacije, primjenjuju se stilovi iz 0% keyframea.
  * `forwards` nakon animacije, ostaju stilovi iz 100% keyframea.
  * `both` uključuje i `backwards` i `forwards`.
`animation-play-state:` `playing` i `paused`, korisno za pauziranje animacije, npr. na `:hover`.

`animation: bounce 1s ease, rotate 1.75s` je shorthand za sve gornje propertije. Dopušta više animacija odjednom.


## CSS Motion Curves
Za custom timing animacija koji izgleda prirodnije koristi:
`transition-timing-function: cubic-bezier(n1, n2, n3, n4)`. Brojevi definiraju točke krivulje. Pronađi ih uz alate
http://cubic-bezier.com i https://matthewlein.com/ceaser


## Animation performance
Da bi animacija tekla glatko na 60Hz monitoru, mora se moći renderirati za manje od `16ms`.
Rendering u browseru uključuje *računanje layouta* i *iscrtavanje*.
Da bi izbjegao izračunavanje layouta, animiraj elemente koji su `absolute` ili `fixed`, ili animiraj propertije koji ne utječu na layout, kao `transform` `opacity` i `color`.
U slučaju da se layout ne mijenja, browser će odvojiti animirani dio u zasebni layer, i uzimati screenshote dok repozicionira jedan iznad drugog (*compositing*). GPU je jako dobar u tome, pa će se sve brže računati. Usto, `transform` i `opacity` se mogu direktno proslijediti u GPU.
Browser nekad ne zna da treba stvori novi layer dok nije prekasno, a animaciji treba početi. Ako animacija trza na početku, pokušaj dodati `will-change` property koji će ga unaprijed obavijestiti.


## Web Animations API _Chrome, FF_
Omogućuje nativno animiranje elemenata iz Javascripta. Korisno u slučaju da CSS animacije nisu dovoljne (npr. ako želiš dimanički mijenjati vrijednosti, upravljati playbackom, ili handlati animation evente).

Najveća prednost u odnosu na druge animation librarije je performance. Čak i ako poslušamo sve savjete za glatke animacije, Garbage Collector može u bilo koje trenutku zagušiti thread zbog čega će animacija opet biti trzava.
Web Animations API šalje naredbe u zaseban thread (*compisitor*) zadužen za crtanje, kojeg koriste i CSS animacije. Zato će čak i uvjetima velikog opterećenja animacije i dalje biti glatke.

Sintaksa je slična kao i CSS:
`elem.animate({ transform: [ 'rotate(0deg)', 'rotate(360deg)' ] },
              { duration: 1000, iterations: Infinity });`

`animation.play()`, `pause()` i `finish()` za upravljanje playbackom.
`animation.playbackRate` za brzinu izvođenja (i smjer, za negativne vrijednosti)


## Primjeri
* confetti: http://codepen.io/danwilson/pen/vKzbgd/
