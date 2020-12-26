# UI Examples

## Colors

Gradient tamnjenja (npr. `hsl(194, 100%, 100%)` do `hsl(194, 100%, 80%)`) će biti življi ako im pomakneš hue za 10-20 stupnjeva (npr. `hsl(194, 100%, 100%)` do `hsl(208, 100%, 80%)`).

Čisti sivi tekst izgleda čudno na obojenoj pozadini. Umjesto toga, koristi hue pozadine (npr. ako je pozadina `hsl(192, 56%, 56%)`, boja teksta neka bude `hsl(192, 17%, 99%)`).

Ako su ikone deblje od teksta pokraj njih, koristi boju svijetliju od teksta za neaktivni state.

Obojena linija na vrhu stranice debljine 4-6px dobro izgleda. Isto vrijedi za modale i panele.

Umjesto razdvajanja naslova panela od ostatka linijom, koristi tamniju nijansu za ostatak.

## Animated gradient buttons

Na hover postavi background, npr:
`background: linear-gradient(to right, #7258FF, #B658FF, #FF34A1, #FF5858, #FCCF58, #E5FC58, #8DE086, #86E0B5, #69DCE3, #698BE3, #7258FF)`

`background-size: 1000% auto` da se raširi.

Zatim dodaj animaciju:
`0% { background-position-x: 0% }` i `100% { background-position-x: 100% }`.

## Shadows

Bijelom tekstu na svijetloj pozadini dodaj lagani shadow (`text-shadow: 0 1px 2px rgba(0, 0, 0, 0.2)`) za bolju čitljivost i da iskoči.

Za hover na button podigni za 1px (čini se kao da iskoči) i povećaj drop shadow spread.

Box shadow izgleda prirodnije s vertikalnim offsetom i manjim spreadom (umjesto `box-shadow: 0px 0px 8px` bolje je `box-shadow: 0px 2px 4px`).

# Literatura

* https://twitter.com/i/events/880688233641848832

