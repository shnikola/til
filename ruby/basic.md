# Ruby

## Enumerable

`Enumerable` je module. Includaj ga ako je tvoja klasa kolekcija.
U klasi definiraj `each` metodu, i automatski dobijaš i `select`, `map` itd. Objekti unutar kolekcije moraju imati definiran `<=>`

## Enumerator

`Enumerator` je objekt koji možeš iterirati, standardno (s `each`) ili ručno (s `next`).

`enum_for(:some_method)` (ili `to_enum(:some_method)`) vraća enumerator za metodu koja radi iteraciju, bez da dohvaćaš sve podatke.

Za metode koje primaju block: `return enum_for(__method__) unless block_given?`

`Enumerator.new do` koristi se za generiranje beskonačnih streamova.

## Modules

Svaki `module` je instanca klase `Module`. Imaju nekoliko primjena:
* *Namespacing*: ako razvijaš library, sve klase stavi unutar modula da bi izbjegao konflikte s klasama istog imena.
* *Method Sharing*: s `include`, `extend` ili `prepend` bilo koja klasa dobija metode iz modula. Korisno ako imaš modul s dobro definiranim public interfaceom (npr `Enumerable`).
* *Global methods*: stateless module s metodama kao `FileUtils`. Koristi `module_function` da sve instance metode kopiraš kao class i pretvoriš u private.

Nemoj korisiti module da razbiješ veliku klasu u više fileova s `include` - samo ćeš si otežati kad ne budeš znao odakle dolazi neka metoda.

Kod nestanja modula koristi puni zapis (`module A; module B`) umjesto skraćenog zapisa (`module A::B`).

U prvom slučaju, `nesting` unutrašnjeg modula je `[A::B, A]`, pa možeš pristupiti konstantama definiranim u `A.`

U skraćenom slučaju, `nesting` je samo `[A::B]`, pa nećeš vidjeti konstante iz `A`.

Rails ima autoload system pa će oba slučaja raditi, ali svejedno izbjegavaj skraćeni oblik.

## Lambdas, procs and blocks (TODO)

`obj.call(params)` metodu bilo kojeg objekta možeš pozvati i s `obj.(params)`.
`arity`
`curry`

## Učitavanje koda

Učitavanje filea doslovno otvara file i izvršava sav kod u njemu. Ima nekoliko načina za to:
* `load "user.rb"` učitava file i izvršava ga, čak i ako je to ranije učinio. Iz nekog razloga zahtjeva i `.rb` ekstenziju.
* `load "user.rb", true` izvršava kod unutar anonimnog modula, pa da lokalne varijable neće leakati u trenutni scope.
* `require "user"` učitava file, ako već nije učitan. Lista svih učitanih fileova nalazi se u `$LOADED_FEATURES` arrayu.
* `require_relative "../user"` učitava file iz lokacije relativne trenutnom fileu.

Pri korištenju `load` i `require`, file se traži u `$LOAD_PATH` (`$:`) arrayu.
U `$LOAD_PATH` se defaultno nalaze samo folderi sa standardnim rubyjem i binarnim ekstenzijama.

Gemovi defaultno nisu u `$LOAD_PATH`, ali RubyGems overridea `require` metodu. Nakon što `require 'active_support'` ne pronađe `active_support.rb` u `$LOAD_PATH`, potražit će ga u svim instaliranim gemovima (`Gem::Specification.find_by_path`) i dodati taj path u `$LOAD_PATH`, kako ga ubuduće ne bi morao tražiti.

Ako radiš vlastiti Ruby projekt kojeg pozivaš iz `main.rb`:
* ili svugdje koristi `require_relative`
* ili dodaj `$:.unshift(File.expand_path("../lib", __FILE__))` na početak `main.rb` i svugdje koristi `require`

Ako radiš gem, koristi `require` svugdje jer će se RubyGems pobrinuti za sve.
Ako radiš na `Rails` aplikaciji, sve se autoloada pa ne trebaš pisati ništa.

## Regexi

* `/regex/`, ili `%r{regex}` ako ne želiš escapati slasheve.
* `Regexp.union(/a/, /b/, ...)` za spajanje više regexa
* `/regex/x` ignorira whitespace i komentare. Super ako želiš kompleksni regex razlomiti u više linija.
* `()` unutar regexa za capture. `(?:)` za grupiranje koje ne želiš captureati.
* `(?<ime>)` za named captures, npr. `/(?<last>\w+), (?<first>\w+)/ =~ "Santic, Nikola"` stvara varijable `last` i `first`.

Matching:
* `str.match?(/re/)` vraća boolean _2.4_
* `str =~ /re/` vraća indeks prvog matcha, ili `nil` ako ga nema
* `str.match(/re/)` vraća `MatchData` objekt: `md[0]` za cijeli match, `md[1]` za prvi capture, `md[2]` za drugi itd; `md.captures` je array svih capturea; `md.named_captures` je hash svih named capturea _2.4_ ; `md.begin(1)` indeks početka prvog capturea.

## URI.join ne radi kako misliš da radi

`URI.join('http://example.com', 'plans', 'change')` neće vratiti `http://example.com/plans/change` nego `http://example.com/change`!
Za joinanje više dijelova URL-a koristi `File.join` (osim što neće raditi na Windowsima)

## method_missing

Kad definiraš `method_missing`, uvijek definiraj i `respond_to_missing?` jer će on, osim `respond_to?`, riješiti da radi i dohvaćanje metode s `method`.

## ruby -n

`-n` radi loop nad svakom linijom STDIN-a i stavlja ga u $_
`ls | ruby -ne 'puts $_'`

## &:protected_metoda

`&:some_protected_method` neće raditi jer se poziv metode šalje iz Symbola, a ne iz trenutne klase. Tja!

## Hash

Za lazy loaded: `Hash.new { |hash, k| hash[k] = expensive_operation(key) }` lazy loaded hash

`{a: {b: 1}}.dig(:a, :b)` nested pristup bez exceptiona _2.3_
`{a: nil, b: 1}.compact` uklanja sve key-value parove kojima value `nil` _2.4_

## Optimizacije

`reverse_each` je brži od `reverse.each`
`str.sub` je brži od `str.gsub` (ako treba samo prva izmjena)
`str.tr` je brži od `str.gsub` (ako se mijenja jedno slovo)
`('a'..'z').cover?('x')` uspoređuje samo s rubovima rangea, dok `('a'..'z').include?` pretvara cijeli range u array.

`@user ||= User.find(id) if id.present?` je loše, jer će izvoditi `if` čak i ako je `@user` postavljen.
`@user ||= (User.find(id) if id.present?)` je malo ružnije, ali puno bolje. Pogotovo za metode koje se često pozivaju.

## Novi featuri

### 2.3

`user&.admin?` je kao `user && user.admin?`. U principu kao `try`, ali `try` ne baca exception ako metoda ne postoji.
`['a', 'b', 'c'].grep_v(/a/) # => ['b', 'c']` - vraća one koji se ne slažu regexom

### 2.4

`binding.irb` unutar koda otvara REPL s trenutnim stanjem.
`[1, 2, 3].sum` od sada i u Rubyiju
 od sada i u Rubyju
`Dir.empty?("dir_name")`, `File.empty?("file_name")`
`123.digits` - vraća znamenke (`[3, 2, 1]`). `digits(16)` radi u drugim bazama
`Fixnum`, `Bignum` i `Integer` su svi sada `Integer` (implementacija je skrivena)
`round`, `ceil`, `floor` primaju precision argument
Unicode characteri se downcaseaju kako treba.
`String.new` prima `:capacity` za memoriju koju treba predalocirati.
