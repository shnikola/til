# Design

## How to Retinify Your Site
* `SVG` ne treba ništa
* `GIF` ako nije animiran, konvertiraj u `PNG`.
* `PNG`, `JPG`, `GIF` napravi verziju uvećanu 2x. Optimiziraj s `imageoptim`.
  * za `<img>`, koristi uvećanu verziju i definiraj `width` i `height` originalne veličine u CSS.
  * za `background-image` koristi uvećanu verziju i `background-size: 100%`.
* `Favicon` napravi `favicon.ico` file s 16x16 i 32x32 varijantama. `xiconeditor` je dobar alat.


## Colors on web
https://webkit.org/blog/6682/improving-color-on-the-web
Boja u printeru nastaje od molekula, te se miješa *subractivno* - što više boja miješaš, rezultat je tamniji.
Boja u ekranima nastaje od svijetla, te se miješa *aditivno* - što više boja miješaš, rezultat je svijetliji.

*Color Space* koristi set parametara za definiranje i uspoređivanje boja, npr. Monochrome (koristi 1 - svjetlinu), RGB (red, green i blue) ili CMYK (cyan, magenta, yellow i black).
*Color Profile* je standardizirani format koji definira *Color Space*, odnosno koje boje bytovi predstavljaju, npr. `IEC 61966-2-1` definira `sRGB` color space. Drži se u zasebnom fileu ili embeddan u slici.
*Gamut* Color Spacea je raspon boja koje može definirati, a *Gamut* uređaja je raspon boja koje može prikazati. Zamisli ga kao površinu unutar granica razlučivosti ljudskog oka.
*Color depth* je količina boja koju možeš definirati. Što više bitova koristiš, više boja i međunijansi imaš.


Cijeli web je zasnovan na `sRGB` prostoru, zato što većina displaya i može prikazati samo `sRGB` prostor. Ali u zadnje vrijeme počeli su se razvijati displayi s širim gamutom (tzv. *Wide Gamut* ili *extended-color* uređaji). Npr. moderni applovi ekrani podržavaju `Display P3` prostor koji ima 25% širi gamut od `sRGB`.

Ako se slika s `P3` profilom otvori na `sRGB` displayu, ili obratno, `WebKit` će izvesti *color-matching*, odnosno prilagoditi boje da se što ispravnije prikažu. Ostali browseri će samo proslijediti sliku grafičkoj kartici, zbog čega neke boje neće izgledati kako bi trebale.

Ako ne želiš dodavati color profile u sliku (ipak pridonosi veličini), `WebKit` će pretpostaviti da se radi o `sRGB`. Ako želiš servirati sliku različitog gamuta za različiti display, možeš koristiti media query `color-gamut: p3`.

I dalje ostaje neodlučeno kako koristiti drugi gamut u CSS-u i HTML Canvasu. `#ff0000` `rgba`, i `hsl` opisuju samo `sRGB`.


## Progressive Web Apps
https://infrequently.org/2016/09/what-exactly-makes-something-a-progressive-web-app/
Za mobile Chrome, iskočit će "Add To Homescreen" prompt. Nužni kriteriji da Chrome to prikaže su:
* učitavaju se preko HTTPSa
* mogu se učitati i offline (makar samo imali custom offline page)
* koriste manifest s barem: `name`, `short_name`, `start_url`, `display` i png iconom od `144x144` ili više.

Još je dobro imati:
* mobile-friendly design
* početno učitavanje u manje od 5 sekundi. Kad je Service Worker loadan, učitavanje u manje od 2 sekunde, neovisno o stanju mreže.
* podržavanje različitih uređaja i browsera
* fluidne animacije

`GoogleChrome/lighthouse` je dobar tool za provjeru svih tih kriterija.


## Inline Form Validation
https://baymard.com/blog/inline-form-validation
* Ne želiš da error iskoči čim user počne tipkati - validiraj nakon što user napusti field, ili kad unese zadanu veličinu teksta (npr. za kreditnu karticu)
* Međutim, kad error postoji na fieldu, ukloni ga čim user promijeni input u validan - nemoj čekati da napusti field.
* Pozitivna validacija (zeleni check) pomaže.
