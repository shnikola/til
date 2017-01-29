# Caching

Web Cache stoji između servera i klijenta, pamti odgovore i koristi ih ako se request ponovi. Time se skraćuje put do servera, pa se *smanjuje latencija* i *smanjuje promet u mreži*.

*Browser Cache* lokalno sprema response za jednog usera (*private cache*). Smanjuje potrošnju bandwitha za iste assete koji se pojavljuju na različitim stranicama.

*Proxy Cache* je na mreži izmežu klijenta i servera, sprema response za mnoge usere (*shared cache*). Postavlja ih ISP kako bi smanjio latenciju i promet na mreži.

*Gateway Cache* (reverse proxy cache) je ispred servera, sprema response za mnoge usere. Postavlja ih vlasnik servera kako bi site bio brži i skalabilniji. CDN je distrubuirana mreža Gateway Cacheva (jer server upravlja onim što će se cachirati).

## Browser and Proxy Cache

Kako Cache radi:
1. Ako header responsa kaže da ga se ne smije čuvati, cache ga ignorira.
2. Ako je request SSL ili Authenticated, shared cache ga ignorira.
3. Ako je cachirani podatak *fresh*, cache ga šalje klijentu bez da provjeri sa serverom. Podatak je *fresh* ako ima expiry koji još nije istekao (ili ako je cache očitao podatak nedavno i on nije izmijenjen duže vremena).
4. Ako je cachirani podatak *stale*, cache će pitati server da ga *validira*, tj. potvrdi je li ostao isti.
5. U posebnim slučajevima (npr. kad je odspojen od mreže) cache može servirati *stale* podatak.

*Freshness* omogućava da se odgovor dobije instatno iz cachea, a *validation* da se cijeli odgovor ne mora nanovo skidati sa servera.

Headeri za *freshness*:
* `Expires: <date>` je stari način, sadrži samo vrijeme kada resource postaje stale.
* `Cache-Control` omogućava puno bolje i preciznije upravljanje. Može imati listu vrijednosti:
  * `public` default, može se normalno cachirati.
  * `private` browser smije cachirati, ali shared cache ne (radi se o podatcima za određenog usera)
  * `no-cache` cache za idući request mora validirati sa serverom prije nego pošalje cachirani rezultat.
  * `no-store` ni browser ni shared cache ne smiju cachirati (za npr. privatne bankovne podatke)
  * `max-age=<seconds>` koliko dugo će biti fresh. `s-max-age` je isto, ali vrijedi samo za shared proxy cache.

Headeri za *validaciju*:
* `Last-Modified` server vraća uz resource. Cache tu vrijednost šalje serveru pri validaciji kao `If-Modified-Since` header. Ako je resource nepromijenjen, server će vratiti `304 Not Modified` i resource se ne mora opet skidati.
* `ETag` je token resourca (najčešće hash ili fingerprint) kojeg server vraća. Mijenja se kad se resource promijeni. Cache ga šalje serveru kao `If-None-Match` header. Opet, ako je resource nepromijenjen, server vraća `304 Not Modified`.

Ako klijent pošalje request s `Vary: User-Agent` headerom, cache smije odgovoriti samo ako je cachirani response dohvaćen s istim `User-Agent` headerom. Na taj način se može pripaziti da se sadržaj generiran dinamički pomoću headera ne servira krivom klijentu.

Cache se ne može invalidirati dok ne istekne expiry. Jedini način za prisiliti cacheve da reloadaju resource je promijeniti URL - zato je korisno ubaciti fingerprint u ime filea, npr. `script.x234dff.js`.

## Caching Checklist

* za isti resource koristi jedan URL.
* enablaj ETagove na serveru.
* resource koje možeš cachiraj na CDNu.
* odvoji dio filea koji se često mijenja kako bi se ostatak mogao bolje cachirati.

## nginx Reverse Proxy Cache

`nginx` ima svoj vlastiti ugrađeni proxy cache - nema potrebe za odvojenim rješenjima poput Varnisha.

U `nginx.conf` dodaj:
* `proxy_cache_path /var/lib/nginx/cache levels=1:2 keys_zone=backcache:8m max_size=50m`
  * `/var/lib/nginx/cache` definira directory u kojem će s držati cache (pripazi da postoji i da ga ownaš)
  * `levels` način organiziranja cachea po folderima (nebitno)
  * `keys_zone` definira ime zone, veličinu rezerviranu za keyeve (`8MB`) i maksimalnu veličinu za cache
* `proxy_cache_key "$scheme$request_method$host$request_uri$is_args$args"` način na koji se generira cache key.
* `proxy_cache_valid 200 302 10m` storea uspješne response i redirectove na 10 minuta, a `404 1m` na 1 minutu.

U konfiguraciju servera dodaj:
* `proxy_cache backcache` definira koji cache zone se koristi.
* `add_header X-Proxy-Cache $upstream_cache_status` dodaje header je bio cache hit ili miss.

Ako imaš baš jako veliki traffic, može se dogoditi *cache stampede*. Više istovremenih requestova počne dohvaćati resource koji je expirao pa cache prosljeđuje request na server za svakog (umjesto samo za jednog). Ovo se može izbjeći lockingom: `proxy_cache_lock on;` i `proxy_cache_use_stale updating;`

Na aplikacijskom serveru vraćaj `Cache-Control: public` samo za stranice koje ne koriste cookije.

## CloudFlare Railgun

CloudFlare inače servira statične resource (slike, JS, CSS) direktno iz cachea svojih datacentara najbližih korisniku. Ali pomoću Railguna može servirati i dinamične stranice koje se ne mogu cachirati, npr. Facebook timeline za određenog korisnika, ili naslovnica CNN-a koja često objavljuje nove vijesti.

Na server se instalira poseban "Listener" koji drži otvorenu konekciju s datacentrima. Pošto dinamične stranice često mijenjaju samo mali dio stranice, šalje se samo promjena što uštedi i do 98% prometa.

Dostupno samo za Enterprise korisnike.

# Literatura

* https://www.mnot.net/cache_docs/
* https://developers.google.com/web/fundamentals/performance/optimizing-content-efficiency/http-caching
