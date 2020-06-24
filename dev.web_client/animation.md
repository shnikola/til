# Animation

## CSS Transitions _IE 10+_

CSS Transitions služe za animiranje prijelaza propertija iz jedne vrijednosti u drugo.
* `transition-property: height, width` lista propertija čija će se promjena animirati. Defaultno `all`. Ne mogu se svi propertiji animirati, samo oni kojima se može izračunati sredina između dvije vrijednosti (npr. pikseli ili boje).
* `transition-duration: 1s` duljina trajanja animacije.
* `transition-timing-function` speed curve animacije: `linear`, `ease-in`, `ease-out`, `ease-in-out`.
* `transition-delay: 1s` delay prije početka animacije.

Shorthand je `transition: background .2s linear`.

## CSS Animations _IE 10+_

CSS animacije su korisne za preciznije upravljanje animacijom prijelaza. Sastoje od dvije komponente: *keyframes* i *animation properties*.

`@keyframes bounce { ` definira ime animacije:
* `0% { transform: scale(0.1); opacity: 0; }` stil na početku animacije.
* `60% { transform: scale(1.2); opacity: 1; }` stil na 60% animacije.
* `100% { transform: scale(1); }` stil na kraju animacije. Browser će

Propertiji na elementu:
* `animation-name: bounce` elementu pridružuje definiranu animaciju.
* `animation-duration: 1s` koliko animacija traje.
* `animation-timing-function:` speed curve animacije: `ease`, `linear`, `ease-in`, `ease-out`, `ease-in-out`.
* `animation-delay: 1s` delay prije početka animacije. `-2s` započinje animaciju odmah od druge sekunde.
* `animation-iteration-count: 2` koliko puta će se animacija ponoviti. `infinite` za zauvijek. Može biti i decimalni broj, npr. `0.5`.
* `animation-direction: normal` smjer izvođenja animacije. `reverse` ide unazad. `alternate` ide do 100% pa unazad do 0%.
* `animation-fill-mode:` primjena propertija animacije.
    * `normal` propertiji se primjenjuju samo tijekom animacije.
    * `backwards` prije animacije, primjenjuju se propertiji iz 0% keyframea.
    * `forwards` nakon animacije, ostaju propertiji iz 100% keyframea.
    * `both` uključuje i `backwards` i `forwards`.
* `animation-play-state:` `playing` i `paused`, korisno za pauziranje animacije, npr. na `:hover`.

Shorthand je `animation: bounce 1s ease, rotate 1.75s`.
Dopušta više animacija odjednom.

Za custom timing animacija koje izgledaju prirodnije koristi *motion curves*. Za vrijednosti `transition-timing-function` i `animation-timing-function` postavi `cubic-bezier(n1, n2, n3, n4)`, gdje brojevi definiraju točke krivulje. Pronađi ih uz alate http://cubic-bezier.com i https://matthewlein.com/ceaser

## Animation Events

Nad elementom koji ima definira `transition` triggerirat će se eventovi `transitionstart` na početku `transitionend` na kraju transitiona. Za svaki property koji prolazi kroz transition dogodit će se zasebni event - o kojem propertiju se radi piše u `event.propertName`.

Za elemente s animacijama triggeriraju se `animationstart` i `animationend`, te `animationiteration` na kraju svakog ponavljanja animacije. Isto kao i za tranisitione, za svaku animaciju trigerira se zasebni event, a o kojoj se radi piše u `event.animationName`.

## Animation performance

Da bi animacija tekla glatko na 60Hz monitoru, mora se moći renderirati za manje od `16ms`. To vrijeme se troši na *recalculate styles*, *layout*, *paint*, i *composite* layera.

Samo 4 propertija garantiraju glatku animaciju: `opacity`, `translate`, `rotate` i `scale`. Svi ostali zahtjevaju više koraka iscrtavanja, prema popisu na https://csstriggers.com/.

Da bi ubrzao izračunavanje layouta, izbjegavaj animirati `height`, `width` ili `left` i `top`. Animiraj elemente koji su `absolute` i `fixed` koji ne utječu na layout. Za pomicanje koristi `transform: translateX()`. Za skrivanje koristi `opacity: 0` i `pointer-events: none`.

U slučaju da se layout ne mijenja, browser će odvojiti animirani dio u zasebni layer, i uzimati screenshote dok repozicionira jedan iznad drugog (*compositing*). GPU je jako dobar u tome, pa će se sve brže računati. Usto, `transform` i `opacity` se mogu direktno proslijediti u GPU.

Međutim, browser ponekad ne zna da treba stvoriti novi layer dok ne bude prekasno i animacija treba početi, što rezultira trzanjem na početku animacije. U tom slučaju, pokušaj dodati `will-change: transform` property koji će ga unaprijed obavijestiti o propertijima koji će se mijenjati.

Također, izbjegavaj animirati previše stvari odjednom. 2 do 3 elementa se mogu istovremeno glatko animirati. Koristi `transition-delay` kako bi stvorio koreografiju.

Pravi snimke animacija i pregledavaj u slow motionu kako bi bolje primjetio zastajkivanja.

## Web Animations API _Chrome, FF_

Omogućuje nativno animiranje elemenata iz Javascripta. Korisno u slučaju da CSS animacije nisu dovoljne (npr. ako želiš dimanički mijenjati vrijednosti, upravljati playbackom, ili handlati animation evente).

Najveća prednost u odnosu na druge animation librarije je performance. Čak i ako poslušamo sve savjete za glatke animacije, Garbage Collector može u bilo koje trenutku zagušiti thread zbog čega će animacija opet biti trzava.
Web Animations API šalje naredbe u zaseban thread (*compositor*) zadužen za crtanje, kojeg koriste i CSS animacije. Zato će čak i uvjetima velikog opterećenja animacije i dalje biti glatke.

Sintaksa je slična kao i CSS:
`elem.animate({ transform: [ 'rotate(0deg)', 'rotate(360deg)' ] },
              { duration: 1000, iterations: Infinity });`

`animation.play()`, `pause()` i `finish()` za upravljanje playbackom.
`animation.playbackRate` za brzinu izvođenja (i smjer, za negativne vrijednosti)

## Alati

* CSS za manje animacije i jednostavne interakcije.
* GSAP (GreenSock) za sequencing i kompleksno kretanje (i cross browser compatibility)
* mo.js je neki cool library isto

Za shape morphing:
* `morphSVG` je plugin za GreenSock
* `snapSVG` ima `animate()` funkciju za animaciju pathova.
* `SVG Morpheus`

## Primjeri

* confetti: http://codepen.io/danwilson/pen/vKzbgd/
* cinema seat preview: http://tympanus.net/codrops/2016/01/12/cinema-seat-preview-experiment/

## Literatura

* http://jankfree.org/ - Animation performance in detail
* https://aerotwist.com/blog/flip-your-animations/
