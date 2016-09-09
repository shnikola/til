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


## Budućnost Rubyja uz Rails
http://solnic.eu/2016/05/22/my-time-with-rails-is-up.html
http://www.akitaonrails.com/2016/05/23/rails-has-won-the-elephant-in-the-room
Čovjeku je dosadio Rails i ne slaže se njegovom arhitekturom, pa ga je odlučio više ne koristiti.
Problem je što Rails drži monopol u Ruby svijetu. Od svih gemova se očekuje da podržavaju Rails,
makar ih to koštalo jednostavnosti. 99% poslova koji se nude vezani su za Rails. Postavlja pitanje
što će biti s Rubijem jednom kad dođe framework koji nudi sve što Rails nudi, a u boljem je jeziku?
(Osobno mislim da ljudi ne biraju framework po tome koliko je akademski dobar, nego po tome koliko materijala
za učenje imaju.)

Stvar je da je Rails dobar za ono za što je namijenjen: Basecamp. A 90% web aplikacija su Basecamp-like CRUD
aplikacije. Ako biraš alat, izaberi onaj koji rješava stvarni problem, i koji su izgradili ljudi koji o njemu
ovise. (Npr. Angular je dobar, ali Googlu ne treba, i zato nisu ni trepnuli kad su odlučili dovesti nekompatibilni
Angular 2). Da, arhitektura ActiveRecorda će u 10% naprednih slučajeva biti problematična, ali ActiveRecord je i
dalje mnogo bolji od prosjeka. I svaki novi framework će se naći u Railsovoj današnjoj situaciji za 10 godina.

**Zaključak:** Rails je pobjedio zbog svoje jednostavnosti za početnike, posjedovanju točnih smjernica za iskusnije, te
dopuštanju donekle prihvatljive fleksibilnosti naprednim developerima. Hoće li moći izdržati još 10 godina
suočen s mnogim malim alternativama koji počinju ispočetka? Vrijeme će pokazati.
