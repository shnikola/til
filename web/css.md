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
* `:disabled` - inputi s `disabled` atributom
* `:enabled` - inputi bez `disabled`
* `:required` - inputi s `required` atributom
* `:optional` - inputi bez `required`
* `:read-only` - inputi s `readonly` atributom
* `:read-write` - inputi bez `readonly`. Uključuje i `contenteditable` elemente.
* `:valid` - inputi s valueom ispravnog formata (npr. `type=email`)
* `:invalid` - inputi s valueom neispravnog formata
* `:in-range` - inputi s valueom unutar zadanog rangea (min i max atributi)
* `:out-of-range` - inputi s valueom izvan zadanog rangea
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


TODO:
transform
filters https://developer.mozilla.org/en-US/docs/Web/CSS/filter
svg
cubic-bezier()
