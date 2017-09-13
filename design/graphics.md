# Graphics

## Colors on web

https://webkit.org/blog/6682/improving-color-on-the-web

Boja u printeru nastaje od molekula, te se miješa *subtraktivno* - što više boja miješaš, rezultat je tamniji.
Boja u ekranima nastaje od svijetla, te se miješa *aditivno* - što više boja miješaš, rezultat je svijetliji.

*Color Space* koristi set parametara za definiranje i uspoređivanje boja, npr. Monochrome (koristi 1 - svjetlinu), RGB (red, green i blue) ili CMYK (cyan, magenta, yellow i black).
*Color Profile* je standardizirani format koji definira *Color Space*, odnosno koje boje bytovi predstavljaju, npr. `IEC 61966-2-1` definira `sRGB` color space. Drži se u zasebnom fileu ili embeddan u slici.
*Gamut* Color Spacea je raspon boja koje može definirati, a *Gamut* uređaja je raspon boja koje može prikazati. Zamisli ga kao površinu unutar granica razlučivosti ljudskog oka.
*Color depth* je količina boja koju možeš definirati. Što više bitova koristiš, više boja i međunijansi imaš.

Cijeli web je zasnovan na `sRGB` prostoru, zato što većina displaya i može prikazati samo `sRGB` prostor. Ali u zadnje vrijeme počeli su se razvijati displayi s širim gamutom (tzv. *Wide Gamut* ili *extended-color* uređaji). Npr. moderni applovi ekrani podržavaju `Display P3` prostor koji ima 25% širi gamut od `sRGB`.

Ako se slika s `P3` profilom otvori na `sRGB` displayu, ili obratno, `WebKit` će izvesti *color-matching*, odnosno prilagoditi boje da se što ispravnije prikažu. Ostali browseri će samo proslijediti sliku grafičkoj kartici, zbog čega neke boje neće izgledati kako bi trebale.

Ako ne želiš dodavati color profile u sliku (ipak pridonosi veličini), `WebKit` će pretpostaviti da se radi o `sRGB`. Ako želiš servirati sliku različitog gamuta za različiti display, možeš koristiti media query `color-gamut: p3`.

I dalje ostaje neodlučeno kako koristiti drugi gamut u CSS-u i HTML Canvasu. `#ff0000` `rgba`, i `hsl` opisuju samo `sRGB`.

## Image Formats

* `JPEG` za fotografije i slike s velikim brojem boja. Lossy format koji dobro komprimira gradijente. Optimizira se smanjenjem kvalitete i noisa. Koristi *progressive JPEG* da možeš korisniku što prije ponuditi sliku.
* `GIF` za animacije koje ne možeš napraviti CSSom. Lossless format s 256 boja. Optimizira se smanjenjem ditheringa i broja boja.
* `PNG-8` za slike s manjim brojem boja. Lossless format s 26 boja, ali bolje komprimira od GIFa. Optimizira se smanjenjem ditheringa i broja boja.
* `PNG-24` za partial transparency. Podržava mnogo boja, ali stvara puno veći file od JPEGa jer je lossless.

Dodatna optimizacija:
* exportaj sliku minimalnih dimenzija koje su ti potrebne.
* provuci je kroz `imageOptim` (desktop) ili `smush.it` (web) lossless optimizatore.
* `data URI` za jako male, jednostavne slike; ali provjeri popravlja li to performance.
* za velike GIFove je optimalnije koristiti WebM video.

## Pixel Density, Demystified

https://medium.com/@pnowelldesign/pixel-density-demystified-a4db63ba2922

**Pixel Density**: broj pixela po inchu (**ppi**). Prvi Mac je imao 72ppi. Moderniji ekrani imali su do 160ppi.
**Retina**:
  * udvostručen broj ppi-a, što omogućuje turbo kristalnu sliku.
  * da bi zadržao jednaku *stvarnu* veličinu, button mora imati 44px za normalni ekran, a 88px za retinu.
**Points** (**pt**): abstrakta mjerna jedinica, uvedena radi lakše komunikacije. 1pt = 1px na normalnim ekranima, 2px na retina ekranima. Button ima 44pt. To je iOS mjera, Android ima **Density Independant Pixels**(**dp**) koji su slični.

Za različite uređaje najčešće ne želimo da button bude iste veličine. Npr. za mobilne uređaje želiš da bude veći od laptopa jer imamo prste deblje od kursora. Za televizor želiš da bude veći jer ti je puno dalji od laptopa.
  * To će se dogoditi ili automatski zbog manje gustoće pixela (TV ima samo 40dpi)
  * ili ćeš morati jednostavno izdizajnirati veće buttone

Dizajniraj u vektorima, i za 1x. Onda skaliraj na 2x, 3x i koliko je već potrebno.

## Gamma Correction

http://blog.johnnovak.net/2016/09/21/what-every-coder-should-know-about-gamma

Gradijent koji linearno povećava količinu svijetla (u fizikalnom smislu), ljudskom oku neće izgledati linearno - izgledat će kao 10% tamnih i 90% svijetlih nijansi. To je iz razloga što percepcija količine svijetla u ljudskom oku nije linearna, nego logaritamska (slično kao i zvuk).

Ako enkodiramo boje s 24-bitnom vrijednosti (RGB24), to daje 8 bitova za svaku komponentu, odnosno 256 različitih nijansi. Ukoliko te nijanse rasporedimo matematički linearno, imat ćemo (za ljudsko oko) premalo tamnih nijansi, a previše svijetlih.

Želimo optimizirati encoding za ljudsku percepciju. Zato se koristi *gamma correction*: transformacija fizikalno linearnih vrijednosti u percepcijski linearne.

De-facto standard `sRGB` koristi gamma enkodirane vrijednosti. Zato `(128, 128, 128)` ne odašilje 50% svjetla u odnosu na `(256, 256, 256)`, nego oko 22%.

Problem je što većina algoritama za obradu slike krivo pretpostavlja da radi s netransformiran bojama, pa razne operacije daju krive rezultate. Takvu grešku rade CSS i Photoshop gradijenti, color i alpha blending, image resizing i antialising. Loše handlanje gamme je također glavni razlog što CGI animacija izgleda fake i plastično.

Zato uvijek provjeri uzima li alat kojeg koristiš gamma correction u obzir.

## Video encoding

Sliku prikazujemo kao 2D matricu piksela. Prikazujemo li boje pomoću tri primarne komponente (RGB), imamo tri 2D matrice čije su vrijednosti intenzitet boje. **Bit depth** označava koliko bitova koristimo za zapis intenziteta. Ako koristimo 8 bita po boji (vrijednosti 0-255), bit depth iznosi 24.

Rezolucija predstavlja veličinu jedne matrice, tj. `širina * visina`.
Display aspect ratio predstavlja odnos između širine i visine slike ili videa (npr. 16:9).
Pixel aspect ratio predstavlja odnos širine i visine samog piksela koji ne mora nužno biti kvadrat.

Video definiramo kao niz frameova u vremenu. **Bit rate** se računa kao `širina * visina * bit depth * frames per second`. Bit rate može biti konstantan (CBR), a može biti i promjenjiv (VBR).

Za prividno povećanje frame ratea ekrana bez da se poveća bandwith, prije se koristio *interlaced video* koji bi prikazao pola slike (u trakicama) u jednom frameu, a drugu polovicu u idućem. Danas ekrani koriste *progressive scan* koji omogućuje da se linije svakog framea slijedno iscrtavaju.

Bez kompresije 1h videa u 720p rezoluciji zauzimao bi 278GB. Jedan od načina kompresije iskorištava činjenicu da naše oči mnogo bolje raspoznaju svjetlinu nego boje. Umjesto RGB modela, koristimo **YCbCr** s tri kanala: brightness, chroma blue i chroma red. Znajući da je oko manje osjetljivo na boje, radimo *Chroma subsampling* koristeći manje rezolucije za chroma kanale (npr. 320x180, ako je original video 1280x720).

Drugi način kompresije je smanjenje redundancije. Umjesto zapisivanja cijelokupnih podataka za svaki frame, možemo zapisivati promjenu koja se događa između dva framea. Tako razlikujemo 3 vrste frameova: I-frame (sadrži sve podatke), P-frame (sadrži razliku u odnosi na prošli), B-frame (sadrži razliku u odnosu na prošli i budući)

# Literatura

* https://medium.com/retronator-magazine/pixels-and-voxels-the-long-answer-5889ecc18190
* https://github.com/leandromoreira/digital_video_introduction
