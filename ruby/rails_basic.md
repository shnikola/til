# Ruby on Rails

## Request/Response Cycle

http://www.rubypigeon.com/posts/examining-internals-of-rails-request-response-cycle

* Rack entry point definiran je u `config.ru`, gdje se poziva `Rails.application`, instanca tvoje aplikacije.
* Rack poziva `call(env)` aplikacije koji radi `build_request(env)` (dodaje hrpu stvari u `env`) i prosljeđuje ga middleware stacku.
* Request se prosljeđuje kroz svaki middleware, i svaki doda nešto u `env`, npr. deserijalizirane cookije ili logiranje.
* Nakon middlewarea ide se u router. `env` se wrapa u `ActionDispatch::Request` objekt, a stvara se prazni `ActionDispatch::Response` objekt.
* Odabire se controller i action te poziva `controller_class.dispatch(action, request, response)`
* Svaki controller može imati vlastiti middleware stack, pa se i kroz njega prolazi.
* Za svaki request controller se instancira i poziva se `dispatch` nad objektom, koji zapisuje `request` i `response`, te poziva kod u akciji koji si napisao.
* `render` postavlja `response.body` i `Content-Type` header.
* Vraća se u controller gdje `response.to_a` dobija rack array koji prosljeđuje routeru, pa nazad kroz sav middleware. Tu se postavlja `ETag`, serijaliziraju se cookiji itd.
* Web Server serijalizira rack array u HTTP response string i šalje ga nazad klijentu.

## Basic Caching

**Fragment Caching** omogućuje cachiranje dijela html stranice.
`cache @product do` spremit će rezultat bloka pod keyem `views/products/<id>-<updated_at>/<md5 fragmenta>`. Key će se promijeniti ako se product ili fragment promijene.
`cache_if` ili `cache_unless` ako želiš uvjetno koristiti caching.
`cache_key` je metoda objekta koju `cache` koristi. Za ActiveRecord ona vraća `<id>-<updated_at>`.

**Russian Doll Caching** omogućuje da caching fragmenti u sebi imaju druge caching fragmente.
U slučaju `Post` `has_many :comments`:
  * ukoliko se jedan `comment` updatea, `post` se neće updateati i cache će postati stale.
  * potrebno dodati u `Comment`: `belongs_to :post, touch: true`.

**SQL Caching** sprema rezultate svakog SQL querija, i vraća ih ako se taj query ponovi *u istom requestu*.

## Cache Stores

Rails koristi *key based cache* - umjesto da eksplicitno expira value, promijenjeni objekt dobije novi key. U slučaju da se cache napuni, prvo će se odbaciti najdavnije korišteni - znači ovi pod starim keyevima.

**MemoryStore** je najbrži, ali jede RAM i nemožeš ga dijeliti ni među procesima, ni među serverima. Koristi ga ako imaš malo caching potreba (< 20MB). Ludo brzo optimizirana verzija toga je **LRURedux**.

**FileStore** je malo sporiji, i ne možeš ga dijeliti među serverima. Također ne radi na Herokuu. Koristi ga ako imaš mali request load, a veliku caching potrebu (> 100MB)

**Memcache** je spor preko networka (računaj oko 20ms bar), ali je distribuiran. Koristi ga ako imaš više servera. Alternativa je **Redis** kojem možeš definirati drugačija pravila expiranja, i može se dumpati na disk pa ga je lakše restartirati.

## Conditional GET

Za ispravan odgovor na klijetnove `HTTP_IF_NONE_MATCH` i `HTTP_IF_MODIFIED_SINCE` headere koristi:
* `if stale?(@product)` oko rendera. `else` nije potreban, automatski će se vratiti `:not_modified`.
* `if stale?(last_modified: @product.updated_at.utc, etag: @product.cache_key)` ako želimo sami birati.
* ako ne pozivaš render, upotrijebi deklarativni `fresh_when(@product)`
* po defaultu se koristi *weak etagovi* koji garantiraju semantičku ekvivalentnost (dobro za HTML).
* s `strong_etag` koriste se *strong etagovi* koji garantiraju byte-za-byte ekvivalentnost (dobro za `Range` requestove na PDF i video, ili za CDNove koji podržavaju samo strong etagove).

## Rails Gems

Bundler omogućuje da aplikacija koristi specifičnu verziju gema, makar je na računalu instalirano više verzija.

U `config/boot.rb` poziva se `bundler/setup`. On iz `$LOAD_PATH` uklanja pathove do svih gemova koje je RubyGems dodao, i dodaje samo one koji se nalaze u Gemfileu.

U `config/application.rb` poziva se `Bundler.require(*Rails.groups)` koji poziva `require` za svaki od gemova u Gemfileu. Zato nije potrebno pozivati `require` posebno unutar aplikacijskog koda.

## Rails Autoloading

Rails koristi autoloading iz dva razloga:
* da izbjegneš duplikaciju pisanja `require` na početku svakog filea
* da omogući reload promjenjenog koda koji se već requireao.

Kada se u kodu koristi konstanta `Post` koji ne postoji, Rails će potražiti file `post.rb` među pathovima u `autoload_paths`. Po defaultu tamo su svi subdirectoriji u `app/`, čak i custom poput `app/services`. U slučaju da je konstanta `Post` unutar nestinga (`[Admin::BaseController, Admin]`), treba provjeriti sve kombinacije fileova (`admin/base_controller/post.rb`, `admin/post.rb` i `post.rb`) u svim pathovima.

Za namespaceove koje nemaju definirani file (npr. `Admin::UsersController` bez `admin.rb`), Rails će stvoriti prazni module on-the-fly.

Ako je `config.cache_classes = false` (development), Rails prati promjene u `autoload_paths`, `config/routes.rb`, `db/schema.rb` i `config/locales`. Ako se ijedan file promijeni, pomoću `remove_const` briše sve konstante kako bi se iznova autoloadale u idućem requestu.

U produkciji aplikacijski fileovi se eager loadaju, i autoloading je od Rails 5 disabled (između ostalog jer nije thread-safe). Zato sve posebne foldere dodaj u `eager_load_paths`, jer će automatski biti dodani i u `autoload_paths` - obratno ne vrijedi.

Railsov autoload konstanti ne koristi `autoload` metodu iz Rubyja iz više razloga (npr. `autoload` nije thread safe, a usto i interno koristi `require`, pa se već učitani fileovi ne bi mogli reloadati). Umjesto toga, koristi `ActiveSupport::Dependencies` koji se veže na `const_missing` i ima vlastiti algoritam traženja filea.

Savjeti:
* Rails ne može znati je li neloadana konstanta bila relative (`module Admin; class UsersController`) ili qualified (`class Admin::UsersController`), pa se neće ponašati isto kao čisti Ruby. Da izbjegneš probleme, uvijek koristi *relative nesting*.
* Nikad ne stavljaj `require` konstanti koje će se autoloadati - samo ćeš ga zbuniti. `require` 3rd party librarija je ok.

## force_ssl

`force_ssl` u controlleru će redirectati svaki HTTP request na HTTPS.

`config.force_ssl = true` će postaviti redirect, a usto i postaviti sve cookije i session da budu Secure (da se ne šalju s HTTP requestovima), te podesiti HSTS headere. Detalji se mogu podesiti s `config.ssl_options`

## Remote IP

`request.ip` koristi `HTTP_CLIENT_IP` i `HTTP_X_FORWARDED_FOR` headere da odredi IP adresu klijenta. Oba su nestandardna headera koje postavljaju proxiji.

`request.remote_ip` radi istu stvar, ali još dodatno provjerava da li je neka od IP adresa spoofana pomoću `ActionDispatch::RemoteIp` middlewarea. Ponekad se dogodi da jedan proxy postavi `CLIENT_IP` header, a drugi `X_FORWARDED_FOR`. U tom slučaju, aplikacijski server ne može znati koja adresa je klijentova, pa po defaultu baca `IpSpoofAttackError`. Ukoliko ne radiš autentifikaciju po IP-u, najjednostavnije je isključiti ovu provjeru pomoću `config.action_dispatch.ip_spoofing_check = false`.

## Time Zones

U Rails aplikaciji postoje 3 različite time zone: sistemsko vrijeme, aplikacijsko vrijeme i database vrijeme.

`Time.now` vraća sistemsko vrijeme procesa. Izbjegavaj ga jer nisi siguran koji je time zone podešen na serveru.

`config.time_zone = ` postavlja aplikacijski time zone, defaultni je `UTC`.
`Time.zone = "Fiji"` postavlja time zone za trenutni thread.
`Time.use_zone(user_zone) do ... end` koristi specifičnu zonu samo unutar bloka.

`Time.zone.now` (`Time.current`) vraća trenutno vrijeme u aplikacijskoj time zoni.
`Time.zone.today` (`Date.current`) vraća današnji datum u aplikacijskoj time zoni.
`Time.zone.parse("...")` parsira vrijeme u aplikacijskoj time zoni.
`Time.zone.local(2015, 12, 1)` stvara novo vrijeme (umjesto `Time.new`).

Za konvertiranje između zona koristi `time.in_time_zone`, ali nemoj ga koristiti za inicijaliziranje datuma. Npr:
* `Date.new(2015, 1, 1).to_time.in_time_zone` prvo pretvara datum u sistemsku zonu (`2015-01-01 00:00:00 +0100`), i onda nju konvertira u aplikacijsku zonu (`2014-31-12 23:00:00 +0000`). To najčešće ne želiš.
* `Date.new(2015, 1, 1).in_time_zone` direktno konvertira u aplikacijsku zonu (`2015-01-01 00:00:00 +0000`)

Pri spremanju u bazu, postoje dvije situacije. Stupci koji ne podržavaju time zone (npr. `timestamp` u MySQL) će prebaciti `Time` objekt u `UTC`. Stupci koji podržavaju time zone (npr. `timestampz` u Postgresu) će spremiti objekt zajedno s time zonom. Ovo je korisno spremati ako ti je bitno u kojoj zoni se nešto dogodilo, ali najčešće nije potrebno.

Pri dohvatu iz baze, `Time` objekti će se defaultno vraćati u `UTC` time zoni. Ako želiš da se vraćaju u aplikacijskoj time zoni, postavi `config.active_record.default_timezone = :local`.

Za serijalizaciju ili slanje kroz API koristi `time.iso8601` koji pretvara vrijeme u string i čuva time zone. `Time.iso8601("...")` parsira takav string.

Ukratko: koristi `UTC` kao aplikacijski time zone, a `Time.zone` i `Time.current` umjesto `Time.now`.

## Turbolinks 5

Pri kliku na link dohvaća se nova stranica, i njen `body` se postavi kao `document.body`. Klijent tako preuzima single process model - ne mora reloadati i reprocesirati assete pri učitavanju svake stranice, niti uspostavljati websocket konekcije.

Novi turbolinksi također imaju adaptere omogućuju ubacivanje dijelova weba u nativne iOS i Android aplikacije.

Nažalost, turbolinksi sa sobom donose mnogo suptilnih komplikacija (treba koristiti `turbolinks:load` umjesto `window.onload`; updatanje asseta; third-party JS librariji koji očekuju da stvari rade normalno) da ih se ne isplati koristiti.
