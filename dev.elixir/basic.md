# Elixir

## Lists and Tuples

`[1, 2, 3]` - lista, koristi za prependanje
`hd(list)`, `tl(list)` - prvi element i ostatak

`{1, 2, 3}` - tuple, koristi za brz pristup po indexu
`elem(tuple, i)` - i-ti element tupla
`put_elem(tuple, i, elem)` - vraća novi tuple, jer su svi data typovi immutable

`size` - ako je dohvatljiv u konstantnom vremenu, npr. `tuple_size`, `byte_size`
`length`  ako je dohvatljiv u linearnom, npr. `length(list)`, `String.length`

## Operatori

`[1, 2, 3] ++ [4, 5]` - konkatenacija lista
`"foo" <> "bar"` - konkatenacija stringova

`and`, `or`, `not` - strogo zahtjevaju booleane
`&&`, `||`, `!` - kao ruby, sve osim false i null je true
`==` i `===` se razlikuju u usporedbi intova i floatova (`2 === 2.0` je false)

## Pattern matching

`{a, b, c} = {:hello, "world", 42}` binda vrijednosti u varijable
`[head | tail] = [1, 2, 3]` - head = 1, tails = [2, 3]
`x = 1` - rebinda vrijednost varijable
`^x = 2` - matcha postojeću vrijednost umjesto da rebinda

```
case {1, 2, 3} do
  {1, x, 3} ->
    "Binda x = 2 i izvršava ovaj kod"
  {1, x, 3} when x > 0 ->
    "Ukoliko je guard true, binda i izvršava"
  _ ->
    "Ako se ništa nije matchalo, raisea se error. S ovim hvatamo sve else."
end
```

**Pazi**: ako guard raisea error, neće ga progarati, samo će biti false
Ako trebaš provjeravati različite stvari (nešto kao if elsif), postoji cond:
```
cond do
  2 + 2 == 5 ->
    "Neće se izvršiti"
  2 + 1 == 3 ->
    "Hoće"
end
```

`if` i `unless` postoje kao macroi na Kernelu
if false do
  :this
else
  :that
end

## Binaries, strings i char lists

`<<1, 2, 3>>` - Binary, s byteovima 1, 2 i 3
`<<0, 1>> <> <<2, 3>>` - konkatenacija Binaryja
Matching radi ubičajeno:
`<<1, 2, x>> = <<1, 2, 3>>`
Ako želimo uhvatiti "tail" binarija, možemo reći da je sam x binary,
ili koristiti konkatenaciju:
`<<1, 2, x :: binary >> = <<1, 2, 3, 4>>`
`<<1, 2>> <> x = <<1, 2, 3, 4>>`

String (s "dvostrukim navodnicima") je **binary** s bytovima UTF-8 reprezentacije tih slova, npr:
`<<104, 101, 197, 130, 197, 130, 111>>` - slovo može biti više bytova
Charlist (s 'jednostrukim navodnicima') je **lista** codepointova (ne bytova), npr:
`[104, 101, 322, 322, 111]` - svaki codepoint je jedno slovo (idu preko 256)

## Keywords and maps

`[{:a, 1}, {:b, 2}, {:b, 3}]` - Keyword Lists, doslovno lista 2-članih tuplea.
Ordered su, mogu više puta imati isti key. Koriste se uglavnom za argumente funkcije.
`[a: 1, b: 2]` - skraćena sintaksa
`list[:a]` - dohvaća prvi value pod tim keyem
`list ++ [a: 1]` - dodavanje nove vrijednosti

`%{:a => 1, 2 => b}` - Map, key value store (kao hash u Rubiju). Bilo što može biti key,
nema orderinga
`%{a: 1, b: 2}` - skraćena sintaksa ako su keyevi atomi
`map[:a]` - dohvaća vrijednost
`Map.get(map, :a)` - isto
`map.c` - isto
`%{map | a: 2}` - postavlja :a na 2 (ako :a ne postoji, baca error)

`put_in` i `update_in` za dodavanje u nestanim strukturama

## Functions and Modules

`def sum(a, b), do: x + y` - named function, poziva se s `sum(1, 2)`, nemaju closure
`sum = fn x, y -> x + y end` - anonymous function, poziva se s `sum.(1, 2)`, imaju closure

Da bi proslijedio named function kao objekt, treba je captureat:
`fun = &sum(&1, &2)` - to je sad anonymous function
`fun = &sum/2` - isto, samo skraćena syntaxa

`&` se može koristiti za skraćeno generiranje funkcija: `&(&1 + 1)` je `fn x -> x + 1 end`

Više povezanih funkcija grupira se u Module
```
defmodule Math do
  def sum(a, b) do
    a + b
  end

  defp private_sum(a, b) do
    a + b
  end
end
```

`def join(a, b, sep \\ " ")` - definira defaultnu vrijednost argumenta

## Enums and streams

Funkcije za obradu lista nalaze se u `Enum` modulu.
`Enum.filter([1, 2, 3], &(&1 > 5))` - filter
`Enum.map([1, 2, 3], &(&1 * 2))` - map
`Enum.reduce([1, 2, 3], 0 , &(&1 + &2))` - reduce

Za nizanje enum obrade koristi se pipe operator `|>`
`1..100_000 |> Enum.map(&(&1 * 3)) |> Enum.filter(odd?) |> Enum.sum`
`|>` uzima lijevi rezultat i prosljeđuje ga kao prvi argument nadesno

Enums su eager load - za lazy load koristi se `Stream`
`1..100_000 |> Stream.map(&(&1 * 3)) |> Stream.filter(odd?)`

## Processes

Procesi u elixiru su lightweight, concurrent i isolated. Komuniciraju preko poruka.
Za stvaranje novog procesa korsiti se `spawn`:
`pid = spawn fn -> 1 + 2 end` - prima funkciju koju će proces izvršiti, vraća pid
`Process.alive?(pid)` - je li proces završio
Svaki process ima mailbox u kojem drži poruke
`send pid, {:hello, "world"}` - šalje poruku na pid
Receive ide kroz mailbox i traži poruku koji matcha neki od patterna
```
receive do
  {:hello, msg} -> msg
  {:world, msg} -> "won't happen"
after
  1000 -> "Timeout"
end
```
U konzoli, `flush` ipisuje sve poruke u mailboxu trenutnog procesa
Ako se dogodi error u procesu, samo će ispisati u logu. Ako želimo da se
error propagira i u parent proces, koristimo `spawn_link`.
`Process.register(pid, :nikola)` - daje ime procesu, pa svi mogu sendati bez da mu znaju pid.

`Task.start fn -> 1 + 2 end` - abstrakcija iznad procesa, malo bolje errore ispisuje.
`Agent.start_link(fn -> %{} end)` - apstrakcija procesa koja drži globalno stanje

## IO

Ispisivanje: `IO.puts "Hello"` ili `IO.puts :stderr, "Hello"`
Učitavanje: `IO.gets "Unesi ime: "` - vraća cijelu liniju (s \n)

Stvaranje patha: `Path.join("app", "models")` ili `Path.expand("~/projects")`
Baratanje fileovima `File.rm/1`, `File. mkdir/1` itd.
Otvaranje filea: `{:ok, file} = File.open "hello.txt", [:write]` - vraća pid procesa koji otvara file
Pisanje u file s `IO`: `IO.binwrite(file, "Hello")` ili `IO.puts(file, "Hello")`
Čitanje iz filea s `IO`: `IO.gets(file)`
Čitanje cijelog filea odjednom:
Ako očekuješ da bi mogao biti error:
```
case File.read("hello.txt") do
  {:ok, body}      -> # do something with the `body`
  {:error, reason} -> # handle the error caused by `reason`
end
```
ili ako očekuješ da će file biti tu:
```
File.read!("hello.txt") # ako je neuspješno raisea se error
```

## Alias, require and import

`alias Foo.Bar, as: Bar` - module `Foo.Bar` se može zvati s `Bar` u trenutnom scopeu
`require Integer` - učitava modul pri kompilaciji, potrebno ako želimo koristiti macroe tog modula
`use ExUnit.Case, option: :value` - requirea modul i poziva njegov `__using/1__` s options
`import List, only: [duplicate: 2]` - možemo zvati `duplicate` umjesto `List.duplicate`
`import List, only: :macros` - automatski radi i require

Imena modula (npr. `String`) se pri kompilaciji pretvaraju u atome `:"Elixir.String"`


PITANJA:
 je li syntax sugar za if vrijedi samo za do/else keyword argumente ili za sve?
