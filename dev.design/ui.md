# UI

## Boje

Gradient tamnjenja (npr. `hsl(194, 100%, 100%)` do `hsl(194, 100%, 80%)`) će biti življi ako im pomakneš hue za 10-20 stupnjeva (npr. `hsl(194, 100%, 100%)` do `hsl(208, 100%, 80%)`).

Obojena linija na vrhu stranice debljine 4-6px dobro izgleda. Isto vrijedi za modale i panele.

Umjesto razdvajanja contenta linijom, koristi background različite boje (npr. sivi).

## Tekst i boje

Čisti sivi tekst izgleda čudno na obojenoj pozadini. Umjesto toga, koristi hue pozadine (npr. ako je pozadina `hsl(192, 56%, 56%)`, boja teksta neka bude `hsl(192, 17%, 99%)`).

Bijelom tekstu na svijetloj pozadini dodaj lagani shadow (`text-shadow: 0 1px 2px rgba(0, 0, 0, 0.2)`) za bolju čitljivost i da iskoči.

Ako su ikone deblje od teksta pokraj njih, koristi boju svijetliju od teksta za neaktivni state.

## Sjene

Svaki element bi trebao biti osvijetljen iz istog izvora. To znači da bi horizontalni i vertikalni offset trebali uvijek biti u istom omjeru.

Elementi koji su "bliži" korisniku (npr. modali) trebaju imati veći blur radius i manji opacity.

Najbolje je unaprijed definirati "razine" na kojima će se elementi postavljati, npr:
* Razina 1: `box-shadow: 4px 8px 8px hsl(0deg 0% 0% / 0.33);`
* Razina 2: `box-shadow: 8px 16px 16px hsl(0deg 0% 0% / 0.25);`

Za posebno lijepe sjene (ako imaš luksuz da ti takve stvari mogu biti bitne), možeš koristiti layered box shadowe, npr:
```
box-shadow:
  0 1px 1px hsl(0deg 0% 0% / 0.075),
  0 2px 2px hsl(0deg 0% 0% / 0.075),
  0 4px 4px hsl(0deg 0% 0% / 0.075),
  0 8px 8px hsl(0deg 0% 0% / 0.075)
```

## Buttons

Za hover na button podigni za 1px (čini se kao da iskoči) i povećaj drop shadow spread.

## Gradient Text

`background-image: linear-gradient(90deg, blue, red)`
`color: transparent`
`-webkit-background-clip: text`

## Animated gradient buttons

Na hover postavi background, npr:
`background: linear-gradient(to right, #7258FF, #B658FF, #FF34A1, #FF5858, #FCCF58, #E5FC58, #8DE086, #86E0B5, #69DCE3, #698BE3, #7258FF)`

`background-size: 1000% auto` da se raširi.

Zatim dodaj animaciju:
`0% { background-position-x: 0% }` i `100% { background-position-x: 100% }`.

# Literatura

* [https://twitter.com/i/events/880688233641848832]

