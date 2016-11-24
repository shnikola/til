# Random

## Continuous Integration
https://circleci.com/
Kad pushash kod, on automatski povuče to na svoj server, i pokrene sve testove.
Čak možeš podesiti da ga deploya na Heroku ako su svi testovi prošli. Jer si lijena guzica.


## Internationalized domain name
DNS podržava ne-ASCII znakove, ali protokoli koje email i browseri koriste često podržavaju samo ASCII.
IDN prevodi unicode u ASCII domene koristeći `Punycode`: `Bücher.ch > xn--bcher-kva.ch`

Ovo se može iskoristiti za *IDN homograph attack*: spoofanje domene koristeći npr. ćirilićna slova koja izgledaju isto kao i latinična. Zato browseri uglavno prikazaju Punycode prikaz upitnih slova.


## Time, technology and leaping seconds
https://googleblog.blogspot.hr/2011/09/time-technology-and-leaping-seconds.html
Zbog raznih fluktuacija u zemljinoj rotaciji, i najprecizniji satovi moraju se povremeno prilagoditi da se poklapaju sa "solarnim" vremenom. Takva prilagođavanja, tzv. *leap seconds* dogodila su se 24 puta od 1972.

U Googlu su to riješili tako da tijekom dana u kojem se *leap second* treba dodati, NTP server postupno dodaje po nekoliko milisekunda, pa se svi serveri pomalo prilagode bez da treba svaki posebno mijenjati.
