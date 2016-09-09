# Ruby on Rails

## Rails u produkciji
http://www.akitaonrails.com/2016/03/22/is-your-rails-app-ready-for-production
**Deploy:** Heroku (ako želiš jeftinije tipa EC2 ili Digital Ocean, treba klijentima dati do znanja da s tim ne dobijaju 24/7 security updates)
**Server:** Passenger ili Puma (Unicorn ima problem sa sporim klijentima)
**Metrics:** New Relic, Papertrail
**Rails Dependencies:** Rails 12 Factor, Rack-Cache middleware, Rack-Attack, Rack-Protection, Sprockets 3.3+
**DB:** Postgresql, male Redis instance uz Sidekiq (workere podesi da koriste readonly Follower bazu)
**CDN:** Koristi ih! AWS Cloudfront ili Fastly.
**Upload:** Attachinary gem (direktni upload iz klijenta na servis koji zaobilazi Rails app, samo dobiješ id)


## Rack
`Rack` je univerzalni adapter između servera (`unicorn`, `puma`) i frameworka (`rails`, `sinatra`).
* Server parsira request.
* Šalje ga racku kroz varijablu u `call(env)`
* Rack vraća array `['200', {'Content-Type' => 'text/html'}, ['Response Body.']]` sa statusom, headerima i bodyjem.


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
  * **MemoryStore** je najbrži, ali jede RAM i nemožeš ga dijeliti ni među procesima, ni među serverima. Koristi ga ako imaš malo caching potreba (< 20MB). Ludo brzo optimizirana verzija toga je **LRURedux**.
  * **FileStore** je malo sporiji, i ne možeš ga dijeliti među serverima. Također ne radi na Herokuu. Koristi ga ako imaš mali request load, a veliku caching potrebu (> 100MB)
  * **Memcache** je spor preko networka (računaj oko 20ms bar), ali je distribuiran. Koristi ga ako imaš više servera. Alternativa je **Redis** kojem možeš definirati drugačija pravila expiranja, i može se dumpati na disk pa ga je lakše restartirati.


## Conditional GET
Za ispravan odgovor na klijetnove `HTTP_IF_NONE_MATCH` i `HTTP_IF_MODIFIED_SINCE` headere koristi:
* `if stale?(@product)` oko rendera. `else` nije potreban, automatski će se vratiti `:not_modified`.
* `if stale?(last_modified: @product.updated_at.utc, etag: @product.cache_key)` ako želimo sami birati.
* ako ne pozivaš render, upotrijebi deklarativni `fresh_when(@product)`
* po defaultu se koristi *weak etagovi* koji garantiraju semantičku ekvivalentnost (dobro za HTML).
* s `strong_etag` koriste se *strong etagovi* koji garantiraju byte-za-byte ekvivalentnost (dobro za `Range` requestove na PDF i video, ili za CDNove koji podržavaju samo strong etagove).




## Amazon Cloudfront
Serviranje static asseta bezveze umara naše servere, želimo da to netko drugi radi. S3 je ok, ali je napravljen za storage, a ne za delivery. Rješenje je cloudfront - daš mu origin domenu, i samo postaviš
`config.action_controller.asset_host = "<YOUR DISTRIBUTION SUBDOMAIN>.cloudfront.net"`
Prvi request na asset cloudfront će proslijediti na origin domenu (naš server), a svi ostali će biti cachirani. Samo pripazi da fileovi imaju hash u imenu kako ne bi bili stale.


## Rails 5 Attribute API
http://edgeapi.rubyonrails.org/classes/ActiveRecord/Attributes/ClassMethods.html
Za definiranje typa za neki column, umjesto da se koristi šugavi serialize.
`attribute :column_name, :integer, array: true`
Podržava custom typove i `changed_in_place?`


## has_many i conditions
http://ducktypelabs.com/four-ways-to-filter-has_many-associations/
Ako želimo dohvatiti sve usere koji sudjeluju u projektu s nekim atributom
`User.joins(:projects).where(projects: { zipcode: 30332 }).uniq` ili, ako imaš scope u Project:
`User.joins(:projects).merge(Project.opened_recently).uniq`
Ovaj `uniq` je potreban jer bi u suprotnom bilo duplih usera.

Ako želimo eager-loadati koristimo includes
`User.includes(:projects).where(projects: { zipcode: '30332' })`
U slučaju da se u `where` koristi sql string umjesto hasha, s `references` se navedu tablice koje treba
joinati u istom queriju (a ne loadati odvojeno):
`User.includes(:projects).where('projects.deleted_at IS NOT NULL').references(:projects)``
Kad se koristi `includes`, `uniq` nije potreban.


## Rails Console
`app` - trenutni session instance, ima `app.get('/projects/6')` i `app.project_path(Project.first)`
`helper` - svi view helperi, npr. `helper.link_to` i `helper.truncate`


## Turbolinks 5
Turbolinks 5 je full rewrite, ajd prouči ga.
**TODO**
