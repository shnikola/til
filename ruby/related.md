# Ruby related stuff

## MRuby
http://lucaguidi.com/2015/12/09/25000-requests-per-second-for-rack-json-api-with-mruby.html
Postoji MRuby (minimal ruby) koji se može embeddat gdjegod podržava C.
Postoji i neki jako brzi novi server H2O.
Zajedno ostvaruju 25.000+ req/s.


## How does bundler work, anyway?
https://www.youtube.com/watch?v=4DqzaqeeMgY
Problem s gem odlučivanjem koju verziju gema koristiti. Rješenje: ne radi tu odluku u runtimeu, nego pri instalaciji.


## Ruby i HTTP/2
HTTP/2 nije kompatibilan s Rakeom: request/response streaming bi trebao biti default, komunikacija dvosmjerna (server ili klijent mogu gurnuti više stvari bez odgovora)
HTTP/2 je inače nešto kao websocketsi, ali za websocketse treba implementirati dosta stvari koje http nudi (redirection, REST, caching)
Prednosti:
- headeri se compressaju (uključujući cookije), uštedi se do 80% - **koristi HTTP/2 compatible proxy (nginx, h2o)**
- mogućnost slanja više responsea u istoj konekciji, domain sharding više nije potreban - **koristi HTTP/2 compatible CDNove**
- jedna konekcija znači jedan TLS handshake
- klijenti mogu izraziti preferenciju koji request da se prvi ispuni (npr. CSS i JS prije imagea)
- pošto je skidanje jednog velikog filea i puno malih sada isto, gubi se potreba za jednim velikim JS i CSS fileom i spriteovima


## RubyConf 2014 - Isomorphic App Development with Ruby and Volt by Ryan Stout
https://www.youtube.com/watch?v=7i6AL7Walc4
Volt - Isomorphic framework (isti kod na serveru i klijentu) - Ruby se kompajlira u JS koristeći **Opal**


## mime-types optimizacija
https://github.com/mime-types/ruby-mime-types/issues/94
mime-types je držao sve tipove u jednom velikom jsonu. Od tog hasha su se onda stvarali `Mime::Type` objekti koji su se čuvali u memoriji. Sve skupa gem je zauzimao 15 MB memorije, što je poprilično.

Umjesto jsona, tipove su podijelili u fileove, po jedan file za svaki atribut, po jedan red za svaki tip. Pošto velika većina aplikacija ne treba sve tipove, `Mime::Type` objekti se lazy loadaju. To je malo usporilo loadanje, ali 10x smanjilo korištenu memoriju.

## Micromachine
https://github.com/soveran/micromachine
Mini state-machine library. Probao sam ga sam implementirati sam - i dosta dobro sam ga napisao. Bravo ja.
