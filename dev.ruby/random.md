# Ruby related stuff

## MRuby

http://lucaguidi.com/2015/12/09/25000-requests-per-second-for-rack-json-api-with-mruby.html

Postoji MRuby (minimal ruby) koji se može embeddat gdjegod podržava C.
Postoji i neki jako brzi novi server H2O.
Zajedno ostvaruju 25.000+ req/s.

## Traveling Ruby

http://phusion.github.io/traveling-ruby/

Traveling Ruby omogućava da zapakiraš Ruby app kako bi bila executable na Windowsima, Linuxu i OSXu.

## Popravljeni open-uri

https://twin.github.io/improving-open-uri

`open-uri` je uglavnom super, ali gem `down` je lightweight wrapper oko njega koji popravlja neke stvari:
* zaštita od remote code execution kad url dolazi od usera (npr. `open("| ls")`).
* dodaje ekstenziju na `Tempfile` koji se stvori pri downloadu.
* dodaje opcije ograničenja broja redirectova i maximum filesizea.
* dodaje defaultni User-Agent da ga aplikacije ne odbijaju.

## Rails ActionCable vs Phoenix Channels

https://dockyard.com/blog/2016/08/09/phoenix-channels-vs-rails-action-cable

ActionCable je nespreman za ozbiljnije zahtjeve u produkciji, već sa 7 kanala s po 200 klijenata počinje ozbiljno kasniti.

Phoenix s druge strane bez problema handlea 275 kanala s 55,000 usera.
Ako radiš aplikaciju s pravim real time zahtjevima, odaberi Phoenix.

## Rails with Service Workers

https://rossta.net/blog/service-worker-on-rails.html

Asset pipeline stvara nekoliko problema pri korištenju service workera:
* svi asseti se drže u `/assets/*`, pa bi service mogao pristupati requestovima samo unutar tog scopea. Zato treba dodati custom middleware koji će servirati `serviceWorker.js` s patha kojeg je potrebno.
* fingerprinting fileova i dugi `Cache-Control` period onemogućava redovno updateanje service workera ugrađenu o browsere. Treba postaviti `Cache-Control: private, max-age=0, no-cache`.

`serviceworker-rails` gem sredi sve te stvari za tebe.
