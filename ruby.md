# Ruby

## Novo u 2.3

`user&.admin?` je kao `user && user.admin?`. U principu kao `try`, ali `try` ne baca exception ako metoda ne postoji.
`{a: {b: 1}}.dig(:a, :b) # => 1` - nested pristup bez exceptiona
`['a', 'b', 'c'].grep_v(/a/) # => ['b', 'c']` - vraća one koji se ne slažu regexom


## MRuby
http://lucaguidi.com/2015/12/09/25000-requests-per-second-for-rack-json-api-with-mruby.html

Postoji MRuby (minimal ruby) koji se može embeddat gdjegod podržava C. 
Postoji i neki jako brzi novi server H2O.
Zajedno ostvaruju 25.000+ req/s.


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

## How does bundler work, anyway?
https://www.youtube.com/watch?v=4DqzaqeeMgY

Problem s gem odlučivanjem koju verziju gema koristiti. Rješenje: ne radi tu odluku u runtimeu, nego pri instalaciji.


## &:protected_metoda
`&:some_protected_method` neće raditi jer se poziv metode šalje iz Symbola, a ne iz trenutne klase. Tja!


## Lazy loadani hash
```ruby
result = Hash.new do |hash, key|
  hash[key] = some_expensive_operation(key)
end
```

## Ruby i HTTP/2

HTTP/2 nije kompatibilan s Rakeom: request/response streaming bi trebao biti default, komunikacija dvosmjerna (server ili klijent mogu gurnuti više stvari bez odgovora)
HTTP/2 je inače nešto kao websocketsi, ali za websocketse treba implementirati dosta stvari koje http nudi (redirection, REST, caching)
Prednosti:
- headeri se compressaju (uključujući cookije), uštedi se do 80% - *koristi HTTP/2 compatible proxy (nginx, h2o)*
- mogućnost slanja više responsea u istoj konekciji, domain sharding više nije potreban - *koristi HTTP/2 compatible CDNove*
- jedna konekcija znači jedan TLS handshake
- klijenti mogu izraziti preferenciju koji request da se prvi ispuni (npr. CSS i JS prije imagea)
- pošto je skidanje jednog velikog filea i puno malih sada isto, gubi se potreba za jednim velikim JS i CSS fileom i spriteovima


## RubyConf 2014 - Isomorphic App Development with Ruby and Volt by Ryan Stout
https://www.youtube.com/watch?v=7i6AL7Walc4

Volt - Isomorphic framework (isti kod na serveru i klijentu) - Ruby se kompajlira u JS koristeći *Opal*
