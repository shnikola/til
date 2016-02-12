# Opinions

## O Railsu 5 i relevantnosti Railsa u današnje vrijeme
https://www.amberbit.com/blog/2015/4/22/why-rails-5-turbolinks-3-action-cable-matter-and-why-dhh-was-right-all-along/

Najveći problem je što frontend developeri ne žele raditi u rubiju (asset pipeline i sprocketsi) kad mogu raditi u jeziku koji poznaju (node.js, gulp). Railsu bi bilo najbolje da se ostavi forsiranja klijentske strane (js.erb, turbolinks) i fokusira se na APIje koje će klijent trošiti.

**Zaključak:** Ne treba se fokusirati na framework nego na jezik - koristiti gemove za ruby a ne za Rails; proučiti ostale jezike poput Go-a, Clojura i Rusta.


## Smrt dependencijima
http://www.mikeperham.com/2016/02/09/kill-your-dependencies

Gemovi vuku druge gemove, i prije nego se okreneš imaš sranja koja ne koristiš a koriste ti masu memorije.

Ako ti gem ne treba, briši ga. Ako njegovu funkcionalnost možeš trivijalno implementirati, učini to.

Ako si autor gema, uvijek za default koristi **stdlib**. `json` je od 1.9 u stdlibu. `Net::HTTP` bi svaki gem trebao defaultno koristiti, a omogućiti developeru da zamijeni Faradayem ili httpartijem.

Simplify, simplify, simplify.
