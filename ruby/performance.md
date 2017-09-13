# Performance & Debugging

## Measuring Ruby

Uvijek napravi benchmark skriptu! Nema optimizacije bez mjerenja.

`rack-mini-profiler` u browseru daje breakdown vremena potrošenog na serveru, a usto je i production safe. Koristi `?pp=help` za detaljnije podatke, npr. flamegraphovi.

`memory_profiler` (uključen u `rack-mini-profiler`) vraća alociranu memoriju i zadržane (retained) objekte.

`rbtrace -p <pid>` zakači se na proces i prati koje metode se pozivaju, isto je production safe. `--firehose` prikuplja sve.

`ab` za mjerenje prosječnog responsea. Npr. `ab -t 10 -c 10 http://localhost:3000/`. Korisne opcije:
* `-c` definira koliko će se istovremeno requestova raditi (namjesti po vlastitom produkcijskom serveru),
* `-t` koliko će sekunda to trajati.

## Primjeri Benchmarka

https://rubybench.org/ za Ruby/Rails/Bundler
https://github.com/discourse/discourse/blob/master/script/bench.rb za Discourse

Napravi svoj benchmark da možeš vidjeti kako promjene u kodu utječu na performance!

## Debugging

`bundle open gem_name` u editoru otvara verziju gema iz Gemfilea. `EDITOR=atom` za definiranje editora.

`gem pristine gem_name` ako si prčkao po gemu i dodavao kod, ovo ga resetira.

`obj.method(:foo).parameters` lista argumenata koje metoda prima.
`obj.method(:foo).source_location` file i linija gdje je metoda definirana.
`obj.method(:foo).super_method.source_location` file i linija gdje je `super` definiran.
`caller` unutar metode, vraća backtrace poziva.

`ruby -d foo.rb` ispisuje sve exceptione, čak i one rescuane.
`bundle exec ruby -d script/rails runner foo.rb` za Rails.

`trap(:INFO) { Thread.list.each {|t| p t, t.backtrace }}` pomaže pri otkrivanju deadlocka. Na CTRL+T (`INFO`) svi threadovi ispisuju gdje se trenutno nalaze.

## Ruby Deoptimization

https://github.com/ruby/ruby/pull/1419

Ruby je dijelom spor jer omogućuje overload operatora. `1 + 2` ne mora vraćati `3`. U ovom pull requestu, predlaže se poboljšanje: Ruby će pretpostaviti da je rezultat `3`, pa će u većini slučajeva raditi brže, a za posebne slučajeve će imati minimalni overhead. Time se određene metode znatno ubrzavaju.

## Optimizing App Boot Time

https://engineering.shopify.com/blogs/engineering/bootsnap-optimizing-ruby-app-boot-time

Monolitne Rails aplikacije često imaju startup time duži od pola minute. Postoji nekoliko načina da se ubrza, izdvojenih u `bootsnap` gem.

Pri izvršavanju `require 'foo'` Ruby slijedno prolazi kroz sve elemente `$LOAD_PATH` arraya i pokušava naći gdje se file `foo.rb` nalazi. Umjesto toga, optimizacija unaprijed cachira sve fileove u `$LOAD_PATH` direktorijima, pa se `require` obavlja u `O(1)`. Isto vrijedi i za Railsov `autoload_paths`.

Compile Ruby koda u bytecode kojeg izvršava Ruby VM je skupa operacija. Optimizacija cachira rezultate compilea .rb fileova. Slično cachira i YAML fileove koji se brže učitavaju iz MessagePack ili Marshall formata.

## Kako su ubrzali development env s jako puno asseta

https://github.com/discourse/discourse/blob/master/lib/middleware/turbo_dev.rb

Koristi ovaj middleware kao prvi u rack stacku, da prekine nepotrebno pozivanje svih ostalih middlewarea (učitavanje sessiona, dohvaćanje konekcije itd.)

