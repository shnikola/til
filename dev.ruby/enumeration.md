# Enumeration

Modul `Enumerable` dodaje metode za iteriranje, pretraživanje, i sortiranje elemenata u kolekciji. `Array`, `Hash`, `Set` i druge kolekcije u Rubyju includaju `Enumerable` pa pružaju isti API za korištenje.

Da bi koristio `Enumerable` metode u vlastitoj klasi, dovoljno je definirati metodu `each` koja će dani blok pozvati nad svakim elementom u kolekciji. Ako želiš koristiti i sortiranje (`min`, `max`, `sort`), elementi kolekcije moraju imati definiran `<=>`.

## Iterating

`reverse_each {|e| ...}` iterira kroz kolekciju unazad.

`each_cons(3) {|e| ...}` iterira kroz kolekciju s prozorom veličine 3 (`[1, 2, 3], [2, 3, 4], ...`).

`each_slice(3) {|e| ...}` iterira kroz kolekciju po 3 elementa (`[1, 2, 3], [4, 5, 6], ...`)

`cycle {|e| ...}` iterira kroz kolekciju u loopu (`1, 2, 3, 1, 2, 3, ...`).
`cycle(3) {|e| ...}` radi 3 ponavljanja.

`each_with_index {|e, i| ...}` dodaje index elementa kao argument bloka.

`each_with_object([]) {|e, o| ...}` dodaje objekt koji se prosljeđuje svakom bloku (npr. za skupljanje dodatnih vrijednosti). Kao rezultat vraća objekt.

## Search

`find {|e| ...}` vraća prvi element koji ispunjava uvjet.

`find_index {|e| ...}` vraća indeks prvog elementa koji ispunjava uvjet.

`include?(obj)` provjerava sadrži li kolekcija dani element.

## Filtering

`all? {|e| ...}` provjerava evaluiraju li svi elementi blok u `true`. `none?` za nijedan, `one?` za točno jedan, `any?` za barem jedan.

`count{|e| ...}` broji koliko elemenata ispunjavaju uvjet.

`uniq` uklanja duplikate iz kolekcije.

`select {|e| ...}` vraća elemente koji ispunjavaju uvjet. `reject` izbacuje one koji ispunjavaju uvjet.

`grep` filtrira koristeći `===`, npr. `grep(/ab*/)`. `grep_v` filtrira elemente koji ne matchaju parametar. Ako im se preda blok, mapirat će elemente u njega.

`first(3)` i `take(3)` dohvaćaju prva 3 elementa. `take_while {|e| ...}` dohvaća elemente od početka dok god ispunjavaju uvjet bloka.

`drop(3)` vraća kolekciju bez prva 3 elementa. `drop_while {|e| ...}` odbacuje elemente dok god ispunjavaju uvjet bloka.

## Sorting

`max` i `min` vraćaju najveći i najmanji element koristeći `<=>`.
`max(3)` i `min(3)` vraćaju 3 najveća odnosno najmanja elementa.

`max{|a, b| ...}` i `min{|a, b| ...}` definiraju metodu usporedbe (npr. `a.length <=> b.length`)

`max_by{|e| ...}` i `min_by{|e| ...}` definiraju vrijednost koja se uspoređuje (npr. `e.length`).

`minmax` i `minmax_by` istovremeno vraćaju najmanju i najveću vrijednost.

`sort` sortira kolekciju koristeći `<=>`. `sort{|a, b| ...}` definira metodu usporedbe, `sort_by{|e| ...}` definira vrijednost po kojoj se sortira.

Za ograničavanje vrijednosti, umjesto `[100, val].min` koristi `val.clamp(0, 100)`.

## Mapping and Reducing

`map {|e| ...}` vraća kolekciju rezultata bloka izvršenog nad svakim elementom.

`flat_map {|e| ...}` kao `map`, ali će pozvati svaki rezultate flattati u jedan array.

`filter_map { |i| i + 1 if i.even? }` mapira i istovremeno izbacuje one koji su `nil`, pa ne moraš zvati `compact`.

`reduce(:+)` kombinira sve elemente koristeći binarnu operaciju zadanu simbolom (bez `&`, to je obični parametar).
`reduce{|memo, e| ...}` kombinira elemente naredbom iz bloka. Kao prvi argument prosljeđuje se trenutni rezultat.
`reduce(5){|memo, e| ...}` zadaje početnu vrijednost rezultatu, inače se  postavlja na prvi element.

## Grouping

`partition {|e| e.odd?}` dijeli elemente u dva arraya, ovisno evaluiraju li blok u `true` ili `false`.

`group_by {|e| e % 3}` grupira elemente u hash prema rezultatima bloka.

`chunk {|e| e.odd?}` grupira uzastopne elemente u array prema rezultatima bloka, npr. `[1, 3, 4, 5, 7].chunk(&:odd?) => [true, [1, 3]], [false, [4]], [true, [5, 7]]`. Vraća enumerator na chunkane elemente.

`chunk_while {|i, j| i >= j}` grupira uzastopne elemente dok god ispunjavaju uvjet u bloku, npr. `[0, 9, 2, 2, 3, 2].chunk_while {|i, j| i <= j} => [[0, 9], [2, 2, 3], [2]]`. Vraća enumerator.

`slice_after {|e| ...}` grupira uzastopne elemente, prekidajući grupu kada se uvjet u bloku ispuni. Vraća enumerator.

`slice_before {|e| ...}` grupira uzastopne elemente, nova grupa počinje kada se uvjet u bloku ispuni. Rezultat prvog elementa se ignorira. Vraća enumerator.

`slice_when {|i, j| ...}` grupira uzastopne elemente, prekidajući grupu kad se uvjet ispuni. Obrnuto od `chunk_while`. Vraća enumerator.

## Enumerator

Metode `each`, `each_with_index`, `each_with_object`, `cycle` i sl. mogu se pozvati bez bloka. U tom slučaju vraćaju `Enumerator` objekt koji se može koristiti za ručno iteriranje.

`e.next` vraća sljedeću vrijednost u enumeriranoj kolekciji. Ako sljedeće vrijednosti nema, poziva se `StopIteration` exception.

`e.peek` vraća sljedeću vrijednost, bez pomaka na sljedeći element.
`e.rewind` premotava iteraciju na početak.

Enumeratori omogućuju ulančavanje različitih Enumerable metoda: `reverse_each.map.with_index {|e, i| ...}`.

Ako imaš vlastitu metodu koja iterira s blokom, možeš joj omogućiti da se ulančava s blokom pomoću `return enum_for(__method__, *args) unless block_given?`.

## Sequence generation

Ručno inicijaliziran enumerator može se iskoristiti za generiranje beskrajnog niza brojeva.

`rand_enum = Enumerator.new { |y| loop { y.yield(rand) } }` stvara enumerator koji će vratiti random broj svaki put kad se `next` pozove.

Pripazi da se u bloku ne koristi obični `yield`, nego `Enumerator::Yielder` objekt `y` kojem se predaje element, a on ga interno prosljeđuje bloku sljedeće Enumerable metode.

Iako je beskonačan, enumerator možemo koristiti s metodama koje ne prolaze kroz sve elemente: `rand_enum.take(5)` ili `rand_enum.take_while(&:odd?)`.

## Lazy enumeration

Enumerable metode poput `select` su eager. U ulančanoj naredbi
`(1..10).select(&:odd?).take(3)`, `select` će prvo dohvatiti cijeli array od `(1..10)`, filtrirati ga i proslijediti metodi `take`.

To stvara problem beskonačnim nizovima poput `(1..Float::INFINITY)` ili gornjeg `rand_enum`. `rand_enum.select(&:odd?).take(5)` se neće nikad izvršiti. `select` će pokušati dohvatiti cijeli array početnog enumeratora koji je beskonačno velik.

Da bi se to izbjeglo, koristi se `Enumerator::Lazy`. On definira posebne verzije `select` i sličnih metoda koje nisu eager, već dohvaćaju jednu po jednu vrijednost i proslijeđuju ih dalje niz lanac.

`rand_enum.lazy.select(&:odd?).take(5)` će se izvršiti tako što će `take` zatražiti prvi element, `select` će onda dohvaćati elemente dok ne dohvati jednog kojeg mu može vratiti, `take` će onda zatražiti idućeg i tako dalje.

## Chainable Streams

https://github.com/lautis/piperator

Ako trebaš downloadati file od nekoliko GB, zipati ga i opet uploadati, sekvencijalan način obrade će zahtjevati da ga zapišeš na disk ili memoriju. Optimalnije rješenje je početi ga zipati i uploadati dok se nije cijeli skinuo. Tako rade nodeJS streamovi, ali u Rubyju nema trivijalnog načina se ulančavaju blokovi koji obrađuju stream.

`Piperator` koristi `Enumerator.new` u objektima koji se ulančavaju kako bi lazily prosljeđivao podatke. Svaki ulančani objekt ima `call(enumerable)` metodu u kojoj je svojevrsni adapter drugih klasa na streamani način. Npr. za skidanje pomoću `Net::HTTP`, streamani podatci prosljeđuju se enumeratoru s `response.read_body { |chunk| yielder << chunk }`.

## Partial Downloads with Enumerators and Fibers

https://twin.github.io/partial-downloads-with-enumerators-and-fibers

Uploader `shrine` ima feature da se file direktno uploada na `S3`, bez da ide na server. Ali uploadanom fileu uvijek treba provjeriti je li *MIME type* onaj kojeg očekujemo. Pritom se ne možemo se pouzdati u ekstenziju ili `Content-Type`, jer dolaze od usera. MIME type se pouzdano može odrediti samo iz *contenta*, koristeći `MimeMagic` gem ili Unix naredbu `file`.

Ako je file remotely uploaded, ne želimo ga opet cijelog skidati na server za validaciju. Želimo stvoriti interface koji će omogućiti partial download *po potrebi* (znači, ne da se file cijeli skine odjednom, nego da skida komad po komad).

`net/http` ima `response.read_body` za chunked download, ali mora se nalaziti unutar `http.request_get` bloka, koji zatvara konekciju pri izlasku. Zato se HTTP request poziva unutar `Fiber`a, a u `http.request_get` blok stavlja se `Fiber.yield` koji pauzira izvršavanje tog koda dok se opet ne pozove s `fiber.resume`. Brilliant!

Inače, gem `HTTP.rb` je puno bolja implentacija http clienta i ima ovo out-of-the box, ali za library je uvijek bolje da koristi stdlib.

# Literatura

* http://patshaughnessy.net/2013/4/3/ruby-2-0-works-hard-so-you-can-be-lazy
