# Metaprogramming

## Local scope

Postoje 3 razine local scopea:
1. top level
2. definicija klasa i modula, izvršava se odmah po definiciji
3. definicija metoda, izvršava se pri pozivu metode

Varijabla definirana u jednoj razini nije defaultno vidljiva u drugoj. Ako želiš koristiti varijablu iz višeg scopea, umjesto `class` i `def` koristi blokove:
* `class User` zamijeni s `User = Class.new do ... end`
* `def read` zamijeni s `define_method :read do ... end`

## self i class

U svakom trenutku izvođenja koda definirani su **trenutni objekt** (self) i **trenutna klasa** (gdje se dodaju def-ovi).

`self` unutar metode je uvijek objekt nad kojim se metoda pozvala.
`self` unutar `class` ili `module` definicije je ta klasa ili modul.

## Closure i bindings

Blok unutar metode vidi varijable koje su definirane izvan bloka. Ako blok izvučemo izvan metode (npr. kao `proc`), on će sačuvati referencu na varijable, produžujući im život.

`proc.binding`, `binding` i `TOPLEVEL_BINDING` vraćaju Binding objekt koji sadrži lokalne varijable, `self`, i blok koji bi se pozvao s `yield`.

Možeš čak i izvesti kod u kontekstu tog bindinga s `binding.eval("print x")`.

`binding.irb` unutar koda otvara REPL s trenutnim stanjem.

## Eval

`eval("1+3")` prima string i izvršava ga kao kod.
`eval("x = 3")` izvršava se u privremenom scopeu - može mijenjati postojeće varijable, ali novostvorene neće biti vidljive izvan njega.
`eval("print x", binding)` kao drugi parametar može primiti binding.

`user.instance_eval do ... end` izvršava blok u kontekstu objekta `obj`, npr. `self` i `@var` će biti dostupni. `def m` unutar bloka će stvoriti singleton metodu.

`User.class_eval do ... end` (ili `User.module_eval`) izvršava blok u kontekstu klase. `def m` unutar bloka će stvoriti instance metodu. Zapravo, `class_eval` je kao da opet otvoriš `class User`, samo što klasa ovdje može biti u varijabli.

`user.instance_exec(1, 2) do |i, j| ... end` (`class_exec` i `module_exec`) su isti kao i `_eval`, samo dopuštaju prosljeđivanje argumenta. To je korisno ako želiš proslijediti `@var` definiran izvan bloka, jer unutar bloka više neće biti vidljiv (`self` se promjenio). Proslijeđivanjem lokalnih varijabli također izbjegavaš stvaranje closurea.

`instance_eval` i `instance_exec` se mogu koristiti za stvaranje DSLa poput: `tag(:p) { concat("Hello") }`. Metoda `concat` se napravi dostupnom tako što se u `tag` blok poziva s `Tag.new.instance_exec(&block)`, gdje je `concat` metoda instance `Tag`.

## Varijable i konstante

`global_variables` vraća listu globalnih varijabli.
`local_variables` listu lokalnih varijabli definiranih u trenutnom scopeu.
`eval("#{var_name} = 2")` postavlja varijablu ako joj je ime dinamičko.
`defined?(x)` provjerava da li varijabla postoji.

`obj.instance_variables` vraća listu instance varijabli.
`obj.instance_variable_get(:@x)` i `_set(:@x, 1)` čita i postavlja varijable izvan objekta.
`obj.instance_variable_defined?(:@x)` provjerava da li varijabla postoji.

`User.class_variables` vraća listu class varijabli.
`User.class_variable_get(:@@x)` i `_set(:@@x, 1)` čita i postavlja varijable izvan klase.
`User.class_variable_defined?(:@@x)` provjerava da li varijabla postoji.

`User.constants` vraća sve konstante definirane unutar modula.
`User.const_get(:Math)` i `_set(:Math, 2)` čita i postavlja konstantu.
`User.const_defined?(:Math)` provjerava je li konstanta postavljena.

## Metode

Svaki objekt ima: `methods`, `public/protected/private_methods`, i `singleton_methods`.

Klase i moduli još imaju: `instance_methods` i `public/protected/private_instance_methods`.

`User.method_defined?(:name)` je ekvivalentno `user.respond_to?(:name)`, osim što method_defined uzima u obzir i protected metode.

`obj.public_send(:name)` poziva public metodu.
`obj.send(:name)` poziva metodu, čak i ako je private.
`obj.__send__(:name)` je sigurni sinonim ako netko slučajno overridea `send`.

`define_method(:name) do ... end` je privatna metoda unutar klase, stvara instance metodu.
`define_singleton_method(:name) do ... end` je public metoda objekta, stvara singleton metodu.

`klass.remove_method(:name)` uklanja metodu iz klase, ostaju metode naslijeđene iz superklasa.
`klass.undef_method(:name)` zabranjuje pozive metode, čak i ako su definirane u superklasama.

## Method object

`user.method(:name)` (ili `public_method`) vraća **Method** objekt .

Metoda se može pozvati iz method objekta pomoću `method.call(...)`. Ima `.name`, `.arity` (broj argumenata koje prima), `.reciever` (objekt na koji je metoda bindana) i `.owner` (klasu u kojoj je metoda definirana).

Method objekti nisu closurei, i nemaju pristup lokalnim varijablama izvan svog koda. Jedini binding koji čuvaje je `self`.

`User.instance_method(:name)` (ili `public_instance_method`) vraća **UnboundMethod** objekt. Može se dobiti iz method objekta s `method.unbind`.

Unbound method predstavlja metodu odvojenu od objekta. Da bi se pozvala, mora se prvo bindati: `method.bind(obj).call(...)`.

## Hooks

`Modul.included` se poziva nad modulom kad ga netko includa, argument je klasa ili modul koji ga includa.
`Modul.extended` isto, samo za extendanje.

`Klass.inhereted` se poziva nad klasom kad je netko naslijedi, argument je subklasa.
`Klass.method_added` se poziva kad se doda instance metoda u klasu, argument je ime metode.
`obj.singleton_method_added` se poziva kad se doda singleton metoda u objekt, argument je ime metode.
