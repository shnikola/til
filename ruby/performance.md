# Performance & Debugging

## Measuring Ruby
https://www.youtube.com/watch?v=LWyEWUD-ztQ
**Bitno**: Uvijek napravi benchmark skriptu! Nema optimizacije bez mjerenja.
`rack-mini-profiler` u browseru daje breakdown vremena potrošenog na serveru. *Production safe.*
  * `?pp=help` za detaljnije podatke, npr. flamegraphovi.
`memory_profiler` (uključen u `rack-mini-profiler`) vraća alociranu memoriju i zadržane (retained) objekte.
`rbtrace -p <pid>` zakači se na proces i prati koje metode se pozivaju. *Production safe.*
  * `--firehose` prikuplja sve.
`ab` za mjerenje prosječnog responsea. Npr. `ab -t 10 -c 10 http://localhost:3000/`
  * `-c` definira koliko će se istovremeno requestova raditi (namjesti po vlastitom produkcijskom serveru),
  * `-t` koliko će sekunda to trajati.


## Primjeri Benchmarka
https://rubybench.org/ za Ruby/Rails/Bundler
https://github.com/discourse/discourse/blob/master/script/bench.rb za Discourse
Napravi svoj benchmark da možeš vidjeti kako promjene u kodu utječu na performance!


## Debugging
http://www.schneems.com/2016/01/25/ruby-debugging-magic-cheat-sheet.html
https://tenderlovemaking.com/2016/02/05/i-am-a-puts-debuggerer.html
  * `bundle open gem_name` - u editoru otvara verziju gema iz Gemfilea. `EDITOR=atom` za definiranje editora.
  * `gem pristine gem_name` - ako si prčkao po gemu i dodavao kod, ovo ga resetira.
  * `obj.method(:foo).source_location` - file i linija gdje je metoda definirana
  * `obj.method(:foo).super_method.source_location` - file i linija gdje je **super** definiran
  * `obj.method(:foo).parameters` - lista argumenata koje metoda prima
  * `caller` - unutar metode, vraća backtrace poziva
  * `ruby -d foo.rb` - ispisuje sve exceptione, čak i one rescuane. `bundle exec ruby -d script/rails runner foo.rb` za Rails.
  * `ObjectSpace.trace_object_allocations_start`, pa onda `ObjectSpace.allocation_sourcefile(obj)` i `ObjectSpace.allocation_sourceline(obj)` - gdje je objekt alociran
  * `trap(:INFO) { Thread.list.each {|t| p t, t.backtrace }}` - za otkrivanje deadlocka, na CTRL+T (`INFO`) svi threadovi ispisuju gdje se trenutno nalaze


## Ruby GC
Svi objekti se spremaju na heap kojeg kontrolira MRI. Heap se sastoji od pageva.
Svaki page je velik `16KB`, i sadrži mjesta za oko `408` objekata.
Svaki objekt isprva zauzima `40` byteova. Kada se objekt stvara, MRI traži slobodno mjesto na heapu. Ako ga nema, dodaje se novi page.

Ruby `2.1` uveo je *generacijski GC*. On radi u dvije faze: *minor GC* čisti nedavno stvorene objekte (jer većina objekata traje kratko), a *major GC* prolazi kroz *sve* objekte, ali se poziva rjeđe.
Generacijski GC i dalje koristi stop-the-world implementaciju - kad se GC pokrene, izvršavanje programi se zaustavlja.
To može trajati i do `100ms`, što je prilično loše ako se dogodi tijekom handlanja requesta.

Ruby `2.2` uveo je *inkrementalni GC* koji dijeli GC u sitne procese. Umjesto jedne duge pauze, dogodit će se više kratkih pauza. Zahvaljujući tome performance je mnogo konzistentniji.


## Ruby Deoptimization
https://github.com/ruby/ruby/pull/1419
Ruby je dijelom spor jer uopće ne pokušava optimizirati svoje izvršavanje.
Problem je u njegovoj dinamičnosti: `1 + 2` ne mora vraćati `3`. U ovom pull requestu, predlaže se poboljšanje: Ruby će pretpostaviti da je rezultat `3`, pa će u većini slučajeva raditi brže, a za posebne slučajeve će imati minimalni overhead. Time se određene metode znatno ubrzavaju.


## Memory Leaks
Objekti se mogu podijeliti u 3 grupe:
  * Statics: framework, svi gemovi, i aplikacijski kod. Jednom kad se učitaju ne oslobađaju se.
  * Slow Dynamics: objekti koji dugo žive, npr. cache prepared SQL izraza u ActiveRecordu. Sporo raste, ali do zadanog limita.
  * Fast Dynamics: objekti kreirani prilikom parsiranja requesta i generiranja responsa. Kad je response spreman, ovi objekti se mogu osloboditi.
Minimalna veličina heapa je zbroj prve i druge grupe. Ako se 3. grupa ne oslobodi, imamo leak i heap konstatno raste.


## Traženje memory leaka
http://www.be9.io/2015/09/21/memory-leak/
https://samsaffron.com/archive/2015/03/31/debugging-memory-leaks-in-ruby
Ako je zauzeće memorije na Heroku stalno uzlazno, postoji leak kojeg GC ne uspjeva počistiti.

Dokaz da leak postoji:
  * prebaci produkcijske podatke u lokalni server za testiranje.
  * `heroku logs -t | tee log/production.log` dohvaća stvarne requeste.
  * `siege -v -c 15 --log=/tmp/siege.log -f urls.txt` šalje po 15 paralelnih poziva simulirajući promet.
  * `ps -eo pid,rss | grep #{Process.pid}` dohvaća *Resident Set Size* (koliko memorije zauzima u RAMu) za trenutni proces.
  * ako se *RSS* konstantno povećava, dokazano postoji leak.

Je li leak u Ruby kodu:
  * koristi `gc_tracer` gem koji se ubaci kao Rack Middleware i ispisuje milijun podataka u svoj log.
  * nas zanimaju *RSS* i broj starih objekata (koje će samo major GC gledati) kroz vrijeme.
  * plotamo ih s `gnuplot` i ako je da broj starih objekata stabilan, ali RSS raste - to znači da leak nije u Ruby kodu.
  * ako broj starih objekata raste, treba pronaći gdje se ti objekti alociraju.
  * `ObjectSpace.trace_object_allocations_start` bilježi alokacije - ovo će dosta usporiti proces, pa ne koristi u produkciji.
  * prati RSS, i nakon što je memorija očito leakala, pozovi `ObjectSpace.dump_all(output: file)`
  * dump vraća json s objektima sortiranim po GC generacijama. Tu možeš pronaći koji kod alocira koliko objekata.

Ako leak nije u Ruby kodu, mora biti u C kodu. Detekcija je malo naporna:
  * instaliraj Ruby s `jemalloc` profilingom, i pokreni ga s `MALLOC_CONF=narenas:1,stats_print:true`.
  * ... i onda po nekim čudima kopaš dok na sreću ne skužiš tko alocira memorije.
  * hint: vjerojatno je neki gem.


## Kako su ubrzali development env s jako puno asseta
https://github.com/discourse/discourse/blob/master/lib/middleware/turbo_dev.rb
Koristi ovaj middleware kao prvi u rack stacku, da prekine nepotrebno pozivanje svih ostalih middlewarea (učitavanje sessiona, dohvaćanje konekcije itd.)


## Kako su optimizirali mime-types
https://github.com/mime-types/ruby-mime-types/issues/94
`mime-types` je držao sve tipove u jednom velikom jsonu. Od tog hasha su se onda stvarali `Mime::Type` objekti koji su se čuvali u memoriji. Sve skupa gem je zauzimao 15 MB memorije, što je poprilično.

Umjesto jsona, tipove su podijelili u fileove, po jedan file za svaki atribut, po jedan red za svaki tip. Pošto velika većina aplikacija ne treba sve tipove, `Mime::Type` objekti se lazy loadaju. To je malo usporilo loadanje, ali 10x smanjilo korištenu memoriju.


## Moja iskustva
Readcube dashboard reports trošili su previše memorije. S `memory_profiler` izmjerio sam da najviše alociraju `number_to_currency` iz ActiveSupporta koji sam includao, `Carmen` koji defaultno učitava sve zemlje na svim jezicima, i `CSV.generate_line` koji svaki put iznova radi `CSV.new`. Svaki pojedinačno nije puno trošio, ali kad se svaki zove 200.000 puta, svaka pretjerana alokacija se osjeti.
