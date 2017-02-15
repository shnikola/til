# Ruby

## Hash

Objekt koji koristiš kao ključ mora imati metode `eql?` i `hash`.
Ako je key mutable, ili napravi `freeze` ili koristi `hash.rehash` kada se promjeni. Stringovi su poseban slučaj, i Ruby interno sprema kopije svih koji se koriste kao keyevi.

`Hash.new { |h, k| h[k] = [] }` s defaultnom vrijednosti.
`Hash.new { |h, k| h[k] = expensive_operation(k) }` lazy loaded.

`{a: {b: 1}}.dig(:a, :b)` nested pristup bez exceptiona _2.3_
`{a: nil, b: 1}.compact` uklanja sve key-value parove kojima value `nil` _2.4_
`{a: 1, b: 2}.assoc(:a)` vraća key-value pair, `[:a, 1]`

## Array

`[1, 2, 3].sum` vraća sumu svih elemenata.
`[1, 2, 3].minmax` vraća min i max arraya.
`['a', 'b', 'c'].grep_v(/a/) # => ['b', 'c']` vraća one koji se ne slažu regexom

## Equality

`equal?` stroga jednakost, provjerava je li isti `object_id`.
`eql?` provjerava jednakost vrijednosti, ali ne dopušta konverziju.
`==` manje strog, dopušta jednostavnu konverziju, npr. `1 == 1.0`
`===` koristi se za `case`. `Range` ga mapira na `include?`, `Regexp` na `match` itd.

## Comparable

`Comparable` je module, ako ga includaš i definiraš `<=>`, automatski dobijaš `<`, `<=`, `==`, `=>`, i `>`. Ponekad je dobro sam implementirati optimalniji `==`.

Ruby će od `==` sam stvoriti `!=`.

## Enumerable

`Enumerable` je module. Includaj ga ako je tvoja klasa kolekcija.
U klasi definiraj `each` metodu, i automatski dobijaš i `select`, `map` itd. Objekti unutar kolekcije moraju imati definiran `<=>`

## Enumerator

`Enumerator` je objekt koji možeš iterirati, standardno (s `each`) ili ručno (s `next`).

`enum_for(:some_method)` (ili `to_enum(:some_method)`) vraća enumerator za metodu koja radi iteraciju, bez da dohvaćaš sve podatke.

Za metode koje primaju block: `return enum_for(__method__) unless block_given?`

`Enumerator.new do` koristi se za generiranje beskonačnih streamova.

## Freeze

`obj.freeze` pretvata objekt u immutable.

## Cloning

`obj.clone` kopira cijeli objekt, uključujući i singleton metode i freeze flag.
`obj.dup` kopira cijeli objekt, bez singleton metoda i unfreeza ga.

Pri kloniranju se ne rade deep kopije. Sve reference koje objekt drži će biti kopirane, pa će i kopija pokazivati na iste objekte.

`initialize_copy` metoda se poziva pri kloniranju. U nju možeš staviti npr. kloniranje djece za deep copy.

`Marshal.dump(obj)` i `Marshal.load(str)` serijaliziraju i deserijaliziraju objekt. Usto su najjednostavniji način za napraviti deep clone.

## Splat (*)

`def max(a, *b)` u definiciji metode `*` pretvara listu argumenata u array `b`.
`max(*[1, 2, 3])` u izrazu `*` pretvara array u listu argumenata metode.

## Blocks, Procs, Lambdas

`def seq(a, &b)` u definiciji metode `&` pretvara blok u Proc objekt `b`.
`seq(&proc)` u pozivu metode `&` pretvara Proc objekt u blok.

`&x` u izrazu zapravo poziva `x.to_proc`.
`&:to_s` radi jer Simboli imaju definirano `to_proc` ponašanje.

Pripazi, `&:my_protected_method` neće raditi jer se poziv metode šalje iz Symbola, a ne iz trenutne klase.

Proc i lambda su oboje instance `Proc` klase i imaju metode:
* `proc.call(args)` za poziv, ili u skraćenom obliku `proc.(args)`.
* `proc.arity` broj parametara koje prima
* `proc.curry(2)` vraća Proc s prvim argumentom postavljenim.

Proc predstavlja blok. Broj argumenata koji se šalje se ne provjerava. `return` se ne odnosi na sam blok, nego na metodu u kojoj je instanciran.
`Proc.new{...}` ili `proc { ... }` stvara novi proc.

Lambda predstavlja metodu. Broj parametara je striktan. `return` vraća samo iz lambde.
`lambda { ... }` ili `-> (args) { ... }` stvara novu lambdu.

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

## PStore

Jednostavni key value store koji se zapisuje na disk. Dobra opcija ako moraš perzistirati podatke prevelike za ENV varijable, a premale da bi koristio pravu bazu. Omogućuje za serijaliziranje bilo kakvih Ruby objekata, ne samo stringova, arrayeva i hasheva.

`store = PStore.new('my_file.pstore')` stvara novi store u zadanom fileu.
`store[:value1] = "Saved"` zapisuje na disk.
`store.delete(:value1)` briše vrijednost.
`store.roots` ispisuje sve keyeve.
`store.transaction { ... }` za transakcijsko zapisivanje.

## ruby -n

`-n` radi loop nad svakom linijom STDIN-a i stavlja ga u $_
`ls | ruby -ne 'puts $_'`

## Operator precedence

`array << 1 == 2 ? : 'a' : 'b'` će dodati `1` u array jer `<<` ima veći precedence. Radije koristi `array.push` ili `<< ( ... )`.

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

### 2.4

`binding.irb` unutar koda otvara REPL s trenutnim stanjem.
`Dir.empty?("dir_name")`, `File.empty?("file_name")`
`123.digits` - vraća znamenke (`[3, 2, 1]`). `digits(16)` radi u drugim bazama
`Fixnum`, `Bignum` i `Integer` su svi sada `Integer` (implementacija je skrivena)
`round`, `ceil`, `floor` primaju precision argument
Unicode characteri se downcaseaju kako treba.
`String.new` prima `:capacity` za memoriju koju treba predalocirati.
