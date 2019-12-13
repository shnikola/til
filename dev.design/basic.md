# Design

## Generalni savjeti

Ne dizajniraš za sebe, nego za korisnika stranice.

Iteracija je ključna za kvalitetu.

## Style guide

Za svaki ozbiljniji projekt napravi mini style guide s tipografijom i elementima koji se ponavljaju.

Ne koristi više od tri font varijante na jednom siteu.

**Linkovi** imaju 3 stanja: base, hover, focus. Plus ti isti za aktivne linkove u navigaciji.

U **formama** definiraj labele, input help i errore. Custom radio i check boxevi imaju stanja koja se kombiniraju (checked/unchecked, hover/not, focus/not, enabled/disabled, error/not).

Definiraj kako interaktivnost izgleda i koristi to samo za interaktivne elemente. Ne koristi istu boju za linkove i za naglašavanje bitnog sadržaja.

Definiraj sve slučajeve promjenjivog sadržaja: što ako je teksta premalo ili previše? Što ako sadržaja nema?

## 8 Point Grid System

Za sve veličine (height, width, padding, margins) koristi višekratnike broja 8. Na taj način će UI biti konzistentan, uređaji s većom pixel gustoćom neće imati problema s mutnih 0.5 piksela, a usto se i ne moraš misliti da li staviti `8px` ili `10px`.

## Information Design

2 golden rules of information design: show the data; show comparisons.

## Image Formats

`JPEG` za fotografije i slike s velikim brojem boja. Lossy format koji dobro komprimira gradijente. Optimizira se smanjenjem kvalitete i noisa. Koristi *progressive JPEG* da možeš korisniku što prije ponuditi sliku.

`GIF` za animacije koje ne možeš napraviti CSSom. Lossless format s 256 boja. Optimizira se smanjenjem ditheringa i broja boja.

`PNG-8` za slike s manjim brojem boja. Lossless format s 26 boja, ali bolje komprimira od GIFa. Optimizira se smanjenjem ditheringa i broja boja.

`PNG-24` za partial transparency. Podržava mnogo boja, ali stvara puno veći file od JPEGa jer je lossless.

# Literatura

* Designing for performance: http://designingforperformance.com/
* https://www.toptal.com/front-end/to-designers-from-a-front-end-developer
* https://medium.com/retronator-magazine/pixels-and-voxels-the-long-answer-5889ecc18190
* https://github.com/leandromoreira/digital_video_introduction

