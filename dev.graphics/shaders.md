# Shaders

## Fragment Shaders

Shaderi su funkcije oblika `main(out vec4 fragColor, in vec2 fragCoord)`. Za određenu koordinatu piksela (tj. *fragmenta*) vraćaju boju kojom će se taj piksel popuniti. Tako svaki piksel poziva istu funkciju, što se naziva *SIMD*: Same Instruction Multiple Data.

Prednost shadera je što se izvršavaju direktno na brzom GPU, i zaobilaze CPU u potpunosti. SIMD omogućava potpunu paralelizaciju renderinga - svaki piksel se može računati na zasebnom GPU coreu. Pritom je svaki thread *blind* (nema uvid u ostale threadove) i *memoryless* (nema spremanja podataka između poziva).

Shaderi se programiraju u `GLSL` (openGL Shading Languageu) ili `HLSL` (za DirectX Microsoftove platforme). GLSL je sličan `C`-u, uz ugrađene helper funkcije i tipove poput `vec3`.

## Inputs

`fragCoord` koordinate koje shader prima su zadane apsolutno, i uvijek ih treba normalizirati u `[0, 1]` interval s `st = fragCoord.xy / iResolution`.

Osim koordinata, svaka funkcija prima *uniform* inpute, tj. inpute koji su isti za svaki fragment u trenutnoj iteraciji. Neki od njih su:
* `iResolution` rezolucija viewporta u pikselima
* `iMouse` koordinate pozicije cursora
* `iTime` broj sekunda otkad je shader pokrenut.

## Interpolacije

Shaderi podržavaju interpolacijske funnkcije koje su hardware akcelerirane.

`step(0.5, x)` vraća `x < 0.5 ? 0 : 1`.
`step(x, 0.5)` vraća `x < 0.5 ? 1 : 0`.
`clamp(0.1, 0.9, x)` ograničava na vrijednosti od `0.1` do `0.9`
`smoothstep(0.1, 0.2, x)` radi prijelaz po `[0.1, 0.2]`, zatim `1`.
`smoothstep(0.1, 0.2, x) - smoothstep(0.3, 0.4, x)` će na `[0.2, 0.3]` biti `1`, s smooth prijelazom.

`mix(colorA, colorB, 0.3)` miješa boje, 30% A i 70% B.
`mix(colorA, colorB, step(...))` radi kao `step > 0 ? colorA : colorB`.

`atan(v.x, v.y)` vraća kut vektora u intervalu `[-pi, pi]`.

## Transformacije

`normalize(v)` vraća vektor jednakog smjera, ali duljine `1`.

`st - vec(0.5)` translatira ishodište u sredinu ekrana.
`st * mat2(cos(a), -sin(a), sin(a), cos(a))` rotira oko ishodišta za `a` rad.
`st * mat2(scale.x, 0.0, 0.0, scale.y)` skalira oko ishodišta za `scale`.

## Signed Distance Fields

*Signed Distance field* je funkcija koja opisuje neki oblik. Za svaku koordinatu vraća skalar s udaljenosti točke od oblika. Ako je koordinata na površini oblika, funkcija vraća `0`, a ako je unutar oblika vraća negativnu vrijednost.

# Literatura

* [https://thebookofshaders.com]
