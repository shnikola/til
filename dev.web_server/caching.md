# Caching

Web Cache stoji između servera i klijenta, pamti odgovore i koristi ih ako se request ponovi. Time se skraćuje put do servera, pa se *smanjuje latencija* i *smanjuje promet u mreži*.

*Browser Cache* lokalno sprema response za jednog usera (*private cache*). Smanjuje potrošnju bandwidtha za iste assete koji se pojavljuju na različitim stranicama.

*Proxy Cache* je na mreži izmežu klijenta i servera, sprema response za mnoge usere (*shared cache*). Postavlja ih ISP kako bi smanjio latenciju i promet na mreži.

*Gateway Cache* (reverse proxy cache) je ispred servera, sprema response za mnoge usere. Postavlja ih vlasnik servera kako bi site bio brži i skalabilniji. CDN je distribuirana mreža Gateway Cacheva (jer server upravlja onim što će se cachirati).

## Browser and Proxy Cache

Kada request stigne do cachea:
1. Ako nema cachirani odgovor, prosljeđuje request serveru. Odgovor će spremiti ako response header dopušta, npr. ako nije `Cache-Control: no-store`. Shared cache također neće spremati SSL i Authenticated odgovore.
2. Ako je cachirani odgovor *fresh*, cache ga šalje klijentu bez da provjeri sa serverom. Podatak je *fresh* ako ima expiry koji još nije istekao.
3. Ako je cachirani podatak *stale*, cache će pitati server da ga *validira*, tj. potvrdi je li ostao isti. U posebnim slučajevima (npr. kad je odspojen od mreže) cache može servirati *stale* podatak.

**Freshness** omogućava da se odgovor dobije instatno iz cachea, a **validation** da se cijeli odgovor ne mora nanovo skidati sa servera.

### Freshness headeri

`Expires: <date>` je stari način, sadrži samo vrijeme kada resource postaje stale. Za preciznije upravljanje, koristi se `Cache-Control`.

`Cache-Control: public, max-age=60` označava normalnu dozvolu cachiranja koje vrijedi 60 sekundi. Za trajanje shared proxy cachea koristi `s-maxage`.

`Cache-Control: private, max-age=60` znači da browser smije cachirati, ali shared cache ne (radi se o podatcima za određenog usera).

`Cache-Control: no-store` ni browser ni shared cache ne smiju cachirati (za npr. privatne bankovne podatke).

`Cache-Control: no-cache` ovaj se malo blesavo zove. Browser mora svaki put provjeriti sa serverom je li cachirani podatak i dalje fresh. Korisno za dinamičke public stranice koje se često mijenjaju, npr. sportski rezultati.

`Cache-Control: must-revalidate, max-age=60` browser mora provjeriti sa serverom je li cachirani podatak i dalje fresh, ali samo ako je prošlo `max-age` vremena od zadnje revalidacije. Korisno za dinamičke public stranice koje se rjeđe mijenjaju.

Ako klijent pošalje request s `Vary: User-Agent` headerom, cache smije odgovoriti samo ako je cachirani response dohvaćen s istim `User-Agent` headerom. Na taj način se može pripaziti da se sadržaj generiran dinamički pomoću headera ne servira krivom klijentu.

### Validation headeri

`Last-Modified` server vraća uz resource. Cache tu vrijednost šalje serveru pri validaciji kao `If-Modified-Since` header. Ako je resource nepromijenjen, server će vratiti `304 Not Modified` i resource se ne mora opet skidati.

`ETag` je token resourca (najčešće hash ili fingerprint) kojeg server vraća. Mijenja se kad se resource promijeni. Cache ga šalje serveru kao `If-None-Match` header. Opet, ako je resource nepromijenjen, server vraća `304 Not Modified`.

## Cache Busting

Cache se ne može invalidirati dok ne istekne expiry. Jedini način za prisiliti cacheve da reloadaju resource je promijeniti URL. Cachiranje HTML-a se zato izbjegava, jer ne možemo lako promijeniti URL kad treba.

Najbolji način za mijenjanje URL-a resursa je staviti fingerprint u ime filea, npr. `script.x234dff.js`. Kad se promijeni resurs, promijenit će mu se i ime. Uz to možemo slobodno koristiti agresivan caching poput `Cache-Control: max-age=31536000`.

`Clear-Site-Data: cache` će navesti browser da pobriše sav cache za origin na koji je poslao request.

## Caching Checklist

* za isti resource koristi jedan URL.
* enablaj ETagove na serveru.
* cachiraj na CDNu assete resource koje možeš.
* odvoji dio filea koji se često mijenja kako bi se ostatak mogao bolje cachirati.

## CloudFlare Railgun

CloudFlare inače servira statične resource (slike, JS, CSS) direktno iz cachea svojih datacentara najbližih korisniku. Ali pomoću Railguna može servirati i dinamične stranice koje se ne mogu cachirati, npr. Facebook timeline za određenog korisnika, ili naslovnica CNN-a koja često objavljuje nove vijesti.

Na server se instalira poseban "Listener" koji drži otvorenu konekciju s datacentrima. Pošto dinamične stranice često mijenjaju samo mali dio stranice, šalje se samo promjena što uštedi i do 98% prometa.

Dostupno samo za Enterprise korisnike.

# Literatura

* https://www.mnot.net/cache_docs/
* https://developers.google.com/web/fundamentals/performance/optimizing-content-efficiency/http-caching
