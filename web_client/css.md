# CSS

## Hierarchy selectors

`.a .b` selektira elemente `.b` koji su bilo gdje među djecom elementa `.a`.
`.a > .b` samo direktna djeca.
`.a ~ .b` sibling, dijele istog roditelja.
`.a + .b` adjacent sibling, ide neposredno iza `.a`.

## Units

* `2px` - 2 točke na ekranu.
* `2em` - 2 puta veće od `font-size` na tom elementu.
* `2rem` - 2 puta veće od `font-size` na root (`<html>`) elementu.
* `2vh` - 2% visine viewporta (prozora). _IE 9+_
* `2vw` - 2% širine viewporta. _IE 9+_
* `2ch` - 2 širine znaka `0`. _IE 9+_

Primjeri korištenja:
* `font-size: 12vw` za tekst koji će uvijek zauzimati istu širinu. Točnu vrijednost odrediš isprobavanjem.
* `height: 100vh; width: 100vw` za hero image koji zauzima cijeli ekran.
* `margin: 20vh 20vw; width: 60vw; height: 60vh;` za apsolutno centrirani div.

Izbjegavaj korištenje `em` i `rem`. Gotovo nikad nećeš htjeti da se promjenom fonta na jednom mjestu automatski promijene dimenzije elemenata po cijelom siteu. Redizajn je ipak malo kompleksniji od toga.

## Attribute selectors _IE 7+_

* `[href]` elementi koji imaju atribut (pa makar i prazan)
* `[href="#"]` s danom vrijednosti atributa. Navodnici nisu uvijek nužni, ali najbolje ih je stavljati na sve.
* `[href^=]` za prefiks, `[href$=]` za sufiks.
* `[href*=]` za match u bilo kojem dijelu vrijednosti.
* `[lang|="en-us"]` vrijednost ima više riječi odvojenih razmacima, `en-us` je jedna od njih.

## Pseudo-classes

Specijalna stanja elemenata koja se mogu targetirati selektorom.

### State

* `:link` - neposjećeni link
* `:visited` - posjećeni link
* `:hover` - pointer je iznad elementa
* `:active` - element je kliknut, tapnut ili keyboardan, ali klik još nije pušten
* `:focus` - element je dobio focus klikom, tapom ili keyboardom

### Structure _IE 9+_

* `:first-child` element koji je prvo dijete parenta, `:last-child` koji je zadnje.
* `:only-child` element koji je jedino dijete parenta.
* `:nth-child()` prima broj (5), parnost (even) ili formulu (2n+6). `:nth-last-child()` isto, samo počinje od kraja.
* `:first-of-type` svi prvi elementi svog tipa u parentu (prvi `li`, prvi `span` itd.) `:last-of-type` svi zadnji.
* `:only-of-type` jedini elementi svog tipa u parentu.
* `:nth-of-type()` - kao nth-child, samo uzima u obzir elemente istog tipa. `:nth-last-of-type()` isto, samo od kraja.

### Inputs _IE 9+_

* `:disabled`, `:enabled` - inputi s i bez `disabled` atributa
* `:required`, `:optional` - inputi s i bez `required` atributa
* `:read-only`, `read-write` - inputi s i bez `readonly` atributa. Uključuje i `contenteditable` elemente.
* `:valid`, `:invalid` - inputi s valueom ispravnog i neispravnog formata (npr. `type=email`)
* `:in-range`, `:out-of-range` - inputi s valueom unutar i izvan zadanog rangea (min i max atributi)
* `:checked` - radio buttoni, checkboxi ili select optioni koji su checked ili selected
* `:indeterminate` - radio buttoni od kojih nijedan nije odabran, ili checkbox kojem je dano indeterminate stanje iz js

* `:empty` - elementi koji u sebi nikakvog teksta (čak ni whitespace) niti druge elemente

### Misc

* `:target`- element čiji je id trenutno u anchoru urla. (`www.example.com/articles#section-1`)
* `:not()` - prima drugi selector - klasu `:not(.blue)` ili pseudoklasu `:not(:last-of-type)`
* `:lang()` - element s definiranim `lang` atributom
* `:root` - najviši parent element. Za HTML je `html`, ali za SVG ili XML može biti drugo.
* `:fullscreen` - element prikazan u fullscreenu (Fullscreen API). _IE 11+, prefixi_

## Pseudo-elements

Virtualni elementi koji ne postoje u DOMu, ali mogu se stvoriti kroz CSS.
Imaju dvostruki `::` da se razlikuju od pseudoklasa, ali _IE 8_ podržava samo `:`.

`::before`, `::after` dodaje virtualno dijete **unutar** elementa.
* element mora biti container, ne radi s `<input>`, `<img>` i sličnim elementima.
* zahtjeva `content` property koji se stavlja kao njegov tekst.
* virtualni element je po defaultu **inline**.

* `::first-letter` - prvo slovo teksta u elementu. Uključuje i `::before`. S `initial-letter` (_Safari_) definiraj koliko linija zauzima.
* `::first-line` - prva linija teksta. Samo za block elemente.
* `::selection` - selektirani tekst u elementu. _IE 9+, -moz prefix_

* `::backdrop` - u fullscreen modu, box koji se renderira direktno ispod elementa. _IE 11+, prefixi_
* `::placeholder` - placeholder input elementa. _nije u standardu, prefixi posvuda_
* `::marker` - oznaka list itema (bullet, redni broj). _nije u standardu još_

## Property inheritance

Svaki property može imati jednu od općih vrijednosti:
* `initial` postavlja na defaultnu vrijednost po CSS standardu.
* `inherit` nasljeđuje vrijednost od parenta. Nekad se to radi automatski (npr. `color` ili `font-family`).
* `unset` ignorira sva CSS pravila, koristi `inherit` ako se inače inherita (npr. `color`), ili `inital` ako ne. _Chrome, FF, Edge_

Automatski nasljeđeni propertiji (`color`, `font-family`, `line-height`) se ne propagiraju u `input` i `textarea` elemente. Da bi to popravio, na početak CSS-a stavi `* { color: inherit; font-family: inherit; line-height: inherit; }`.

`all: initial` postavlja sve propertije elementa (osim `direction`) na početne vrijednosti. Korisno ako želiš resetirati element.
`all: inherit` nasljeđuje sve propertije od parenta.

## Display property

* `display: none` uklanja element iz document flowa. Dokument se renderira kao da element ne postoji.
* `display: inline` element se nalazi unutar linije. `margin` i `padding` se mogu dodati, ali djelovat će samo horizontalno. Ignorira se `width` i `height`.
* `display: inline-block` isto kao `inline`, ali može se postaviti `width` i `height`.
* `display: block` po defaultu zauzima širinu cijelog svog parenta.
* `display: list-item` default za `<li>`, ponaša se kao `block`, ali dodaje i marker box koji se može stilizirati s `list-style`.
* `display: flow-root` kao `block`, ali stvara zasebni formatting context pa se za floatanu djecu ne treba koristiti clearfix. _Chrome, FF_

## Visibility

`visiblity: hidden` čini element i njegovu djecu nevidljivim, ali i dalje utječe na layout.

## Overflow

Definira kako će se prikazati sadržaj koji izlazi izvan granica elementa. Defaultno, tekst se wrapa i povećava visinu parent elementa pa on sam neće uzrokovati overflow.

* `overflow: visible` je defaultan, prikazuje sadržaj.
* `overflow: hidden` skriva sadržaj koji viri.
* `overflow: scroll` skriva sadržaj i prikazuje **oba** scrollbara.
* `overflow: auto` skriva sadržaj i prikazuje scrollbar gdje je potreban.

Postavljanje `overflow` propertija stvar anovi formatting context

## Box Model

`box-sizing` određuje kako se `width` i `height` koriste.

Kod `box-sizing: content-box`, `width` i `height` definiraju veličinu sadržaja elementa. `padding` i `border` se pribraraju za ukupnu veličinu elementa.

Kod `box-sizing: border-box`, `width` i `height` definiraju veličinu cijelog elementa (boxa). `padding` i `border` se oduzimaju od toga i ostatak je veličina sadržaja.

`height: auto` i `width: auto` prilagođavaju veličinu sadržaju.

`margin: 0 auto` horizontalno centrira element, ako je manji od parenta.

Vrijednosti u postotcima za `padding` i `margin` (npr. `padding-top: 10%`) se uvijek računaju u odnosu na **width** elementa, čak i za `-top` i `-bottom` propertije.

Margine se u nekim slučajevima spajaju u jednu (*margin collapse*):
* unutar praznog elementa, njegov `margin-top` i `margin-bottom`.
* kod susjednih sibling elemenata, `margin-bottom` i `margin-top`.
* kod parenta i prvog/zadnjeg djeteta, njihovi `margin-top`ovi, odnosno `margin-bottom`i ako nema paddinga, bordera ili inline contenta između. Tako margina djeteta može "procuriti" izvan parenta.

## Border i Box Shadow

`border: 1px solid red` shorthand definira:
* `border-width` debljina bordera.
* `border-style` defaultno `none`. Može biti: `solid`, `dotted`, `dashed` i još par ružnih stilova.
* `border-color` boja bordera.

`border-image` shorthand za korištenje slike umjesto linije za border:
* `border-image-source` resource sa slikom, url ili gradijent.
* `border-image-slice` način na koji će se razrezati slika, npr. `30%` sa svake strane.
* `border-image-width` veličina image bordera, može ulaziti u padding.
* `border-image-outset` proširenje image bordera izvan boxa.
* `border-image-repeat` kako se ispunjava border: `stretch`, `repeat`, `round`

`box-shadow: <x-offset> <y-offset> <blur> <spread> <color>` za dodavanje sjene:
* `<x-offset>` negativan pomiče sjenu ulijevo, pozitivan udesno
* `<y-offset>` negativan pomiče sjenu prema gore, pozitivan dolje
* `<blur-radius>` za `0px` je potpuno oštra. Što je broj veći, sjena je šira i mutnija.
* `<spread-radius>` za `0px` sjena je iste veličine kao element. Negativan je smanjuje, pozitivan povećava.

`box-shadow: inset ...` prikazuje sjenu unutar elementa umjesto vani.

Možeš imati više različitih sjena s `box-shadow: 5px 5px red, 0px 0px blue`.

`box-decoration-break` definira kako će se `background`, `padding`, `border`, `margin`, i `box-shadow` ponašati kada se element wrapa u više linija. _Chrome, FF_
* `slice` će se ponašati kao da element nije fragmentiran.
* `clone` će svaki fragment posebno stilizirati.

## Outline

`outline: 1px solid blue` iscrtava liniju oko elementa, ali za razliku od `border` ne utječe na layout i ne moraju biti rectangle.

`outline-offset: 2px` definira udaljenost outlinea od bordera.

## Tables

Tablice se ponašaju različito od `block` i `inline` elemenata. Svaka će tablica biti u svom redu (kao `block`), ali bit će široke koliko i sadržaj unutar njih (kao `inline`).

`text-align` i `vertical-align` na polju definiraju poravnanje teksta unutar njega.

`table-layout` kako se određuje širina stupaca: `auto` prema svim poljima ili `fixed` prema širini polja u prvom retku.

`border-collapse` razdvojenost bordera polja: `separate` (default) svako polje ima svoj, `collapse` imaju zajednički.
`border-spacing: 2px` razmak između bordera dvaju polja. Radi samo ako je `border-collapse: separate`.

`empty-cells` hoće li se prikazati border i pozadina ako je polje prazno: `show` ili `hide`

Ako želiš sakriti dio tablice, koristi `visibility: collapse` umjesto `display: none` kako se pozicija stupaca ne bi morala iznova računati.

`caption-side` gdje će pozicionirati `<caption>` element u odnosu na tablicu:`top` ili `bottom`.

## Position i Stacking Context

*Stacking order* određuje koji element će se iscrtavati iznad kojega. U slučaju da `position` i `z-index` atributi nisu definirani, elementi se iscrtavaju redom kojim su navedeni u dokumentu.

`z-index` elemenata se gleda samo unutar istog *stacking contexta*, što znači da element iz jednog contexta neće biti prikazan iznad višeg contexta čak i ako mu staviš `z-index: 100000`.

*Stacking context* se stvara u parentu ako ima:
* definiran `position` različit od `static` + definiran `z-index`.
* definiran `opacity`, `filter`, `transform`, ili `perspective`.
* `isolation: isolate`.

Z-indeksi se lako mogu zakomplicirati ako ih koristiš nasumično. Umjesto toga, definiraj varijable (kao za boje) za različite layere, tj. stacking kontekste aplikacije: `$z-index-header: 1; $z-index-menu: 2; $z-index-modal: 3;` i sl.

## Float i Block Formatting Context

Element koji ima `float: left` će ostati dio flowa dokumenta, ali će ga se pogurati do lijevog ruba parenta, ili do drugog prethodno floatanog elementa. Na taj način se više floatanih elemenata niže jedan kraj drugoga, a kad napune cijelu širinu parenta, prelaze u idući red.

Ako želiš da se element ne wrapa oko floatova već da se pozicionira ispod njih, koristi `clear: left`.

Pravila pozicioniranja i clearanja floatova primjenjuju se samo na elemente unutar istog *block formatting contexta*. Margine između različitih BFC se ne collapsaju.

*Block formatting context* se stvara u parentu ako ima:
* definiran `float`
* `position: absolute` ili `fixed`
* `display: inline-block`, `table-cell`, `table-caption` ili `flow-root`
* `overflow` koji nije `visible`

Floatani elementi ne utječu na visinu parenta, pa je moguće da visoki floatani element viri izvan nižeg parenta. U slučaju da su u parentu samo floatani elementi, visina parenta će biti `0`, što najčešće ne želiš.

Da bi se to izbjeglo, od parenta se može stvoriti posebni BFC. Po specifikaciji, BFC i floatovi u njemu se ne smiju preklapati, pa će se posljedično parent BFC povećati da obuhvati sve floatove u sebi.

Za stvaranje novog BFC-a koristi se nespretno nazvani `clearfix` hack. Ima mnogo varijatni, a najjednostavnija je `:after { content: ""; display: table; clear: both; }` koja podržava i _IE8+_. Za modernije browsere, koristi `display: flow-root`.

## Fonts

`font-size`: visina slova. Koristi `px`. `em` i `rem` se ne isplate.

`line-height`: visina linije u odnosu na `font-size`. Najsigurnije koristiti unitless mjere, npr. `1.2`.

`font-family` definira listu fontova odvojenih zarezima. Imena fontova s razmacima stavi u navodnike. Odabire se prvi font koji je dostupan. Međutim, ako se neki glyph ne nalazi u odabranom fontu, tražit će se u ostalima. Generične obitelji su zadnji fallback: `serif`, `sans-serif`, `monospace`, `cursive` i `fantasy`(!).

`font-weight`: 100, 200, 300, 400 (`normal`), 500, 600, 700 (`bold`), 800, 900. Fallback do 400 ide na tanje, od 500 na deblje.

`font-style`: `normal`, `italic` ili `oblique` (ali njega nitko ne koristi).

`font-stretch`: `condensed`, `normal`, `expanded`. _IE9+_

Ukoliko zadani `font-syle` ili `font-weight` nisu pronađeni u odabranom fontu, browser će ih pokušati *simulirati*, što često ne izgleda baš najbolje. Postoji `font-synthesis` za disablanje toga ali je podržan samo u _FF_. Do tada, pazi da fontovi koje loadaš imaju stilove i weightove koje koristiš.

`@font-face` definira novi font s atributima:
* `font-family` ime novog fonta
* `src` lista urlova odakle se može skinuti: `url('/fonts/awesome.woff') format('woff'), ...`.
* `font-weight`, `font-style` itd. definiraju tip fonta. Defaultno je sve `normal`.
* `font-display` definira kako će se ponašati prije nego se loada.

Ako želiš detaljno definirati kako se određeni glyphovi fonta ponašaju, postoje:
* `font-kerning` za disablanje pametnog kerninga.
* `font-variant-ligatures` za disablanje ligatura.
* `font-variant-caps` za small-caps i slične.
* `font-variant-numeric` za stilove znamenki
* `font-variant-alternates` za swasheve, ornamente i sl.
_FF, Safari, font-feature-settings za starije browsere_

`font-smoothing` kontrolira anti-aliasing: `none` isključuje, `antialised` ili `subpixel-antialised` za uređaje bez retine.

## Text

`text-align`  kako se inline sadržaj poravnava u elementu. `left`, `right`, `center` i `justify`. `text-align-last` posebno za zadnju liniju teksta.

`text-indent` koliko je uvučena prva linija (pozitivno ili negativno).

`text-transform` mijenja case `uppercase`, `downcase`, `capitalize`.

`text-overflow` definira kako se skriva tekst koji overflowa: `ellipsis` ili custom string (`"..."`).

Linije bez white spacea se po defaultu ne wrapaju. To se može postići s `overflow-wrap: break-word` (`word-wrap: break-word` u starim browserima). Alternativa je `word-break: break-all` koji lomi riječi još agresivnije (čak i kratke riječi koje bi se mogle jednostavno gurnuti u idući red).

`hyphens` hoće li se riječi rastavljati s crticom.
* `none` neće rastavljati nikada.
* `manual` rastavljati će samo na ručno označene `&shy;` (označava mjesto pogodno za rastavljanje) i `&hyphen;` (uvijek se prikazuje kao crtica) u HTMLu.
* `auto` browser će automatski odlučiti, ali će uzeti u obzir i ručno označene.

`white-space` definira kako se white space u sadržaju interpretira:
* `normal` (default), spaceovi se zbijaju u jedan, line breakovi ignoriraju.
* `nowrap` isto kao `normal`, ali cijeli se tekst se prikazuje u jednoj liniji, bez wrapanja.
* `pre` whitespace je očuvan, bez wrapanja
* `pre-wrap` isto kao `pre` ali s wrapanjem.

Za finije upravljanje razmacima između slova i riječi: `letter-spacing: 2em`, `word-spacing: 3em`.

`text-decoration` dodaje liniju ispod (`underline`), iznad (`overline`) ili preko teksta (`line-through`). On je zapravo shorthand koji može imati i boju i stil linije: `text-decoration: underline wavy red`.
`text-decoration-color` definira boju linije.
`text-decoration-skip` za `spaces` linija neće ići ispod razmaka, `ink` linija neće ići preko descendera slova.

`text-shadow: <x-offset> <y-offset> <blur> <color>` radi isto kao `box-shadow`, samo nema `<spread-radius>`.

## vertical-align

`vertical-align` radi samo na `inline` i `inline-block` elementima, te definira kako će se element poravnati u odnosu na parenta.

Neke od vrijednosti su:
* `baseline` poravnava u s baselineom parenta.
* `top`, `bottom` poravnava s vrhom/dnom cijele linije.
* `text-top`, `text-bottom` poravnava s vrhom/dnom parentovog fonta.
* `middle` poravnava sredinu elementa sa sredinom parentove linije.

Za vertikalno centriranje `block` elementa, najjednostavnije je: `position: absolute; top: 50%; transform: translateY(-50%)`.

## Writing Modes

* `direction: rtl` tekst i elementi se pišu sdesna na lijevo (npr. arapski).
* `writing-mode: vertical-rl` vertikalni tekst, stupci zdesna nalijevo (npr. japanski)
* `writing-mode: vertical-lr` vertikalni tekst, stupci slijeva na desno.
* `text-orientation` za usmjeravanje vertikalnog teksta.

## Colors

`rgba(255, 125, 12, 0.2)` (ili `#ff880f`): `rgb` u rasponu `0..255`, i alpha kanal između `0.0` i `1.0`.
`hsla(150, 50%, 50%, 0.5)`: `hue` je kut od `0` do `360` (odabire boju), `saturation` (zasićenost) i `lightness` (svjetlina) su postotak. Ovaj format je malo lakši za vizualizirati na prvi pogled. _IE 9+_

`currentColor` jednak je `color` propertiju elementa, korisno za npr. `border: 1px solid currentColor` _IE 9+_

`@color-profile { ... }` definira color profile koji se koristi u CSS-u.

## Object fit

`object-fit` definira kako se objekt (image, video) resizea da bi stao u parent container:
* `none` neće mijenjati veličinu (default).
* `fill` raširit će se nepoštujući ratio da bi u potpunosti ispunio container.
* `contain` raširit će se do rubova održavajući aspect ratio, ostavlja prazan prostor.
* `cover` raširit će se do ruboba održavajući aspect ratio, odreže višak.
* `scale-down` kao `contain` ili `none`, koji god je manji.

## Background

`background-color` boja pozadine, defaultno `transparent`.
`background-image` jedna ili više slika ili gradijenata, odvojenih zarezima. Image može biti i animirani `gif` ili `svg` s definiranom animacijom u CSS-u.

`background-position` pozicija slike, izražena s `left/right/center`, u `px`, ili `%`. Odvoji zarezima ako imaš više pozadina.

`background-clip` u kojem dijelu elementa se pozadina iscrtava.
  * `content-box` prikazuje se ispod contenta, do paddinga
  * `padding-box` prikazuje se ispod paddinga, do bordera
  * `border-box` prikazuje se i ispod bordera
  * `text` prikazuje se samo ispod teksta, dosta kul izgleda.

`background-origin` isto kao `background-clip` samo ne cropa background nego ga resizea. _IE 9+_

`background-size` _IE 9+_
  * `100px 50px` za fiksnu veličinu
  * `50%` skalira u odnosu na veličinu elementa
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

## Blending _Chrome, FF_

`mix-blend-mode` definira kako se blendaju sadržaj elementa s backgroundom i sadržajem parenta.

`background-blend-mode` kako se blendaju background images i background color.

Blending može biti `multiply`, `screen`, `overlay` i drugi photoshop modovi.

Koristi `isolation: isolate` da se background ne uzima u obzir.

## Gradients _IE10+_

Gradijenti u CSS-u mogu ići kao vrijednost gdje god može biti slika (npr. `background-image`).

`linear-gradient(to right, #000, #fff)` definira linearni gradijent:
* smjer se defira riječima (`to bottom`) ili kutom (`30deg`, `0` je prema gore).
* color stops se definiraju s listom boja (`white, red 20%, blue 70%, black`). Prva je na 0%, zadnja na 100%. Bez postotaka boje točke se ravnomjerno raspoređuju.
* boje mogu biti i transparentne ako koristiš `rgba()`

`radial-gradient(circle, #000, #fff)` definira radijalni gradijent koji se širi iz centra elementa:
* oblik se definira s `circle` ili `ellipse`.
* radius se može definirati pikselima, ili s `closest-side` (do najbliže stranice) ili `farthest-corner` (do najdaljeg kuta)
* color stops su isti kao i kod `linear-gradient`

`repeating-linear-gradient` i `repeating-radial-gradient` mogu se koristiti za ponavljanje gradijenata, npr:
* `repeating-linear-gradient(-45deg, red, red 5px, white 5px, white 10px)` striped background.
* `repeating-radial-gradient(black, black 5px, white 5px, white 10px)` koncentrični krugovi

## Cursor

`cursor` određuje izgled cursora iznad elementa: `default`, `pointer`, `wait`, `zoom-in`, `grab`...

`cursor: url(cursor.png) pointer` podržava custom image s fallbackom.

`pointer-events` definira kako element reagira na mouse event. `auto` radi normalno, `none` propušta click elementu ispod. _IE 11+_

`touch-action` definira kako element reagira na touch. `auto` dopušta sve, `none` disabla sve, `pan-x` i `pan-y` dopušta samo scroll. _IE 10+_

Moguće je selektirati vrstu uređaja po pointeru: _Chrome, Edge_
* `@media (pointer: coarse)` neprecizno upravljanje (npr. touchscreen ili Kinect).
* `@media (pointer: fine)` precizni upravljanje (miš, touchpad, tablet stilus)
* `@media (hover: none)` primarni input ne podržava hover.

## Lists

`list-style-type: none` skriva marker liste.
`list-style-image: url("square.png")` koristi image za marker.
`list-style-position:` `inside` ili `outside` list item content boxa.

## Columns _IE 10+_

Omogućuje prikaz teksta u više stupaca bez da moramo definirati gdje stupac počinje i završava.
* `column-width: 12em` ili `column-count: 3` za definiranje širine/broja stupaca. `columns` je shorthand za obojicu.
* `column-gap: 2em` definira razmak između stupaca.
* `column-rule: 2px solid black` definira liniju između stupaca.
* `column-fill: balance` browser podešava stupce da im visina bude podjednaka (default). `auto` puni stupce po redu.
* `column-span: all` na unutarnjem elementu, proširuje element preko svih stupaca.

## Flexbox _IE 11+_

Flexbox omogućava lakše raspoređivanje elemenata unutar parenta, bez `floata` ili apsolutnog pozicioniranja.

Parent element koji ima `display: flex` ili `inline-flex` je *flex container*, a njegova djeca (uključujući i tekst direktno u njemu) su *flex itemi*.

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
  * `flex: 0 1 auto` item će se smanjiti ako nema dovoljno mjesta, inače ostaje iste veličine. Defaultna vrijednost.
  * `flex: 1 1 auto` item će se smanjiti ako nema dovoljno mjesta, inače će zauzeti svo slobodno mjesto. Skraćeno `flex: auto`.
  * `flex: 0 0 auto` item neće mijenjati veličinu. Skraćeno `flex: none`.

_IE10_ i _IE11_ imaju različite defaultne vrijednosti za `flex` shorthand, pa umjesto `flex: 1` koristi `flex: 1 0 0`.

Nemoj koristiti `%` margine i padding na flex itemima, prekomplicirano je.

## CSS Grid

`display: grid` ili `inline-grid` označava parent kao *grid container*. Njegovi child elementi tretiraju se kao *grid itemi*.

`grid-template-columns` definira kako se itemi postavljaju u stupce.
`grid-template-columns: 200px 200px` dva stupca fiksne širine.
`grid-template-columns: 1fr 2fr` dva stupca, drugi duplo širi od prvoga.
`grid-template-columns: 200px 1fr 1fr` fiksni stupac, a druga dva jednako dijele ostatak prostora.

`grid-template-columns: min-content ...` zauzima minimalnu širinu da sadržaj ne overflowa.
`grid-template-columns: max-content ...` zauzima minimalnu širinu da sadržaj ne wrapa.

`grid-template-columns: 30px 100px 30px 100px 30px 100px` se može kraće napisati kao `grid-template-columns: repeat(3, 30px 100px)`.

`grid-template-rows` je isto, samo za veličinu redova.

`grid-template-areas` dijeli prostor grida na imenovane površine:
```
grid-template-areas: "title title title"
                     "menu header header"
                     "menu sidebar footer"
```

`grid-gap: 5px` za razmak između itema. Shorthand za `grid-column-gap` i `grid-row-gap`.

Grid itemi se defaultno pozicioniraju po redu navedenom u dokumentu, i svaki zauzima jedan cell.
`grid-column-start: 3` pozicionira item da počne od 3. stupca.
`grid-column-end: 5` proteže item do 5. stupca. S gornjom vrijednosti, item će biti širok 2 stupca.
`grid-area: "title"` proteže item na površini `title` zadanu u `grid-template-areas`.

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

Transformiranje elemenata koje ne utječe na layout, npr `transform: scale(1.5)`. Transformacije se mogu kombinirati: `transform: scale(1.5) rotate(20deg)`.

`transform-origin` definira ishodište elementa (os za rotaciju, fiskna točka za scale i skew). Defaultno je središte elementa.

2D transformacije:
* `rotate(20deg)`, defaultna os je centar elementa. Prima i `grad`, `rad`, i `turn`, npr. `3turn` za tri pune rotacije.
* `scale(0.75)` za jednoliko, `scale(1.2, 2.0)` za različito, ili `skewX(5deg)`, `skewY(10deg)`.
* `translate(1px, 2px)`, ili `translateX(-10px)`, `translateY(25%)`.
* `skew(20deg, 30deg)`, ili `skewX(5deg)`, `skewY(10deg)`.

Za 3D transformacije potrebno je definirati perspektivu.
* `perspective-origin(10px 30px)` definira koordinate vanishing pointa.
* `transform: perspective(20px) ...` definira udaljenost perspektive. Što je broj manji, bliže smo, pa je promjena veća.
* perspektiva definirana na parentu primjenjuje se na sve child elemente.

3D transformacije:
* `rotateX` (os rotacije je horizont), `rotateY` (os rotacije je y-os), `rotateZ` (rotira na ravnini ekrana).
* `scaleX`, `scaleY`, `scaleZ` (Z se primjeti tek ako je element rotiran)
* `translateX`, `translateY`, `translateZ`

Ako želiš da se više elemenata međusobno preklapa u istom prostoru, trebaju biti u istom *3d rendering contextu* (slično kao i *stacking context* za 2d). Najlakši način za stvoriti zajednički 3d context je napraviti parenta s `transform-style: preserve-3d`, a djeci dati `position: absolute`.

Dodavanje određenih propertija na parent može vratiti `transform-style` u `flat` pa se 3D izgubi. Izbjegavaj `overflow`, `clip-path`, `opacity`, `filter`.

Ako se element 3D transformira unutar parenta koji se isto transformira, po defaultu neće dobiti svoj 3D prostor već će ostati flat u parentu. Za omogućavanje nested 3D transformacije, dodaj `transform-style: preserve-3d` u parenta.

Ako 3D tranformacija okrene element da mu vidimo guzicu, defaultno će biti vidljiv. Za sakriti guzicu, koristi `backface-visibility: hidden`.

## Clip Paths and Shapes _Chrome, Safari_

`clip-path` definira dio elementa koji će se prikazati i sakriti ostatak.

`shape-outside` definira kako će se wrapati text oko elementa, bez da mijenja oblik elementa. Radi samo za floatane elemente.

Vrijednosti koje mogu primiti:
* `inset(10px 20px 30px 40px)` definira shape od bordera elementa.
* `circle()` ili `ellipse()`. `circle(100px at 30% 50%)` definira radius i centar.
* `polygon(10px 10px, 20px 20px, 30px 30px)` definira poligon pomoću vrhova.
* `url(image.png)` koristi se alpha kanal slike (wow!).

`shape-margin: 10px` dodaje marginu oko oblika.

## Resize

`resize: horizontal`, `vertical` ili `both` omogućuje korisniku da može resizati element. Element mora imati postavljen `overflow` na nešto osim `visible`.

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

## Variables (Custom Properties) _Chrome, FF, Safari_

Korisno za reusanje vrijednosti. Za razliku od preprocessor varijabli, mogu se mijenjati u runtimeu.
* `:root { --main-color: red; }` postavljanje vrijednosti varijable.
* `p { color: var(--main-color); }` dohvaćanje vrijednosti varijable.

## Media queries

Media queries služe za prepoznavanje vrste i značajki browsera na kojem se stranica prikazuje. Više uvjeta se može kombinirati pomoću `and`, npr. `@media screen and (min-device-width: 320px)`.

Media types:
* `@media screen` za normalne browsere.
* `@media print` za printanje stranice.
* `@media speech` za screen readere.

Media features:
* `@media (min-width: 200px)` i `@media (max-height: 30em)` za širinu i visinu viewporta.
* `@media (orientation: portrait)` ili `landscape` za orijentaciju uređaja.
* `@media (min-resolution: 300dpi)` ili `2dppx` za rezoluciju uređaja.

Korištenje relativnih veličina u media querijima (`30em`) omogućava da se content ispravno prikazuje čak i ako korisnik mijenja defaultnu veličinu fonta.

## Feature queries _Chrome, FF, Edge_

`@support (display: grid) { ... }` primjenit će block samo ako browser podržava property u zagradama.

Ovo *ne služi* da bi ispitao da li je `border-radius` podržan prije nego ga probaš primijeniti - browser će ionako preskočiti property koji ne podržava. Ovo služi da grupiraš propertije koje želiš da se primjene ako je `border-radius` podržan, npr. želiš `border-width: 3px` samo ako će biti i `border-radius`.

Pravila se mogu kombinirati s `or`, `and` i `not`, npr. `(border-radius: 1px and display:grid)`

Zbog starih browsera strukturiraj css da ide *prvo fallback code*, *onda support block*.

## Literatura

* http://cssreference.io/
* https://tympanus.net/codrops/css_reference
* http://flexboxfroggy.com/ za učenje flexboxa
