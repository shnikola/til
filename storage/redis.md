# Redis

Redis nije samo key-value store u kojem su values stringovi, već može spremanti različite struktrure podataka kao što su liste, setovi, hashevi, i bit mape.

## Keys

Redis keyevi su binary safe, što znači da key može biti string, ali i JPEG slika. Svejedno, izbjegavaj jako duge keyeve jer zauzimaju memoriju i usporavaju lookup. Ako želiš koristiti neku veliku vrijednost, hashiraj je s SHA-1.

`EXISTS mykey` vraća `1` ako key postoji, `0` ako ne postoji.
`DEL mykey` briše key. Vraća `1` ako je key postojao, `0` ako nije,

`TYPE mykey` vraća tip podatka spremljen pod keyem, `none` ako key ne postoji.

Svakom keyu može se podesiti expiration, nakon kojeg će biti obrisan kao da je pozvan `DEL`.

`EXPIRE mykey 5` postavlja trajanje keya na 5 sekundi.
`SET mykey value ex 10` dodaje novi key s trajanjem 10 sekundi.

`TTL mykey` vraća broj sekundi do isteka keya.

`PERSIST mykey` uklanja expiration, key neće biti obrisan.

## Strings

`SET mykey value` sprema string pod zadanim keyem. Ako key postoji, bit će prepisan. Za dodavanje stringa s razmakom, koristi navodnike `"foo bar"`.

`INCR counterkey` parsira zapisanu vrijednost i atomarno je povećava za 1.
`INCRBY counterkey 50` povećava za zadani broj.
`DECR` i `DECRBY` smanjuju.

`GETSET mykey newval` sprema novu vrijednost pod key, a vraća staru.

`MGET a b c` dohvaća više keyeva odjednom kako bi umanjio latenciju.
`MSET a 10 b 20 c 10` sprema više vrijednosti.

`MULTI` otvara transakciju. Queuea sve sljedeće naredbe dok se ne pozove `EXEC` koji će ih izvršiti atomarno.

## Lists

Liste u Redisu su implementirane pomoću linked liste, što znači da se dodavanje i brisanje elementa na početak ili kraj izvodi u konstantom vremenu, ali dohvaćanje elementa po indeksu traje linearno.

`LPUSH mylist a` dodaje element na početak liste. Može istovremeno dodati više elemenata, npr. `LPUSH mylist a 12 7 k`
`RPUSH mylist b` dodaje element na kraj liste.

`LPOP mylist` vraća prvi element i briše ga iz liste.
`RPOP mylist` vraća zadnji element i briše ga iz liste.

`LRANGE mylist 0 -1` dohvaća elemente od prvog do zadnjeg.

Liste se često koriste se cachiranje, npr. posljednjih postova na twitteru koji se guraju s `LPUSH` i dohvaćaju s `LRANGE 0 9`. U tom slučaju dobro je definirati capped listu koja će pamtiti samo posljednjih N elemenata a ostale odbacivati.

`LPUSH mylist <elementi>` i `LTRIM mylist 0 9` dodat će nove elemente na početak i odbaciti sve osim prvih deset.

Liste se korist i za job management gdje se jobovi stavljaju u listu s `LPUSH` a workeri ih uzimaju s `RPOP`. U tom slučaju još bolje je koristiti `BRPOP` koji u slučaju da nema elemenata u listi blokira workera dok se novi element ne stavi.

`BRPOP mylist 5` znači da će worker čekati 5 sekundi.
`BRPOP list1 list2 5` će pokušati dohvatiti element iz ijedne od danih lista.

## Hashes

`HSET mykey a 5` postavlja field `a` na vrijednost `5`.
`HMSET mykey a 5 b 1 c 3` postavlja više fieldova.

`HGET mykey a` dohvaća field `a`
`HMGET mykey a b c` dohvaća više fieldova odjednom, vraća array.

`HDEL a b c` briše fieldove `a`, `b` i `c`

`HINCRBY mykey a 10` inkrementira vrijednost fielda `a` za `10`.

`HGETALL mykey` vraća cijeli hash.
`HKEYS mykey` vraća sve fieldove hasha.
`HVALS mykey` vraća sve vrijednosti hasha.

# Literatura

* https://redis.io/topics/data-types-intro
