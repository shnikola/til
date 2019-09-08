# Rails Caching

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

