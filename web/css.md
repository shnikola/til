# CSS

## Pseudo-classes
https://www.smashingmagazine.com/2016/05/an-ultimate-guide-to-css-pseudo-classes-and-pseudo-elements/
Fantomsko stanje elementa koje se može targetirati CSSom.

**State:**
* `:link` - neposjećeni link
* `:visited` - posjećeni link
* `:hover` - pointer je iznad elementa
* `:active` - element je kliknut, tapnut ili keyboardan, ali klik još nije pušten
* `:focus` - element je dobio focus klikom, tapom ili keyboardom

**Structure:** _IE 9+_
* `:first-child` - element koji je prvo dijete parenta _IE 7+_
* `:last-child` - element koji je zadnje dijete parenta
* `:nth-child()` - prima broj (5), parnost (even) ili formulu (2n+6).
* `:nth-last-child()` - kao nth-child, samo počinje od kraja.
* `:only-child` - element koji je jedino dijete parenta.
* `:first-of-type` - svi prvi elementi svog tipa u parentu (prvi `li`, prvi `span` itd.)
* `:last-of-type` - zadnji elementi svog tipa
* `:nth-of-type()` - kao nth-child, samo uzima u obzir elemente istog tipa
* `:nth-last-of-type()` - kao nth-of-type, samo od kraja.
* `:only-of-type` - jedini elementi svog tipa u parentu.

**Inputs:** _IE 9+_
* `:disabled`, `:enabled` - inputi s i bez `disabled` atributa
* `:required`, `:optional` - inputi s i bez `required` atributa
* `:read-only`, `read-write` - inputi s i bez `readonly` atributa. Uključuje i `contenteditable` elemente.
* `:valid`, `:invalid` - inputi s valueom ispravnog i neispravnog formata (npr. `type=email`)
* `:in-range`, `:out-of-range` - inputi s valueom unutar i izvan zadanog rangea (min i max atributi)
* `:checked` - radio buttoni, checkboxi ili select optioni koji su checked ili selected
* `:indeterminate` - radio buttoni od kojih nijedan nije odabran, ili checkbox kojem je dano indeterminate stanje iz js

* `:empty` - elementi koji u sebi nikakvog teksta (čak ni whitespace) niti druge elemente

**Misc:**
* `:target`- element čiji je id trenutno u anchoru urla. (`www.example.com/articles#section-1`)
* `:not()` - prima drugi selector - klasu `:not(.blue)` ili pseudoklasu `:not(:last-of-type)`
* `:lang()` - element s definiranim `lang` atributom
* `:root` - najviši parent element. Za HTML je `html`, ali za SVG ili XML može biti drugo.
* `:fullscreen` - element prikazan u fullscreenu (Fullscreen API). _IE 11+, prefixi_


## Pseudo-elements
Virtualni elementi koji ne postoje u DOMu, ali mogu se stvoriti kroz CSS.
Imaju dvostruki `::` da se razlikuju od pseudoklasa, ali _IE 8_ podržava samo `:`

* `::before`, `::after` - dodaje virtualno dijete **unutar** container elementa (ne radi na inputima, imageima i sl.) Dijete se dodaje **inline** po defaultu. Zahtjeva `content` property koji se stavlja kao njegov tekst. Razmaci se uzimaju u obzir. Tekst pseudo elementa se ne može selektirati.
* `::first-letter` - prvo slovo teksta u elementu. Uključuje i `::before`.
* `::first-line` - prva linija teksta. Samo za block elemente.
* `::selection` - selektirani tekst u elementu. _IE 9+, -moz prefix_

* `::backdrop` - u fullscreen modu, box koji se renderira direktno ispod elementa. _IE 11+, prefixi_
* `::placeholder` - placeholder input elementa. _nije u standardu, prefixi posvuda_
* `::marker` - oznaka list itema (bullet, redni broj). _nije u standardu još_
* `::spelling-error`, `::grammar-error` - dio teksta koji je browser podcrtao kao neispravan _nije u standardu još_


## Functions
* `url()` - prima pokazivač na resource. Može biti apsolutna, relativna (u odnosu na css file) adresa, ili data-uri.
* `attr()` - vraća vrijednost atributa na elementu (npr. `attr(data-count)`). Zasad ga podržava samo `content`. _IE 8+_
* `calc()` - računanje, ali s miješanim unitima (npr. `100% - 3em`) _IE 9+_


## calc()
`calc()` je kul zato jer se računa prilikom rendera i dopušta miješanje postotaka i drugih unita.
Uvijek koristi prvo fallback (`height: 80%`) a onda funkciju (`height: calc(...)`).
* `width: calc(100% - 50px)`: ako imaš sidebar of 50px a želiš zauzeti ostatak.
* `background-position: calc(100% - 50px)`: pozicionira background od donjeg desnog kuta.
* `width: calc(60% - 1em)` i `width: 40%`: za dva stupca varijabilne širine s fiksnim razmakom između.


## Counters
`counter-reset: articles 0` resetira counter za taj element i svu djecu
`counter-increment: articles 1` povećava counter svaki put kad se susretne s ovim pravilom.
`content: "Article " counter(articles, decimal)` za korištenje countera u `content`.
Pripazi da ne koristiš countere za bitan sadržaj - screen readeri ne čitaju generirani `content`. _IE 8+_


## Cursor
`cursor:` osim na standardni način, može biti zadan s `url(...), pointer` za custom grafiku (i fallback).
Moguće je selektirati vrstu uređaja po pointeru: _Chrome, Edge_
  * `@media (pointer: coarse)` neprecizno upravljanje (npr. touchscreen ili Kinect).
  * `@media (pointer: fine)` precizni upravljanje (miš, touchpad, tablet stilus)


## Tables
`table-layout` kako se određuje širina stupaca: `auto` prema svim poljima ili `fixed` prema širini prvog retka.
border-collapse
border-spacing
caption-side
empty-cells
vertical-align


## Feature queries
`@support (display: grid) { ... }` primjenit će block samo ako browser podržava property u zagradama. _Chrome, FF, Edge_
* ovo *ne služi* da bi ispitao da li je `border-radius` podržan prije nego ga probaš primijeniti - browser će ionako preskočiti property koji ne podržava.
* ovo služi da grupiraš propertije koje želiš da se primjene ako je `border-radius` podržan, npr. želiš `border-width: 3px` samo ako će biti i `border-radius`.
* pravila se mogu kombinirati s `or`, `and` i `not`, npr. `(border-radius: 1px and display:grid)`
* zbog starih browsera strukturiraj css da ide *prvo fallback code*, *onda support block*.   


## Units
`2 px` - 2 točke na ekranu.
`2 em` - 2 puta veće od `font-size` na tom elementu.
`2 rem` - 2 puta veće od `font-size` na root (`<html>`) elementu.
`2 vh` - 2% visine viewporta (prozora). _IE 9+_
`2 vw` - 2% širine viewporta. _IE 9+_

* `font-size: 12vw` za tekst koji će uvijek zauzimati istu širinu. Točnu vrijednost odrediš isprobavanjem.
* `height: 100vh; width: 100vw` za hero image koji zauzima cijeli ekran.
* `margin: 20vh 20vw; width: 60vw; height: 60vh;` za apsolutno centrirani div.


## Colors
`rgba(255, 125, 12, 0.2)` (ili `#ff880f`) - `rgb` u rasponu `0..255`, i alpha kanal između `0.0` i `1.0`.
`hsla(150, 50%, 50%, 0.5)` - `hue` je kut od `0` do `360` (odabire boju), `saturation` (zasićenost) i `lightness` (svjetlina) su postotak. Ovaj format je malo lakši za vizualizirati na prvi pogled. _IE 9+_

`currentColor` jednak je `color` propertiju elementa, korisno za npr. `border: 1px solid currentColor` _IE 9+_


## Fonts
`font-family:` lista fontova, odvojena zarezima. Imena s razmacima stavi u navodnike.
  * Odabire se prvi font koji je dostupan. Međutim, ako se neki glyph ne nalazi u odabranom fontu, tražit će se u ostalima.  
  * Generične obitelji su zadnji fallback: `serif`, `sans-serif`, `monospace`, `cursive` i `fantasy`(!!!).

`font-weight`: 100, 200, 300, 400 (`normal`), 500, 600, 700 (`bold`), 800, 900. Fallback do 400 ide na tanje, od 500 na deblje.
`font-style`: `normal`, `italic` ili `oblique` (ali njega nitko ne koristi)
`font-stretch`: `condensed`, `normal`, `expanded`. _IE9+_

Ukoliko zadani `font-syle` ili `font-weight` nisu pronađeni u odabranom fontu, browser će ih pokušati *simulirati*, što često ne izgleda baš najbolje. Postoji `font-synthesis` za disablanje toga ali je podržan samo u _FF_. Do tada, pazi da fontovi koje loadaš imaju stilove i weightove koje koristiš.

`font-size`: visina slova. Koristi relativne vrijednosti `em` i `rem`, eventualno `px`.
`line-height`: visina linije u odnosu na `font-size`. Najsigurnije koristiti unitless mjere, npr. `1.2`.

`@font-face` definira novi font s atributima:
  * `font-family` - ime novog fonta
  * `src` - lista urlova odakle se može skinuti
  * `font-weight`, `font-style` itd. definiraju tip fonta. Defaultno je sve `normal`.

Browser neće prikazati tekst dok se font s `@font-face` ne loada, pa se pri otvaranju stranice događa *Flash of Invisible Text*.
Ako želiš to izbjeći, koristi library kao `Font Face Observer`, ili `font-display: swap` unutar `@font-face`. _Chrome_

Ako želiš detaljno definirati kako se određeni glyphovi fonta ponašaju, postoje `font-variant-caps`, `font-variant-ligatures`,
`font-variant-numeric` (za brojeve), `font-variant-position` (za `<sub>` i `<sup>`). _FF_


## Text
`text-align`
`text-decoration`
`text-indent`
`text-shadow`
hyphens
letter-spacing
line-break
tab-size
text-align-last
text-size-adjust
text-transform
white-space
word-break
word-spacing
word-wrap


## Background
`background-color` boja pozadine, defaultno `transparent`.
`background-image` jedna ili više slika, odvojene zarezima. Poziciju svake podesi s `background-position: 20px 0, 10px 0`

`background-position` pozicija slike, izražena s `left/right/center`, u `px`, ili `%`.
`background-clip` u kojem dijelu elementa se pozadina iscrtava. `background-origin` u kojem dijelu počinje. _IE 9+_
  * `content-box` prikazuje se ispod contenta, do paddinga
  * `padding-box` prikazuje se ispod paddinga, do bordera
  * `border-box` prikazuje se i ispod bordera

`background-size` _IE 9+_
  * `100px 50px` za fiksnu veličinu
  * `50%` skalira u odnosu na element
  * `contain` raširi do rubova elementa održavajući aspect ratio. Rupe popuni s `background-color`
  * `cover` raširi do rubova elementa održavajući aspect ratio. Viškove odreže.

`background-repeat` kako se ponavlja:
  * `no-repeat` iscrta samo jednom
  * `repeat` ponavlja dok ne prekrije cijelu površinu, zadnji image se clipa ako ne stane.
  * `repeat-x`, `repeat-y` ponavlja u samo jednom smjeru.
  * `space` jednoliko ponavlja unutar elementa, bez clipanja, ostavljajući prostor između. _IE 9+_

`background-attachment` kako se ponaša pri scrollanju:
  * `scroll` fiksirana s elementom, ne scrolla (default)
  * `fixed` fiksirano u odnosu na viewport, scrolla kad i prozor. Korisno za parallax scrolling
  * `local` scrolla s elementom. (Trenutno ne radi na retina chromeu). _IE 9+_

`background-blend-mode` kako se blendaju background images i background color. _Chrome, FF_
  * `multiply`, `screen`, `overlay` i drugi photoshop modovi.


## Filter
Dodavanje efekata na slike i backgrounde, npr. `filter: blur(5px)` _Chrome, FF, Safari_
* `grayscale(30%)` (crno-bijelo), `sepia(30%)` (žućkasto), `saturate(120%)` (pojačava boje)
* `hue-rotate(90)` (rotira boje), `invert(50%)` (obrne boje)
* `brightness(120%)` (posvjetljenje), `contrast(30%)` (razlika između tamnog i svijetlog)
* `blur(5px)` (zamućenje)
* `opacity(30%)` (prozirnost)
* `drop-shadow(5px 5px 0)` (kao `box-shadow`, ali prati outline)

Filteri se mogu i kombinirati (`filter: blur(2px) sepia(10%)`) i animirati.


TODO:
@media screen only
transform
svg
cubic-bezier()
global values: inherit initial unset
