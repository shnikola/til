# Ruby

## Enumerable i Enumerator
`Enumerable` je module. Includaj ga ako je tvoja klasa kolekcija.
  * u klasi definiraj `each` metodu, i automatski dobijaš i `select`, `map` itd.
  * objekti unutar kolekcije moraju imati definiran `<=>`

`Enumerator` je objekt koji se koristi za interno (`each`) i eksterno (`next`) iteriranje.
  * `Enumerator.new do` koristi se za generiranje beskonačnih streamova.
  * `enum_for` koristi unutar `each` metode: `return enum_for(__method__) unless block_given?`. Na taj način možeš chainati enumeratore, npr. `each.with_index`


## Modules
Svaki `module` je instanca klase `Module`. Imaju nekoliko primjena:
* *Namespacing*: ako razvijaš library, sve klase stavi unutar modula da bi izbjegao konflikte s klasama istog imena.
* *Method Sharing*: s `include`, `extend` ili `prepend` bilo koja klasa dobija metode iz modula. Korisno ako imaš modul s dobro definiranim public interfaceom (npr `Enumerable`).
* *Global methods*: stateless module s metodama kao `FileUtils`. Koristi `module_function` da sve instance metode kopiraš kao class i pretvoriš u private.

Nemoj korisiti module da razbiješ veliku klasu u više fileova s `include` - samo ćeš si otežati kad ne budeš znao odakle dolazi neka metoda.


## Nested modules
http://blog.honeybadger.io/avoid-these-traps-when-nesting-ruby-modules
Kod nestanih modula, pristup konstantama ne određuje parent-child odnos objekata, nego struktura koda (Module.nesting)

```ruby
module A
  C = 1
  module B
    Module.nesting # => [A::B, A], vidi C
  end
end
```

Ali zato:
```ruby
module A::B
  Module.nesting # => [A::B], ne vidi C
end
```

Međutim, Rails ima autoload system koji pomoću `Module.const_missing` detektira kad file s konstantom nije učitan, pa će ovo u Railsu raditi. Zaključak: izbjegavaj `A::B` definiciju modula.


## Pozivanje vanjskih procesa
``ls`` ili `%x{ls}` forka novi proces i izvršava ga.
  * vraća `stdout` novog procesa kao string. Status možeš dohvatiti iz `$?.success?` (bleh)
  * blokira dok se proces ne završi.
  * exceptioni se prosljeđuju masteru.

`system("ls")` isto forka novi proces i izvršava ga.
  * vraća `true` ili `false` prema exit statusu. Output printa na `stdout`.
  * blokira dok se proces ne završi.
  * exceptioni *ne* dolaze do mastera.

`exec("ls")` prekida Ruby proces s `exit` i zamjenjuje ga danom naredbom. Ništa se ne vraća jer više nismo u Rubyju.
`Process.spawn("ls")` pokreće proces u subshellu i odmah vraća PID. Ne blokira.

`Open3` daje detaljniji pristup:
  * `Open3.capture2('ls', options)` vraća `stdout` i `status`.
  * `Open3.capture3('ls', options)` vraća `stdout`, `stderra` i `status`.
  * `Open3.pipeline('sort', 'uniq -c', in: 'file.txt', out: 'count.txt')` za pipeline.
  * `:stdin_data` opciju koristi za slanje stdina.


## Regexi
* `%r` za custom separator, npr. `%r{regex}` ako ne želiš escapati slasheve.
* `/regex/x` ignorira whitespace i komentare. Super ako želiš kompleksni regex razlomiti u više linija.
* `(?:)` za grupiranje koje ne želiš captureati.
* `(?<ime>)` za named captures, npr. `/(?<last>\w+), (?<first>\w+)/ =~ "Santic, Nikola"` stvara varijable `last` i `first`.


## Postoji li parent method
`if defined? super`


## URI.join ne radi kako misliš da radi
`URI.join('http://example.com', 'plans', 'change')` neće vratiti `http://example.com/plans/change` nego `http://example.com/change`!
Za joinanje više dijelova URL-a koristi `File.join` (osim što neće raditi na Windowsima)


## method_missing
Kad definiraš `method_missing`, uvijek definiraj i `respond_to_missing?` jer će on, osim `respond_to?`,
riješiti da radi i dohvaćanje metode s `method`.


## ruby -n
`-n` radi loop nad svakom linijom STDIN-a i stavlja ga u $_
`ls | ruby -ne 'puts $_'`


## &:protected_metoda
`&:some_protected_method` neće raditi jer se poziv metode šalje iz Symbola, a ne iz trenutne klase. Tja!


## Lazy loadani hash
```ruby
result = Hash.new do |hash, key|
  hash[key] = some_expensive_operation(key)
end
```


## Optimizacije
`reverse_each` je brži od `reverse.each`
`str.sub` je brži od `str.gsub` (ako treba samo prva izmjena)
`str.tr` je brži od `str.gsub` (ako se mijenja jedno slovo)

`@user ||= User.find(id) if id.present?` je loše, jer će izvoditi `if` čak i ako je `@user` postavljen.
`@user ||= (User.find(id) if id.present?)` je malo ružnije, ali puno bolje. Pogotovo za metode koje se često pozivaju.


## Novo u 2.3
`user&.admin?` je kao `user && user.admin?`. U principu kao `try`, ali `try` ne baca exception ako metoda ne postoji.
`{a: {b: 1}}.dig(:a, :b) # => 1` - nested pristup bez exceptiona
`['a', 'b', 'c'].grep_v(/a/) # => ['b', 'c']` - vraća one koji se ne slažu regexom


## Novo u 2.4
`Regexp#match?` - vraća boolean i 3 puta je brži od `match` i `=~`
`/(?<first_name>) (?<last_name>\w+)/.match("Nikola Santic").named_captures` - vraća hash named capturea
`/(\d+)-(\d+)-(\d+)$/.match('2016-07-18').values_at(1, 3)` - vraća vrijednosti za 1. i 3. capture
`[1, 2, 3].sum` - od sada i u Rubyiju
`Dir.empty?("dir_name")`, `File.empty?("file_name")`
`123.digits` - vraća znamenke (`[3, 2, 1]`), `digits(16)` radi u drugim bazama
`Fixnum`, `Bignum` i `Integer` su svi sada `Integer` (implementacija je skrivena)
`round`, `ceil`, `floor` primaju precision argument
Unicode characteri se downcaseaju kako treba.
`String.new` prima `:capacity` za memoriju koju treba predalocirati.
`Thread.report_on_exception = true` ne prekida proces kao `abort_on_exception`, ali i dalje ispiše error.
