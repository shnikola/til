# SVG

Svg se u browseru može koristiti na nekoliko načina:
* kao `<img src='logo.svg'>` za neinteraktivne slike
* kao `<svg>`, čime dijeli DOM s HTML-om
* kao `<object data='logo.svg'>` stvara scope za CSS i DOM, a omogućava interaktivnost (npr. hover).


## <svg>
Koordinatni sustav `<svg>` elementa ima origin `(0,0)` gore lijevo.
* `width` i `height` definiraju veličinu elementa i defaultni `viewBox`
* `viewBox="0 0 300 400"` definira koji dio (x, y, width, height) infinite canvasa unutar elementa je vidljiv.

SVG se sastoji od elemenata čiji izgled možemo definirati na više načina:
* atributom, npr. `fill="red"`
* `style` atributom, npr. `style="fill:red"`
* CSS pravilom, npr. `circle { fill: red; }`


## Simple objects
Objekti se isrtavaju redom kojim su navedeni u SVG-u, tj. posljednji objekt će biti iznad svih

`<line>` se definira s `x1`, `y1` i `x2`, `y2`
`<rect>` se definira s `x` i `y` gornjeg lijevog vrha, te `width` i `height`
`<circle>` se definira s `cx` i `cy` centra, te `r` za radius.
`<ellipse>` se definira s `cx` i `cy` centra, te `rx` i `ry` za radiuse.
`<polygon>` i `<polyline>` se definiraju s `points`.
`<image>` se definira s `x`, `y`, `width` i `height`, te `xlink:href`


## Path
Svi gore navedeni objekti, a i bilo koji drugi, mogu se nacrtati pomoću `<path>` objekta.
Path prima niz naredbi i argumenata, npr. `<path d="M 150,0 L 75,200 L 225,200 Z">`. Uppercase naredbe koriste apsolutne koordinate, lowercase relativne u odnosu na trenutnu poziciju.
* `M 150,0` pozicionira se na određenu točku (bez crtanja)
* `L 200,0` crta ravnu liniju od trenutne do dane točke. `h 10` crta horizontalnu, a `v 10` vertikalnu liniju.
* `Z` zatvara path, tj. povlači ravnu liniju do zadnje pozicije odabrane s `M`.
* `Q 20,40 30,20` crta kvadratičnu bezierovu krivulju, `C` kubičnu
* `A` crta elliptical arc koji prima milijun parametara.


## Text
Ako želiš tekst unutar SVG-a, ubaci ga u `<text>` element.
* pozicija se definira s `x` i `y`
* stilizira se sa standardnim `font-size`, `font-family` i ostalim atributima.
* kao i ostali elementi, mogu mu se mijenjati `stroke` i `fill`, koristiti filteri i transformacije.
* za selektiranje dijela teksta, koristi `<tspan>` unutar elementa.
* za vijuganje po pathu, koristi `<textPath xlink:href="#MyPath">` unutar elementa.
* ovakav tekst je screen-readable, search enginei će ga prepoznati, i bit će pronađen s ctrl-F.

Za objekte koji sadrže grafički tekst, može se koristiti `<title>` element unutar njih za accessibility.


## Stroke, fill, opacity
Svaki objekt ima svoj `stroke` i `fill` (ako je path zatvoren).
* `stroke` je boja linije, `none` za prozirno
* `stroke-width` je širina linije.
* `stroke-linecap` kako izgleda vrh linije (`butt`, `round`, `square`)
* `stroke-dasharray` za dashed linije. `5,5` znači 5px linije, 5px razmaka.

* `fill` boja unutrašnjosti, `none` za prozirno
* `fill-rule` kako se definira unutrašnjost za pathove koji se višestruko sjeku (`nonzero`, `evenodd`)

* `opacity` za cijeli objekt, ili zasebno `stroke-opacity` i `fill-opacity`


## Grouping and reusing
Za grupiranje elemenata koje možeš kasnije reusati koristi `<g>`, ili još bolje `<symbol>` koji može definirati vlastiti `viewBox`. Tako definirani elementi insertaju se pomoću `<use xlink:href="symbolId">`

## CSS
* `d: path("M2,2 Q8,2 8,8")` za mijenjanje patha


## Savjeti
* definiraj oblike po funkciji, ne po izgledu. kasnije ćeš ih stilizirati.


# Literatura
* http://w3c.github.io/svgwg/specs/svg-authoring
