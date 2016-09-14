# Ruby related stuff

## MRuby
http://lucaguidi.com/2015/12/09/25000-requests-per-second-for-rack-json-api-with-mruby.html
Postoji MRuby (minimal ruby) koji se može embeddat gdjegod podržava C.
Postoji i neki jako brzi novi server H2O.
Zajedno ostvaruju 25.000+ req/s.


## Traveling Ruby
http://phusion.github.io/traveling-ruby/
Traveling Ruby omogućava da zapakiraš Ruby app kako bi bila executable na Windowsima, Linuxu i OSXu.


## How does bundler work, anyway?
https://www.youtube.com/watch?v=4DqzaqeeMgY
Problem s gem odlučivanjem koju verziju gema koristiti. Rješenje: ne radi tu odluku u runtimeu, nego pri instalaciji.


## Ruby i HTTP/2
HTTP/2 nije kompatibilan s Rackom: request/response streaming bi trebao biti default, komunikacija dvosmjerna (server ili klijent mogu gurnuti više stvari bez odgovora)
HTTP/2 je inače nešto kao websocketsi, ali za websocketse treba implementirati dosta stvari koje http nudi (redirection, REST, caching)
Prednosti:
- headeri se compressaju (uključujući cookije), uštedi se do 80% - **koristi HTTP/2 compatible proxy (nginx, h2o)**
- mogućnost slanja više responsea u istoj konekciji, domain sharding više nije potreban - **koristi HTTP/2 compatible CDNove**
- jedna konekcija znači jedan TLS handshake
- klijenti mogu izraziti preferenciju koji request da se prvi ispuni (npr. CSS i JS prije imagea)
- pošto je skidanje jednog velikog filea i puno malih sada isto, gubi se potreba za jednim velikim JS i CSS fileom i spriteovima


## Rack::Events
https://github.com/rack/rack/blob/master/lib/rack/events.rb
Rack je malo šugav jer svaki middleware mora kopati po čitavom "rack stacku" da bi napravio nešto.
Ovo je prijedlog event-based pristupa gdje se middleware kojeg ne zanima response body zakači na evente poput `on_start`, `on_finish` ili `on_error`.


## Micromachine
https://github.com/soveran/micromachine
Mini state-machine library. Probao sam ga sam implementirati sam - i dosta dobro sam ga napisao. Bravo ja!


## File Uploads with Shrine
https://twin.github.io/shrine-2-0-released
Izgleda jako kul, isprobaj!


## Popravljeni open-uri
https://twin.github.io/improving-open-uri
`open-uri` je uglavnom super, ali gem `down` je lightweight wrapper oko njega koji popravlja neke stvari:
  * zaštita od remote code execution kad url dolazi od usera (npr. `open("| ls")`).
  * dodaje ekstenziju na `Tempfile` koji se stvori pri downloadu.
  * dodaje opcije ograničenja broja redirectova i maximum filesizea.
  * dodaje defaultni User-Agent da ga aplikacije ne odbijaju.


## Partial Downloads with Enumerators and Fibers
https://twin.github.io/partial-downloads-with-enumerators-and-fibers
Uploader `shrine` ima feature da se file direktno uploada na `S3`, bez da ide na server. Ali uploadanom fileu uvijek treba provjeriti je li *MIME type* onaj kojeg očekujemo. Pritom se ne možemo se pouzdati u ekstenziju ili `Content-Type`, jer dolaze od usera. MIME type se pouzdano može odrediti samo iz *contenta*, koristeći `MimeMagic` gem ili Unix naredbu `file`.

Ako je file remotely uploaded, ne želimo ga opet cijelog skidati na server za validaciju. Želimo stvoriti interface koji će omogućiti partial download *po potrebi* (znači, ne da se file cijeli skine odjednom, nego da skida komad po komad).

`net/http` ima `response.read_body` za chunked download, ali mora se nalaziti unutar `http.request_get` bloka, koji zatvara konekciju pri izlasku. Zato se HTTP request poziva unutar `Fiber`a, a u `http.request_get` blok stavlja se `Fiber.yield` koji pauzira izvršavanje tog koda dok se opet ne pozove s `fiber.resume`. Brilliant!

Inače, gem `HTTP.rb` je puno bolja implentacija http clienta i ima ovo out-of-the box, ali za library je uvijek bolje da koristi stdlib.