# CSS

## Hierarchy selectors
`.a .b` selektira elemente `.b` koji su bilo gdje među djecom elementa `.a`.
`.a > .b` samo direktna djeca.
`.a ~ .b` sibling, dijele istog roditelja.
`.a + .b` adjacent sibling, ide neposredno iza `.a`.


## Attribute selectors _IE 7+_
* `[href]` elementi koji imaju atribut (pa makar i prazan)
* `[href="#"]` s danom vrijednosti atributa. Navodnici nisu uvijek nužni, ali najbolje ih je stavljati na sve.
* `[href^=]` za prefiks, `[href$=]` za sufiks.
* `[href*=]` za match u bilo kojem dijelu vrijednosti.
* `[lang|="en-us"]` vrijednost ima više riječi odvojenih razmacima, `en-us` je jedna od njih.


## Pseudo-classes
Specijalna stanja elemenata koja se mogu targetirati selektorom.

**State:**
* `:link` - neposjećeni link
* `:visited` - posjećeni link
* `:hover` - pointer je iznad elementa
* `:active` - element je kliknut, tapnut ili keyboardan, ali klik još nije pušten
* `:focus` - element je dobio focus klikom, tapom ili keyboardom

**Structure:** _IE 9+_
* `:first-child` element koji je prvo dijete parenta, `:last-child` koji je zadnje.
* `:only-child` element koji je jedino dijete parenta.
* `:nth-child()` prima broj (5), parnost (even) ili formulu (2n+6). `:nth-last-child()` isto, samo počinje od kraja.
* `:first-of-type` svi prvi elementi svog tipa u parentu (prvi `li`, prvi `span` itd.) `:last-of-type` svi zadnji.
* `:only-of-type` jedini elementi svog tipa u parentu.
* `:nth-of-type()` - kao nth-child, samo uzima u obzir elemente istog tipa. `:nth-last-of-type()` isto, samo od kraja.

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

* `::before`, `::after` dodaje virtualno dijete **unutar** elementa.
  * element mora biti container tipa, ne radi na inputima, imageima i sl.
  * zahtjeva `content` property koji se stavlja kao njegov tekst. Tekst se ne može zasebno selektirati CSS-om.
  * virtualni element je po defaultu **inline**.

* `::first-letter` - prvo slovo teksta u elementu. Uključuje i `::before`. S `initial-letter` (_Safari_) definiraj koliko linija zauzima.
* `::first-line` - prva linija teksta. Samo za block elemente.
* `::selection` - selektirani tekst u elementu. _IE 9+, -moz prefix_

* `::backdrop` - u fullscreen modu, box koji se renderira direktno ispod elementa. _IE 11+, prefixi_
* `::placeholder` - placeholder input elementa. _nije u standardu, prefixi posvuda_
* `::marker` - oznaka list itema (bullet, redni broj). _nije u standardu još_


## Universal values
* `initial` postavlja na defaultnu vrijednost po CSS standardu.
* `inherit` nasljeđuje vrijednost od parenta. Nekad se to radi automatski (npr. `color` ili `font-family`).
* `unset` ignorira sva CSS pravila, koristi `inherit` ako se inače inherita (npr. `color`), ili `inital` ako ne. _Chrome, FF, Edge_


## attr() _IE 8+_
`attr(data-name)` vraća vrijednost atributa na elementu. Zasad ga podržava samo `content`.


## calc() _IE 9+_
`calc()` je kul zato jer se računa prilikom rendera i dopušta miješanje unita (npr. `100% - 3em`)
* `width: calc(100% - 50px)`: ako imaš sidebar of 50px a želiš zauzeti ostatak.
* `background-position: calc(100% - 50px)`: pozicionira background od donjeg desnog kuta.
* `width: calc(60% - 1em)` i `width: 40%`: za dva stupca varijabilne širine s fiksnim razmakom između.

Uvijek koristi prvo fallback (`height: 80%`) a onda funkciju (`height: calc(...)`).


## Counters
`counter-reset: articles 0` resetira counter za taj element i svu djecu
`counter-increment: articles 1` povećava counter svaki put kad se susretne s ovim pravilom.
`content: "Article " counter(articles, decimal)` za korištenje countera u `content`.
Pripazi da ne koristiš countere za bitan sadržaj - screen readeri ne čitaju generirani `content`. _IE 8+_


## Cursor
* `cursor` tip cursora iznad elementa: `default`, `pointer`, `wait`, `zoom-in`, `grab`...
* `cursor: url(cursor.png) pointer` custom image s fallbackom
* `pointer-events` kako element reagira na mouse event. `auto` radi normalno, `none` propušta click elementu ispod. _IE 11+_
* `touch-action` kako element reagira na touch. `auto` dopušta sve, `none` disabla sve, `pan-x` i `pan-y` dopušta samo scroll. _IE 10+_

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
`font-size`: visina slova. Koristi relativne vrijednosti `em` i `rem`, eventualno `px`.
`line-height`: visina linije u odnosu na `font-size`. Najsigurnije koristiti unitless mjere, npr. `1.2`.

`font-family`: lista fontova, odvojena zarezima. Imena s razmacima stavi u navodnike.
  * Odabire se prvi font koji je dostupan. Međutim, ako se neki glyph ne nalazi u odabranom fontu, tražit će se u ostalima.  
  * Generične obitelji su zadnji fallback: `serif`, `sans-serif`, `monospace`, `cursive` i `fantasy`(!).

`font-weight`: 100, 200, 300, 400 (`normal`), 500, 600, 700 (`bold`), 800, 900. Fallback do 400 ide na tanje, od 500 na deblje.
`font-style`: `normal`, `italic` ili `oblique` (ali njega nitko ne koristi)
`font-stretch`: `condensed`, `normal`, `expanded`. _IE9+_

Ukoliko zadani `font-syle` ili `font-weight` nisu pronađeni u odabranom fontu, browser će ih pokušati *simulirati*, što često ne izgleda baš najbolje. Postoji `font-synthesis` za disablanje toga ali je podržan samo u _FF_. Do tada, pazi da fontovi koje loadaš imaju stilove i weightove koje koristiš.

`@font-face` definira novi font s atributima:
  * `font-family` ime novog fonta
  * `src` lista urlova odakle se može skinuti: `url('/fonts/awesome.woff') format('woff'), ...`.
  * `font-weight`, `font-style` itd. definiraju tip fonta. Defaultno je sve `normal`.
  * `font-display` definira kako će se ponašati prije nego se loada.

Ako želiš detaljno definirati kako se određeni glyphovi fonta ponašaju, postoje `font-kerning` (za disablanje pametnog kerninga), `font-variant-ligatures` (za disablanje ligatura), `font-variant-caps` (za small-caps i slične), `font-variant-numeric` (za stilove znamenki), `font-variant-alternates` (za swasheve, ornamente i sl.) _FF, Safari, font-feature-settings za starije browsere_


## Text (TODO)
`text-align`
`text-decoration`
`text-indent`
`text-shadow`
hyphens
line-break
tab-size
text-align-last
text-size-adjust
text-transform
white-space
word-break
word-wrap

Za finije upravljanje razmacima između slova i riječi: `letter-spacing`, `word-spacing`.


## vertical-align
`vertical-align` radi samo na `inline` i `inline-block` elementima, te definira kako će se poravnati u odnosu na parenta.
Neke od vrijednosti su:
* `baseline` poravnava u s baselineom parenta.
* `top`, `bottom` poravnava s vrhom/dnom cijele linije.
* `text-top`, `text-bottom` poravnava s vrhom/dnom parentovog fonta.
* `middle` poravnava sredinu elementa sa sredinom parentove linije.

Za vertikalno centrirati `block` element, najjednostavnije je: `position: absolute; top: 50%; transform: translateY(-50%)`.


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


## Padding and Margin
Vrijednosti u postotcima za padding i margin (npr. `padding-top: 10%`) se uvijek računaju u odnosu na *width* elementa, čak i za vertikalne `-top` i `-bottom` propertije.


## Flexbox _IE 11+_
Flexbox omogućava lakše raspoređivanje elemenata unutar parenta, bez `floata` ili apsolutnog pozicioniranja.
Parent element koji ima `display: flex` je *flex container*, a njegova djeca (uključujući i tekst direktno u njemu) su *flex itemi*.

Properties *containera*:
`flex-direction` definira *main axis* po kojem se itemi slažu:
  * `row` i `row-reverse` za horizontalno.
  * `column` i `column-reverse` za vertikalno.

`justify-content` definira kako se itemi raspoređuju po *main axis*:
  * `flex-start` od početka
  * `flex-end` od kraja
  * `center` od sredine
  * `space-between` poravnato s rubovima, s jednakim razmacima između
  * `space-around` sa svih strana jednakih razmaka

`align-items` definira kako se itemi raspoređuju po *cross axis*:
  * `flex-start` poravnati s gornjim rubom
  * `flex-end` poravnati s donjim rubom
  * `center` poravnati po sredini
  * `baseline` poravnati da im se baselinei poklapaju
  * `stretch` rastegnuti da zauzimaju cijelu visinu containera

`flex-wrap` hoće li se itemi wrapati:
  * `nowrap` stisne sve iteme da stanu u jednu liniju. Default.
  * `wrap` wrapa iteme u dodatne linije.
  * `wrap-reverse` wrapa iteme u dodatne linije, ali počinje od dna.

`align-content` kako se wrapane linije raspoređuju (ima iste vrijednosti kao `justify-content`)
`flex-flow: row wrap` je shorthand za `flex-direction` i `flex-wrap`.

Properties *itema*:
`align-self` definira poseban alignment samo za item, npr. `align-self: flex-end` kad je `align-items: flex-start`.
`order` definira drugačiji redoslijed itema. Defaultno svi imaju `order: 0`.

Veličina itema definirana je negovim contentom. Ostatak slobodnog prostora raspoređuje se pomoću `flex-grow` propertija:
* svi itemi defaultno imaju `flex-grow: 0` pa će slobodan prostor ostati neispunjen.
* ako samo jedan item ima `flex-grow: 1`, zauzet će sav slobodan prostor.
* ako više itema ima definiran `flex-grow`, podijelit će ga proporcionalno tome (`flex-grow: 2` dobija duplo više od `1`)

Ako želiš definirati fiksnu veličinu itema, koristi `flex-basis`:
* definira `width` ako je direction `row`, `height` ako je `column`.
* prima iste vrijednosti kao `width`: `px`, `em`, ili `%` (u odnosu na flex container).
* ako ukupna širina svih itema premašuje container, `flex-shrink` definira za koliki se faktor item može smanjiti.

`flex: 1 6 20%` je shorthand za `flex-grow`, `flex-shrink` i `flex-basis`.
  * `flex: 1 0 0` item zauzima sav slobodan prostor, neovisno od svoje početne veličine.
  * `flex: 0 0 auto` item zadržava svoju početnu veličinu.


## Filter _Chrome, FF, Safari_
Dodavanje efekata na slike i backgrounde, npr. `filter: blur(5px)`
Filteri se mogu i kombinirati (`filter: blur(2px) sepia(10%)`), a i animirati.
* `grayscale(30%)` (crno-bijelo), `sepia(30%)` (žućkasto), `saturate(120%)` (pojačava boje)
* `hue-rotate(90)` (rotira boje), `invert(50%)` (obrne boje)
* `brightness(120%)` (posvjetljenje), `contrast(30%)` (razlika između tamnog i svijetlog)
* `blur(5px)` (zamućenje)
* `opacity(30%)` (prozirnost)
* `drop-shadow(5px 5px 0)` (kao `box-shadow`, ali prati outline)


## Transform _IE 9+_
Transformiranje elemenata koje ne utječe na layout, npr: `transform: scale(1.5)`
Transformacije se mogu kombinirati: `transform: scale(1.5) rotate(20deg)`.

`transform-origin` definira ishodište elementa (os za rotaciju, fiskna točka za scale i skew). Defaultno je središte elementa.

2D transformacije:
* `rotate(20deg)`, defaultna os je centar elementa.
* `scale(0.75)` za jednoliko, `scale(1.2, 2.0)` za različito, ili `skewX(5deg)`, `skewY(10deg)`.
* `translate(1px, 2px)`, ili `translateX(-10px)`, `translateY(25%)`.
* `skew(20deg, 30deg)`, ili `skewX(5deg)`, `skewY(10deg)`.

Za 3D transformacije potrebno je definirati perspektivu.
* `perspective-origin(10px 30px)` definira vanishing point perspektive.
* `transform: perspective(20px) ...` definira udaljenost perspektive. Što je broj manji, bliže smo, pa je promjena veća.
* perspektiva definirana na parentu primjenjuje se na sve child elemente.

3D transformacije:
* `rotateX` (os rotacije je horizont), `rotateY` (os rotacije je y-os), `rotateZ` (rotira na ravnini ekrana).
* `scaleX`, `scaleY`, `scaleZ` (Z se primjeti tek ako je element rotiran)
* `translateX`, `translateY`, `translateZ`

Ako se element 3D transformira unutar parenta koji se isto transformira, po defaultu neće dobiti svoj 3D prostor već će ostati flat u parentu. Za omogućavanje nested 3D transformacije, dodaj `transform-style: preserve-3d` u parenta.

Ako 3D tranformacija okrene element da mu vidimo guzicu, defaultno će biti vidljiv. Za sakriti guzicu, koristi `backface-visibility: hidden`.


## Shapes _Chrome, Safari_
`shape-outside` definira oblik floatanog elementa oko kojeg će se wrapati text. Po defaultu je to kvadrat, ali ne mora biti.
* `circle()` ili `ellipse()`. `circle(100px at 30% 50%)` definira radius i centar.
* `polygon(10px 10px, 20px 20px, 30px 30px)` definira poligon pomoću vrhova.
* `url(image.png)` koristi se alpha kanal slike (wow!).


## Variables (Custom Properties) _Chrome, FF, Safari_
Korisno za reusanje vrijednosti. Za razliku od preprocessor varijabli, mogu se mijenjati u runtimeu.
* `:root { --main-color: red; }` postavljanje vrijednosti varijable.
* `p { color: var(--main-color); }` dohvaćanje vrijednosti varijable.


# Literatura
* http://flexboxfroggy.com/ za učenje flexboxa

# TODO:
multicolumn
css-grid (kaže Jen da treba 6 mjeseci da se nauči)
write-modes
@media (screen, screen only)
