# Ruby on Rails

## Rails u produkciji
http://www.akitaonrails.com/2016/03/22/is-your-rails-app-ready-for-production

- **Deploy:** Heroku (ako želiš jeftinije tipa EC2 ili Digital Ocean, treba klijentima dati do znanja da s tim ne
  dobijaju 24/7 security updates)
- **Server:** Passenger ili Puma (Unicorn ima problem sa sporim klijentima)
- **Metrics:** New Relic, Papertrail
- **Rails Dependencies:** Rails 12 Factor, Rack-Cache middleware, Rack-Attack, Rack-Protection, Sprockets 3.3+
- **DB:** Postgresql, male Redis instance uz Sidekiq (workere podesi da koriste readonly Follower bazu)
- **CDN:** Koristi ih! AWS Cloudfront ili Fastly.
- **Upload:** Attachinary gem (direktni upload iz klijenta na servis koji zaobilazi Rails app, samo dobiješ id)


## Cache
Rails koristi key based cache - umjesto da expira value, on promijenjeni objekt dobije novi key. U slučaju da se memcached napuni, prvo će se odbaciti najdavnije korišteni - znači ovi pod starim keyevima.

- **MemoryStore** je najbrži, ali jede RAM i nemožeš ga dijeliti. Koristi ga ako imaš malo caching potreba (< 20MB). Ludo brzo optimizirana verzija toga je **LRURedux**.
- **FileStore** je malo sporiji, i ne možeš ga dijeliti među serverima. Također ne radi na Herokuu. Koristi ga ako imaš mali request load, a veliku caching potrebu (> 100MB)
- **Memcache** je spor preko networka (računaj oko 20ms bar), ali je distribuiran. Koristi ga ako imaš više servera. Alternativa je **Redis** kojem možeš definirati drugačija pravila expiranja, i može se dumpati na disk pa ga je lakše restartirati.


## Thread safety
https://bearmetal.eu/theden/how-do-i-know-whether-my-rails-app-is-thread-safe-or-not/

GIL ne dopušta da se dva threada istovremeno izvršavaju, ali to ne znači da je kod thread-safe. Thread se i dalje može pauzirati u bilo kojem trenutku, i drugi mu sjebat dijeljene podatke.

Rails i njegovi dependenciji su thread-safe. Kriv je tvoj kod! Ili gemovi.
Railsova arhitektura je takva da se po defaultu ništa ne dijeli između threadova, ali može se desiti da nešto slučajno podijelimo kroz:
* globalne varijable
* class varijable (koje su principu isto što i globalne)
* class instance varijable (opet, klase se dijele, pa tako i njihove instance varijable)
* konstante (ako ih nedajbože mijenjaš u kodu)
* memoizaciju - koristi različita imena `@ivar`ova ako memoiziraš sa super: https://github.com/rails/rails/pull/9789/files


## Rails 5 Attribute API
http://edgeapi.rubyonrails.org/classes/ActiveRecord/Attributes/ClassMethods.html

Za definiranje typa za neki column, umjesto da se koristi šugavi serialize.
`attribute :column_name, :integer, array: true`
Podržava custom typove i changed_in_place?


## Url escaping

Url helperi escapaju parametre s `CGI.escape` (escapa točkuzarez, ampersand i sve),
a sve ostalo s `URI.escape` (ne escapa ove stvari koje se mogu pojaviti u uriju)


## Amazon Cloudfront

Serviranje static asseta bezveze umara naše servere, želimo da to netko drugi radi. S3 je ok, ali je napravljen za storage, a ne za delivery. Rješenje je cloudfront - daš mu origin domenu, i samo postaviš
`config.action_controller.asset_host = "<YOUR DISTRIBUTION SUBDOMAIN>.cloudfront.net"`
Prvi request na asset cloudfront će proslijediti na origin domenu (naš server), a svi ostali će biti cachirani. Samo pripazi da fileovi imaju hash u imenu kako ne bi bili stale.


## Debugging memory usage on Heroku
http://blog.codeship.com/debugging-a-memory-leak-on-heroku

Najvjerojatnije ti app ne leaka memoriju, nego samo koristi puno. Vatrogasna rješenja: smanjiti broj workera u Pumi i dodati `puma_worker_killer`. Za analizu koristi derailed gem:
`derailed bundle:mem` - analizira Gemfile
`derailed exec perf:objects` - koje linije alociraju najviše objekata, dodaj ALLOW_FILES za grep filter fileova
`derailed exec perf:mem_over_time` - za traženje leakova, kopipejstaj rezulat u google spreadsheets za graf


## JSON API Schema
Kako validirati JSON podatke koji dolaze u API? strong_params su meh - ne provjeravaju tip, kod se drži u controlleru. Loš data ne bi uopće trebao doću do app layera. Postoji format definiranja ulaznih (i izlaznih) podataka koji se zove JSON Schema. Todo: Pogledaj njegove integracije u railsu.


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
