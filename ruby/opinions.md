# Opinions

## O Railsu 5 i relevantnosti Railsa u današnje vrijeme

https://www.amberbit.com/blog/2015/4/22/why-rails-5-turbolinks-3-action-cable-matter-and-why-dhh-was-right-all-along/

Osnovni problem je što backend developeri ne žele raditi u JS-u, pa se smišljaju ActionCable, js.erb, Turbolinks i sl. U isto vrijeme frontend developeri ne žele raditi u Rubyju (asset pipeline i sprocketsi) kad mogu raditi u jeziku koji poznaju (node.js, gulp).

Railsu bi bilo najbolje da se ostavi forsiranja klijentske strane i fokusira na APIje koje će klijent trošiti.

A ti kao developer se nemoj fokusirati na framework, nego na jezik. Razvijaj gemove za Ruby, a ne za Rails. Ne koristi jedan čekić za sve.

## Smrt dependencijima

http://www.mikeperham.com/2016/02/09/kill-your-dependencies

Gemovi vuku druge gemove, i prije nego se okreneš imaš sranja koja ne koristiš a koriste ti masu memorije.

Ako ti gem ne treba, obriši ga. Ako njegovu funkcionalnost možeš trivijalno implementirati, učini to.

Ako si autor gema, uvijek za default koristi **stdlib**. `json` je od 1.9 u stdlibu. `Net::HTTP` bi svaki gem trebao defaultno koristiti, a omogućiti developeru da zamijeni Faradayem ili httpartijem.

Simplify, simplify, simplify.

## Budućnost Rubyja uz Rails

http://solnic.eu/2016/05/22/my-time-with-rails-is-up.html
http://www.akitaonrails.com/2016/05/23/rails-has-won-the-elephant-in-the-room

Čovjeku je dosadio Rails i ne slaže se njegovom arhitekturom, pa ga je odlučio više ne koristiti.

Problem je što Rails drži monopol u Ruby svijetu. Od svih gemova se očekuje da podržavaju Rails, makar ih to koštalo jednostavnosti. 99% poslova koji se nude vezani su za Rails. Postavlja pitanje što će biti s Rubyjem jednom kad dođe framework koji nudi sve što Rails nudi, a u boljem je jeziku?

(Osobno mislim da ljudi ne biraju framework po tome koliko je akademski dobar, nego po tome koliko materijala za učenje imaju.)

Stvar je da je Rails dobar za ono za što je namijenjen: Basecamp. A 90% web aplikacija su Basecamp-like CRUD aplikacije. Ako biraš alat, izaberi onaj koji rješava stvarni problem, i koji su izgradili ljudi koji o njemu ovise. (Npr. Angular je dobar, ali Googlu ne treba, i zato nisu ni trepnuli kad su odlučili dovesti nekompatibilni Angular 2).

Da, arhitektura ActiveRecorda će u 10% naprednih slučajeva biti problematična, ali ActiveRecord je i dalje mnogo bolji od prosjeka. I svaki novi framework će se naći u Railsovoj današnjoj situaciji za 10 godina.

Rails je pobjedio zbog svoje jednostavnosti za početnike, posjedovanju točnih smjernica za iskusnije, te dopuštanju donekle prihvatljive fleksibilnosti naprednim developerima. Hoće li moći izdržati još 10 godina
suočen s mnogim malim alternativama koji počinju ispočetka? Vrijeme će pokazati.

## Ruby Community Mindset

http://gilesbowkett.blogspot.hr/2016/10/let-asset-pipeline-die.html

Giles Bowket je prije mjesec dana učio početi japanski, pa ima potrebu napisati golemi esej o svom novootkrivenom shvaćanju Ruby kulture. Ako nije dovoljno jasno, živcira me taj čovjek.

Ali osnovna ideja iza teksta vrijedi: nije dobro okupljati zajednicu oko mržnje prema nečemu, bio to PHP, Java ili Javascript. Ruby zajednica oduvijek se ističe po inkluzivnosti, i zato bi trebala omogućiti koegzistenciju različitih stavova, paradigmi i kultura.

Konkretan primjer koji uzima je Sprockets, koji je u groznom stanju po pitanju koda i održavanja. Rails bi umjesto toga trebao omogućiti jednostavno korištenje naprednijih rješenja poput Webpacka, koje razvijaju Javascript developeri koji su ionako veći eskperti u frontendu.

