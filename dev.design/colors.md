# Boje

Boja u printeru nastaje od molekula, te se miješa *subtraktivno* - što više boja miješaš, rezultat je tamniji.

Boja u ekranima nastaje od svijetla, te se miješa *aditivno* - što više boja miješaš, rezultat je svijetliji.

## Color Spaces

**Color Space** je matematički model za opisivanje boja koristeći set brojeva. Različiti prostori koriste različite parametre, npr. `Monochrome` koristi 1 - svjetlinu; `RGB` koristi 3 - red, green i blue; `CMYK` koristi 4 - cyan, magenta, yellow i key.

**Gamut color spacea** je raspon boja koje može definirati, a **gamut uređaja** je raspon boja koje može prikazati. Zamisli ga kao podskup unutar granica razlučivosti ljudskog oka

**Color Depth** je broj boja koje možeš definirati. Što više bitova koristiš, više boja i međunijansi imaš.

**Color Profile** je standardizirani format koji definira color space, odnosno koje boje bytovi predstavljaju. Npr. `IEC 61966-2-1` definira `sRGB` color space. Drži se u zasebnom fileu ili embeddan u slici.

## RGB vs HSL

`RGB` je defacto standard za digitalne medije, jer uređaji koriste RGB lampice za prikaz. Problem s RGB-om je što nije prirodan način za ljudsko opisivanje boja. Kako RGB boju učiniti življom, ili svijetlijom?

`HSL` je prostor prilagođen ljudskoj intuiciji. *Hue* predstavlja boju, *Chroma* živost, *Lightness* svjetlinu. Problem je što način na koji HSL računa lightness ne uzima u obzir da ljudsko oko doživljava različite boje različito svijetlima (npr. žuta i plava iste lightness vrijednosti, žuta će se opet činiti svijetlija).

Percepcijski uniformni prostori, (npr. `CIELAB`)  uzimaju u obzir ljudsku percepciju i koriste transformacije kako bi boje s istim dimenzijama bile jednako percipirane.

## Boje na Webu

Cijeli web je zasnovan na `sRGB` prostoru, zato što većina displaya i može prikazati samo `sRGB` prostor. Ali u zadnje vrijeme počeli su se razvijati displayi s širim gamutom (tzv. *Wide Gamut* ili *extended-color* uređaji). Npr. moderni Appleovi ekrani podržavaju `Display P3` prostor koji ima 25% širi gamut od `sRGB`.

Ako se slika s `P3` profilom otvori na `sRGB` displayu, ili obratno, `WebKit` će izvesti *color-matching*, odnosno prilagoditi boje da se što ispravnije prikažu. Ostali browseri će samo proslijediti sliku grafičkoj kartici, zbog čega neke boje neće izgledati kako bi trebale.

Ako ne želiš dodavati color profile u sliku (ipak pridonosi veličini), `WebKit` će pretpostaviti da se radi o `sRGB`. Ako želiš servirati sliku različitog gamuta za različiti display, možeš koristiti media query `color-gamut: p3`.

I dalje ostaje neodlučeno kako koristiti drugi gamut u CSS-u i HTML Canvasu. `#ff0000` `rgba`, i `hsl` opisuju samo `sRGB`.

## Gamma Correction

Počnemo li od crne boje, i konstantno povećavamo količinu svijetla, ljudskom oku će se činiti kao da je većina boja koje dobijemo svijetlo siva. Iako je ovakva paleta linearna (u fizikalnom smislu), percepcija količine svijetla u ljudskom oku nije linearna, nego logaritamska (slično kao i zvuk).

`RGB24` enkodira svaku boju 24-bitnom vrijednosti, što daje 8 bitova za svaku komponentu, odnosno 256 različitih nijansi. Rasporedimo li te nijanse matematički linearno, imat ćemo (za ljudsko oko) premalo tamnih nijansi, a previše svijetlih.

Želimo optimizirati encoding za ljudsku percepciju. Zato se koristi **gamma korekcija**: transformacija fizikalno linearnih vrijednosti u percepcijski linearne. `(128, 128, 128)` odašilje samo 22% svijetla u odnosu na `(256, 256, 256)`, ali našem se oku čini da je točno upola tamnije.

Formula za transformaciju je `V_en = V_lin^(1/γ)`, gdje su `V` vrijednosti intervala `[0, 1]`.  Svaka color space specifikacija definira svoju gamma vrijednost `γ`. Uobičajena je `2.2`, ali neki imaju custom krivulje (npr. `sRGB`.)

Problem je što većina algoritama za obradu slike krivo pretpostavlja da radi s netransformiranim bojama, pa razne operacije daju krive rezultate. Takvu grešku rade CSS i Photoshop gradijenti, color i alpha blending, image resizing i antialising. Loše handlanje gamme je također glavni razlog što CGI animacija izgleda fake i plastično.

# Literatura

* http://blog.johnnovak.net/2016/09/21/what-every-coder-should-know-about-gamma
* https://webkit.org/blog/6682/improving-color-on-the-web
* https://stripe.com/blog/accessible-color-systems

