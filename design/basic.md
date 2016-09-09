# Design

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


# How to Retinify Your Site
* `SVG` ne treba ništa
* `GIF` ako nije animiran, konvertiraj u `PNG`.
* `PNG`, `JPG`, `GIF` napravi verziju uvećanu 2x. Optimiziraj s `imageoptim`.
  * za `<img>`, koristi uvećanu verziju i definiraj `width` i `height` originalne veličine u CSS.
  * za `background-image` koristi uvećanu verziju i `background-size: 100%`.
* `Favicon` napravi `favicon.ico` file s 16x16 i 32x32 varijantama. `xiconeditor` je dobar alat.
