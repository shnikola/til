# HTTP 1.1
http://www.jmarshall.com/easy/http/

Svaka HTTP poruka ima jednaki format:
```
<inicijalna linija, različita za request i response>
Header1: value1
Header2: value2

<optional message body, u kojem god formatu>
```
Inicijalna linija za request: `GET /path/to/file/index.html HTTP/1.1`
Inicijalna linija za response: `HTTP/1.1 200 OK`


## Headeri:
- `Host`(c): jedini required za klijenta. Zahvaljujući njemu više domena može živjeti na istoj IP adresi
- `Date`(c,s): vrijeme kad je poruka poslana. Required za server.
- `Content-Type`(c,s): format bodija u messageu
- `Content-Length`(c,s): broj byteova u bodiju
- `User-Agent`(c): identificira klijenta
- `Server`(s): ime servera
- `Referer`(c): adresa stranice s koje se slijedio link na trenutni request.
- `Link`(s): odnos s drugim resourcom. Google SEO koristi `<http://domain.com/original-post.pdf>; rel="canonical"``
- `Location`(s): u slučaju redirecta, na koju adresu ići
- `Retry-After`(s): u slučaju 503 Service Unavailable, vrijeme nakon kojeg da klijent pokuša opet.
- `Upgrade`(c,s): zahtjev klijentu/serveru da se pređe na drugi protokol (npr. HTTPS, HTTP2)

**Methods:**
`GET` dohvaća resource, bez side effecta.
`HEAD` dohvaća samo headere, a ne i body. Korisni za saznati podatke o resourcu bez da ga skineš.
`POST` šalje podatke vezane za resource.
`PUT` šalje cijeli resource.
`PATCH` šalje promjene koje treba aplicirati na resourcu.
`DELETE` briše resource.
`TRACE` vraća request koji je primio, koristi za debugging.
`OPTIONS` vraća metode koje server podržava za taj resource.
`CONNECT` šalje zahtjev proxiju da napravi HTTP tunel do zadane domene. Koristi se za uspostavljanje HTTPS konekcije.

- `Allow`(s): u slučaju 405 Method not allowed, daje popis dopuštenih metoda (npr. `GET, HEAD`)
- `Accept-Patch`(s): koji content-type je dozvoljen za PATCH (tj. kako su formatirane instrukcije za promjenu)
- `Max-Forwards`(c): koristi se samo uz TRACE, ograničava koliko se puta poruka može forwardati kroz proxije.

**Chunked** response omogućuje serveru da šalje podatke bez da unaprijed zna njihovu veličinu
- `Transfer-Encoding`(s): `chunked` za slanje u chunkovima. Primjer poruke:
- `Trailer`(s): lista fieldova koja će se naći u traileru poruke (npr checksumi i sl.)
```
HTTP/1.1 200 OK
Date: Fri, 31 Dec 1999 23:59:59 GMT
Content-Type: text/plain
Transfer-Encoding: chunked

1a [veličina chunka]
abcdefghijklmnopqrstuvwxyz [chunk]
10
1234567890abcdef
0

Trailer1: value1
```

**Persistent connection:** Otvaranje i zatvaranje TCP konekcije zahtjeva znatan dio CPU vremena, bandwitha, i memorije. Većina web stranica uključuje više fileova na istom serveru, pa treba omogućiti razmjenu više requestova i responsa preko iste TCP konekcije. U HTTP 1.1 to je defaultno uključeno.
- `Connection`(c,s): `keep-alive` za održati konekciju, `close` za zatvoriti nakon responsa.

**Content negotiation:** na istom URLu može se naći više verzija istoj resursa. Klijent može zatražiti određenu verziju uz oznaku quality factora: `de; q=1.0, en; q=0.5`
- `Accept`(c): koji `Content-Type` očekuje (npr. `text/plain`)
- `Accept-Encoding`(c): koji content encoding client podržava.
- `Content-Encoding`(s): encoding korišten na podatcima (`gzip`,`deflate`,`compress`...). Zapravo bi se za ovo trebao koristiti `Transfer-Encoding`, ali browseri nisu ispravno shvatili standard.
- `Accept-Language`(c): koji jezik user preferira. Ok za hint, ali treba omogućiti usera da overrida vrijednost s nekom drugom (npr. parametar ili cookie).
- `Content-Language`(s): jezik contenta
- `Content-Disposition`(s): način na koji će se resource prikazati u klijentu. Koristi se uglavnom za direktni download (`attachment; filename="fname.ext"`)

- `Vary`(s): Lista klijentovih headera koji su korišteni za serviranje sadržaja. Na taj način klijent zna promjena kojih headera invalidira cache. Nije baš najsigurnije za prčkati, ali dobro je znati da postoji i da te može sjebati.

**Cookies:**
- `Set-Cookie`(s): daje upute za postavljanje jedne key-value vrijednosti (npr. `sessionToken=abc123`). Dodatni atributi su `Domain`, `Path`, `Expires`/`Max-Age`, `Secure` (šalje se samo preko HTTPSa), `HttpOnly` (ne može se pristupiti JSom)
- `Cookie`(c): skup svih cookija postavljenih za trenutnu domenu

**Cache:**
- `Cache-Control`(s): Tko može cachirati resource, pod kojim uvjetima, i koliko dugo.
- `Last-Modified`(s): Kad je zadnji put resource promijenjen.
- `If-Modified-Since`(c): Zadnji datum promjene cachiranog resourca. Poruka serveru da vrati `304` ako se nije promijenio.
- `If-Unmodified-Since`(c): Poruka serveru da vrati error ako se promijenio. Korisno ako radiš PUT, a ne želiš pregaziti nečije promjene.
- `ETag`(s): Entity tag, najčešće hash resourca.
- `If-None-Match`(c): ETag cachiranog resourca. Poruka serveru da vrati `304` ako se nije promijenio.
- `If-Match`(c): Poruka serveru da vrati error ako se promijenio
- `Age`(s): koliko dugo je resource u proxy cacheu (u sekundama)

**Range**: Omogućava parcijalno dohvaćanje filea sa servera (npr. video od 2. minute). Server vraća 206 Partial Content.
- `Accept-Ranges`(s): daje do znanja da podržava range requestove za resource
- `Range`(c): Zahtjeva dio resourca (npr. `bytes=500-999`)
- `Content-Range`(s): Dolazi uz 206, označava koji dio resourca je u responseu (npr. `bytes=500-1000`)
- `If-Range`(c): Ako klijent ima dio rangea, a želi ostatak, ovdje šalje etag ili datum s `Range` headerom. Ako se resource nije promijenio, dobit će dio koji nedostaje. Ako se resource promijenio, dobit će cijeli.

**Proxies:** Proxiji rade request u ime klijenta, pa klijentova IP adresa nikad ne dođe do servera. Isto je i s reverse proxijima, gdje server ne zna koji host je klijent tražio. Da bi se te infomracije prenijele, uvedeni su custom headeri koji su postali de-facto standard
- `X-Forwarded-For`(c): IP adresa klijenta i svih prethodnih proxija u kroz koje je request prošao (npr. `129.78.138.66, 129.78.64.103`)
- `X-Forwarded-Host`(c): Host i port koji je klijent tražio od reverse proxija (npr. `en.wikipedia.org:80`)
- `X-Forwarded-Proto`(c): Protokol koji je klijent tražio od reverse proxija (npr. `https`)
- `Forwarded`(c): pokušaj standardiziranja gornja 3 headera. (npr. `for=192.0.2.60; proto=http; `). Ne koristi se baš.
- `Via`(c, s): verzija protokola, hostname, i optional product version proxija kroz koje je poruka prošla (npr. `1.0 fred, 1.1 example.com (Apache/1.1)`). Uglavnom za debugiranje, a služi i za detektiranje proxy loopova.

**CORS:**
Slanje advanced requestova (POST, PUT, DELETE) i custom headera iz AJAXa na resource s druge domene nije dopušteno. Time se sprječava zloupotreba cookija i autentifikacije sitea na drugoj domeni (npr. skripta na zlom siteu POSTa sliku kurca na tvoj facebook). To se zove **Same Origin Policy**.
CORS je način da klijent i server na siguran način mogu utvrditi je li dopušten cross origin request.

Ako je request zajeban (sve osim GET, HEAD i POST bez custom headera), šalje se preflight OPTIONS request na server.
- `Origin`(c): domena s koje dolazim
- `Access-Control-Request-Method`(c): koju metodu planiram pozvati
- `Access-Control-Request-Headers`(c): koje headere planiram koristiti
Server odgovara s:
- `Access-Control-Allow-Origin`(s): dopuštena domena, `*`, ili error ako nije dopušten
- `Access-Control-Allow-Methods`(s): dopuštene metode
- `Access-Control-Allow-Headers`(s): dopušteni headeri
- `Access-Control-Max-Age`(s): koliko dugo ovaj preflight response može biti cachiran
Nakon toga se šalje pravi request, opet s `Origin` headerom.

Za slanje cookija i authentifikacije preko AJAXa, potrebno je dodati `withCredentials = true` flag.
Browser će pritom odbaciti svaki response koji nema
- `Access-Control-Allow-Credentials`(s): `true`

**Basic Authentication:** server vraća `HTTP 401 Unauthorized` response i `WWW-Authenticate` header.
- `WWW-Authenticate`(s): shema za autorizaciju (npr. `Basic`)
- `Authorization`(c): Shema za autorizaciju i credentialsi. Za Basic to su Base64-ani `username:password`, dakle nimalo sigurno slanje podataka.
- `Proxy-Authenticate`(s): shema za autorizaciju na proxy (npr. `Basic`)
- `Proxy-Authorization`(c): shema i credentialsi za proxy

**Do Not Track:**
 - `DNT`(c): Ako je 1, korisnik ne želi da ga se tracka. Ako je 0, pristaje na to. Ako je null, header se ne šalje.
 - `TSV`(s): Tracking status value, kako trenutno server tracka. (npr. `C` - tracking with consent)
Nažalost, nema zakonskih ni tehnoloških obveza da server ispoštuje korisnikov izbor. Plus, još nije dio standarda.
