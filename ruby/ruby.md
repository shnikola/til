# Ruby

## Postoji li parent method
`if defined? super`


## Varijable iz regexa
```
if /^(?<last>\w+),\s*(?<first>\w+)$/ =~ "Santic, Nikola"
  p first, last
end
```

## method_missing
Kad definiraš `method_missing`, uvijek definiraj i `respond_to_missing?` jer će on, osim `respond_to`,
riješiti da radi i dohvaćanje metode s `method`.


## Novo u 2.3
`user&.admin?` je kao `user && user.admin?`. U principu kao `try`, ali `try` ne baca exception ako metoda ne postoji.
`{a: {b: 1}}.dig(:a, :b) # => 1` - nested pristup bez exceptiona
`['a', 'b', 'c'].grep_v(/a/) # => ['b', 'c']` - vraća one koji se ne slažu regexom


## ruby -n
`-n` radi loop nad svakom linijom STDIN-a i stavlja ga u $_
`ls | ruby -ne 'puts $_'`


## Ruby modules
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


## &:protected_metoda
`&:some_protected_method` neće raditi jer se poziv metode šalje iz Symbola, a ne iz trenutne klase. Tja!


## Lazy loadani hash
```ruby
result = Hash.new do |hash, key|
  hash[key] = some_expensive_operation(key)
end
```

## Debugging
http://www.schneems.com/2016/01/25/ruby-debugging-magic-cheat-sheet.html
https://tenderlovemaking.com/2016/02/05/i-am-a-puts-debuggerer.html
* `bundle open gem_name` - u editoru otvara verziju gema iz Gemfilea. `EDITOR=atom` za definiranje editora.
* `gem pristine gem_name` - ako si prčkao po gemu i dodavao kod, ovo ga resetira.
* `obj.method(:foo).source_location` - file i linija gdje je metoda definirana
* `obj.method(:foo).super_method.source_location` - file i linija gdje je **super** definiran
* `obj.method(:foo).parameters` - lista argumenata koje metoda prima
* `caller` - unutar metode, vraća backtrace poziva
* `ruby -d foo.rb` - ispisuje sve exception, čak i one rescuane. `bundle exec ruby -d script/rails runner foo.rb` za Rails.
* `ObjectSpace.trace_object_allocations_start`, pa onda `ObjectSpace.allocation_sourcefile(obj)` i `ObjectSpace.allocation_sourceline(obj)` - gdje je objekt alociran
* `trap(:INFO) { Thread.list.each {|t| p t, t.backtrace }}` - za otkrivanje deadlocka, na CTRL+T (`INFO`) svi threadovi ispisuju gdje se trenutno nalaze
