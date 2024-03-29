# HTTP 1.X

HTTP 1.X je protokol plaintext formata, delimitiran newline znakovima. Svaka HTTP poruka ima jednaki format:
```
<inicijalna linija, različita za request i response>
Header1: value1
Header2: value2

<optional message body, u kojem god formatu>
```
Inicijalna linija za request: `GET /path/to/file/index.html HTTP/1.1`
Inicijalna linija za response: `HTTP/1.1 200 OK`

## Metode

* `GET` dohvaća resource, bez side effecta.
* `HEAD` dohvaća samo headere, a ne i body. Korisni za saznati podatke o resourcu bez da ga skineš.
* `POST` šalje podatke vezane za resource.
* `PUT` šalje cijeli resource koji će prepisati postojeći ako ga ima. Idempotent je, što znači da slanje više puta ima isti rezultat.
* `PATCH` šalje promjene koje treba aplicirati na resourcu.
* `DELETE` briše resource.
* `TRACE` vraća request koji je primio, koristi za debugging.
* `OPTIONS` vraća metode koje server podržava za taj resource.
* `CONNECT` šalje zahtjev proxiju da napravi HTTP tunel do zadane domene. Koristi se za uspostavljanje HTTPS konekcije.

Ukoliko metoda nije dopuštena, server će vratiti `405 Method Not Allowed` i `Allow`(s) s listom dopuštenih metoda.

## Osnovni headeri

* `Date`(c,s) vrijeme kad je poruka poslana. Required za server.
* `Content-Type`(c,s) format podataka u message body.
* `Content-Length`(c,s) broj byteova u message body.
* `Upgrade`(c,s) zahtjev klijentu/serveru da se pređe na drugi protokol (npr. HTTPS, HTTP2)

* `Host`(c) jedini required za klijenta. Zahvaljujući njemu više domena može živjeti na istoj IP adresi.
* `User-Agent`(c) identificira klijenta
* `Referer`(c) adresa stranice s koje se slijedio link na trenutni request.

* `Server`(s) ime servera.
* `Retry-After`(s) u slučaju 503 Service Unavailable, vrijeme nakon kojeg da klijent pokuša opet.

## Chunked Response

Chunked response omogućuje streamanje resourca, odnosno slanje podataka bez da se unaprijed zna njihova veličina.

* `Transfer-Encoding: chunked`(s) za slanje u chunkovima.
* `Trailer`(s) lista fieldova koja će se naći u traileru poruke (npr checksumi i sl.)

```
HTTP/1.1 200 OK
Content-Type: text/plain
Transfer-Encoding: chunked

1a [veličina chunka]
abcdefghijklmnopqrstuvwxyz [chunk]
10
1234567890abcdef
0

Trailer1: value1
```

## Persistent connection

Otvaranje i zatvaranje TCP konekcije zahtjeva znatan dio CPU vremena, bandwidtha, i memorije. Umjesto da se konekcija zatvara nakon dohvaćanja jednog resursa, optimalnije ju je ostaviti otvorenom za dohvaćanje idućeg. U HTTP/1.1 to je uključeno po defaultu.

`Connection: keep-alive`(c,s) šalje se za održati konekciju otvorenom, a `Connection: close` za zatvoriti nakon responsea.

## Content negotiation

Na istom URLu može se naći više verzija istoj resursa. Klijent može zatražiti određenu verziju uz oznaku quality factora: `de; q=1.0, en; q=0.5`

* `Accept`(c) koji `Content-Type` očekuje (npr. `text/plain`)
* `Content-Type`(s) format podataka u message body.

* `Accept-Encoding`(c) koji content encoding client podržava.
* `Content-Encoding`(s) encoding korišten na podatcima (`gzip`,`deflate`,`compress`...). Zapravo bi se za ovo trebao koristiti `Transfer-Encoding`, ali browseri nisu ispravno shvatili standard.

* `Accept-Language`(c) koji jezik user preferira. Ok za hint, ali treba omogućiti da user overridea vrijednost na drugi način (npr. parametar ili cookie).
* `Content-Language`(s) jezik contenta.

* `Content-Disposition`(s) način na koji će se resource prikazati u klijentu. Koristi se uglavnom za direktni download (`attachment; filename="article.pdf"`)

## Redirects

Redirection se triggerira kada server vrati `3xx` response zajedno s `Location`(s) headerom u kojem se nalazi URL redirecta.

Za trajne GET redirecte koji će ostati zapamćeni u search engineima, koristi `301 Moved Permanently`. Za ostale metode, koristi `308 Permanent Redirect`, ali on nije podržan u starijim browserima.

Za privremene GET redirecte koje ne želiš da se zapamte u search engineima, koristi `302 Found`. Za ostale metode, koristi `307 Temporary Redirect`, ali on nije podržan u starijim browserima.

Ako želiš redirectati nakon POST ili PUT requesta kako refresh ne bi ponovio taj request, koristi `303 See Other`.

Poseban slučaj je `304 Not Modified` koji "redirecta" na lokalnu kopiju u browserovom cacheu.

## Cookies

`Set-Cookie`(s) daje upute za postavljanje jednog key-value para (npr. `Set-Cookie: user_id=2`), u korisnikov cookie jar. Server može postaviti više cookija tako da pošalje više `Set-Cookie` headera.

Nakon postavljanja cookija, browser će u svakom requestu slati `Cookie` header sa svim cookijima koje smije slati na tu domenu.

Uz postavljanje vrijednosti, mogu se postaviti i atributi (npr. `Set-Cookie: user_id=2; Secure; HttpOnly`) koji definiraju gdje i kako se cookie smije koristiti.

`Max-Age=2600000` je vrijeme u sekundama koliko cookie smije trajati prije nego ga browser izbriše. Stari browseri koriste `Expires=Thu, 01-Jan-1970 00:00:01 GMT`. Ako nije definirano, postavlja se session cookie koji traje dok je browser window otvoren.

`Domain=google.com` definira na koje domene browser smije slati cookie, uključujući i subdomene, npr. `www.google.com`. Ako nije definirano, browser će cookie spremiti pod domenu s koje je napravio request, bez subdomena. Slanje se može ograničiti i na razini patha s `Path=/user`, ali to se rijetko koristi.

`Secure` znači da će browser slati cookie samo u HTTPS requestovima. Najbolje koristi uvijek kako bi izbjegao Man-in-the-middle napade.

`HttpOnly` znači da cookie neće biti vidljiv skriptama, niti će ga skripte moći mijenjati. Defaultno, javascript može pristupiti cookijima domene u kojoj se skripta izvršava.

`SameSite` definira hoće li se cookiji slati ako se korisnik nalazi na drugom siteu (npr. otvoren je `a.com` a radi request na `b.com`; `www.google.com` i `api.google.com` se broje kao isti site). `SameSite=Strict` će slati cookie samo ako se request radi sa istog sitea na kojem je postavljen cookie. Ovo je malo prestrogo, jer neće slati cookie ni za inicijalni GET request kada klikneš na link s tuđe domene, gdje najčešće ipak želiš cookie da možeš prepoznati use. `SameSite=Lax` je uglavnom ono što želiš, jer dopušta cookije u tom inicijalnom requestu, ali ne i za ostale stvari. `SameSite=None` ne stavlja nikakve restrikcije na siteove, koristi ga ako


## Basic Authentication

Ukoliko je potrebna autentifikacija, server će vratiti `HTTP 401 Unauthorized` response i `WWW-Authenticate`(s) header sa shemom za autentifikaciju, npr. `Basic`.

Klijent odgovara s `Authorization`(c) koji sadrži shemu i credentialse. Za `Basic` to su Base64 kodirani `username:password`. Ukratko, nimalo siguran način autentifikacije.

Browser će slati isti `Authorization` header sa svakim budućim requestom unutar patha koji je vratio `401`. Npr. ako je `/admin/index` vratio `401`, browser će slati header na `/admin/*` rute, ali ne i na `/public`.

Slična metoda postoji za autentifikaciju na proxiju pomoću `Proxy-Authenticate`(s) i `Proxy-Authorization`(c).

## Cache

* `Cache-Control`(s) tko smije cachirati resource, pod kojim uvjetima, i koliko dugo.

* `Last-Modified`(s) kada je zadnji put resource promijenjen.
* `If-Modified-Since`(c) zadnji datum promjene cachiranog resourca. Poruka serveru da vrati `304` ako se nije promijenio.
* `If-Unmodified-Since`(c) poruka serveru da vrati error ako se promijenio. Korisno ako radiš PUT, a ne želiš pregaziti nečije promjene.

* `ETag`(s) entity tag, najčešće hash resourca.
* `If-None-Match`(c) ETag cachiranog resourca. Poruka serveru da vrati `304` ako se nije promijenio.
* `If-Match`(c) poruka serveru da vrati error ako se promijenio

* `Age`(s) koliko dugo je resource u proxy cacheu (u sekundama)
* `Vary`(s) lista klijentovih headera koji su korišteni za serviranje sadržaja. Na taj način klijent zna promjena kojih headera invalidira cache. Nije baš najsigurnije za prčkati, ali dobro je znati da postoji i da te može sjebati.

## Range

Omogućava parcijalno dohvaćanje filea sa servera (npr. video od druge minute).

Server s `Accept-Ranges`(s) daje do znanja da podržava range requestove za resource.

Klijent šalje `Range`(c) kojim zahtjeva dio resourca, npr. `bytes=500-999`.

Server vraća `206 Partial Content` i `Content-Range`(s) s rangeom vraćenog resourca, npr. `bytes=500-1000`.

Ako klijent ima dio rangea, a želi ostatak (npr. ako resuma download), onda uz `Range`(c) šalje i `If-Range`(c) s datumom ili etagom. Ukoliko je resource ostao isti, dobit će `206` i dio koji nedostaje. Ako se resource promijenio, dobit će `200` i cijeli resource.

## Proxies

Proxiji rade request u ime klijenta, pa klijentova IP adresa nikad ne dođe do servera. Isto je i s reverse proxijima, gdje server ne zna koji host je klijent tražio. Da bi se te informacije prenijele, uvedeni su custom headeri koji su postali de-facto standard.

* `X-Forwarded-For` i `Client-IP`(c) IP adresa klijenta i svih prethodnih proxija u kroz koje je request prošao (npr. `129.78.138.66, 129.78.64.103`)
* `X-Forwarded-Host`(c) host i port koji je klijent tražio od reverse proxija (npr. `en.wikipedia.org:80`)
* `X-Forwarded-Proto`(c) protokol koji je klijent tražio od reverse proxija (npr. `https`)
* `Forwarded`(c) pokušaj standardiziranja gornja 3 headera. (npr. `for=192.0.2.60; proto=http; `). Ne koristi se baš.
* `Via`(c, s): verzija protokola, hostname, i optional product version proxija kroz koje je poruka prošla (npr. `1.0 fred, 1.1 example.com (Apache/1.1)`). Uglavnom za debugiranje, a služi i za detektiranje proxy loopova.

## Do Not Track

Sasvim nevjerojatno, netko je pokušao standardizirati transparentnost trackinga na webu. Browseri bi trebali omogućiti da korisnik odabere želi li da ga se tracka, pri čemu će se u svaki request dodati `DNT: 1`(c) (ne želim) ili `DNT: 0` (želim).

Serveri bi također trebali obavještavati korisnika o načinu na koji trackaju s `TSV`(s), npr. `C` - tracking with consent. Nažalost, nema zakonskih ni tehnoloških obveza da server ispoštuje korisnikov izbor.

# HTTP/2

Ako želiš raditi paralelne requestove u HTTP 1.X, moraš koristiti više TCP konekcija. Zbog request queueinga, svaka konekcija može istovremeno prenositi samo jedan request ili response. Koliki god bandwidth imao, latenciju ne možeš smanjiti dok god šalješ request po request.

HTTP/2 napravljen je da podržava sve postojeće funckionalnosti HTTP 1.1, ali da ukloni problem latencije i smanji broj potrebnih konekcija na pojedinom hostu.

## Uspostava

Negotiation protokola može se izvesti na više načina, ali najčešći je unutar TLS handshakea koristeći ALPN. HTTP/2 ne zahtjeva TLS, ali svi browseri ga trenutno tako koriste. TLS također onemogućuje razne proxije i routere koji ne znaju za HTTP/2 da prtljaju po paketima.

Umjesto toga može se koristiti `Upgrade` header, ali to zahtjeva dodatni roundtrip.

## Streams

U HTTP/2, sva komunikacija se obavlja preko jedne TCP konekcije koja može sadržavati neograničen broj dvosmjernih *streamova*. Svaki stream ima ID i koristi se za prenošenje *messagea*. Message predstavlja HTTP poruku, tj. request ili response, a sastoji se od jednog ili više binarnih *frameova*. Frame prenosi određenu vrstu podataka (npr. HTTP headere ili dio payloada), a u svom headeru sadrži ID streama, što omogućuje paralelno slanje više frameova iz različitih streamova (*interleaving*).

Zahvaljujući tome sve TCP konekcije su perzistentne, i otvara se samo jedna po originu. Time se smanjuje overhead stvaranja novih konekcija, a i memorijski i procesorski footprint na serveru, klijentima i svima između. Ista konekcija će se koristiti čak i za requestove iz različith tabova. `chrome://net-internals#http2` ispisuje listu trenutnih konekcija u browseru.

Sadržaj frameova više nije plaintext s newline delimiterima, nego je podijeljen u binarno enkodirane frameove. Binarni format je robusniji i eliminira različite interpretacije whitespacea.

Klijent može svakom streamu definirati prioritet ili ovisnost o drugom streamu, kako bi server prilagodio alokaciju CPU-a, memorije i bandwitha te osigurao optimalno slanje podataka. Browseri će automatski priotizirati bitne resurse poput HTML dokumenta, blokirajućih CSS-a i JS-a, dok će slike dohvaćati s nižim prioritetom.

## Headeri

Kako bi se smanjio overhead slanja headera HTTP/2 ih komprimira koristeći HPACK format. Također, svi headeri su lowercase.

## Flow Control

Flow control dopušta primaocu da kontrolira koliko podataka prima od pošiljatelja. Ovo je korisno u slučaju kad npr. klijent zatraži cijeli video, a korisnik ga u pola skidanja pauzira. Klijent tada može smanjiti window tog streama i omogućiti drugim streamovima da zauzmu veći bandwith. Ovo je slično kao i TCP flow control, ali omogućuje kontrolu na razini streama.

U HTTP/1.1, jednom kad je `Content-Length` poslan, nemoguće je prekinuti poruku bez da se prekina cijela konekcija, što je skupo. HTTP2 podržava prekidanje poruke pomoću RST_STREAM framea bez da se prekine konekcija.

## Server Push

*Server push* omogućuje serveru da šalje više responsa na jedan klijentov request. Na ovaj način server može poslati resurse za koje zna da će klijentu biti potrebni (npr. JS i CSS) bez da ih klijent zatraži, uštedivši na latenciji requesta.

Server prije slanje šalja uvodni frame s headerom resourca (PUSH_PROMISE) i daje priliku klijentu da odbije push (RST_STREAM) ako npr. već ima resource u cacheu.

Ovo je efektivno isto što i resource inlining, ali dodatno omogućuje zasebni caching svakog resourca.

## Early Hints

Ako server želi poslati klijentu upute da preloada neki resurs, to može učiniti s npr. headerom `Link: rel=preload`. Ali da bi taj header poslao, i dalje mora pričekati da cijeli response bude spreman, što uključuje čekanje baze i generiranje HTML-a. Server push dopušta ranije slanje, ali može se iskoristiti samo za resurse koji su dostupni server. Također, server push će početi slati resurs čak i ako klijent ima u cacheu - preloading je fleksibilniji i potrošit će manje bandwitha.

*Early hints* omogućuju serveru da pošalje headere prije konačnog responsea. Klijentu se šalje resonse sa statusom `103`, na što će klijent evaulirati headere, iskoristiti ih koliko može, i nastaviti čekati da stigne uobičajeni response.

# Literatura

* http://www.jmarshall.com/easy/http/
* http://http2-explained.haxx.se/content/en
* https://hpbn.co/http2/

