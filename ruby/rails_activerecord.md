# ActiveRecord

## UUID

Korištenje UUID-a olakšava skaliranje pošto se idjevi više ne generiraju sekvencijalno. Još jedna prednost je što ćeš znati id modela prije nego ga saveaš u bazu, pa ga možeš poslati u cache ili search engine prije nego se db transakcija izvrši.

`config.active_record.primary_key = :uuid` defaultno koristi UUID za sve modele.

`create_table :users, id: :uuid` koristi UUID za tablicu u migraciji.

## Associations

Kada se model persista, automatski će saveati sve `has_one` i `has_many` associatione koji još nisu persisted. Želiš li izbjeći to ponašenje, dodaj `has_one :user, autosave: false`.

## Foreign keys

`add_foreign_key :articles, :users` dodaje foreign key na `user_id` stupac tablice `articles`.

`add_foreign_key :user, :posts, on_delete: :cascade` dodaje kaskadno brisanje.

`t.references :post, foreign_key: { on_delete: :cascade }, index: true` za dodavanje pri kreiranju tablice.

Uvijek koristi foreign_key u kombinaciji s indeksom.

## Preloading

Za izbjegavanje N+1 querija, koristi neku od metoda koje preloadaju asocijacije objekta.

`post.preload(:comments)` dohvaća komentare u zasebnom queriju.
`post.eager_load(:comments)` koristi LEFT OUTER JOIN.

`post.includes(:comments)` do Railsa 5 je sam donosio odluku. Od Railsa 5 se ponaša kao `preload(:comments)`, a ako mu dodaš `.references(:comments)` kao `eager_load(:comments)`

`post.joins(:comments)` ne učitava asocijacije pa ga koristi za filtriranje.

## has_many i conditions

http://ducktypelabs.com/four-ways-to-filter-has_many-associations/

Ako želimo dohvatiti sve usere koji sudjeluju u projektu s nekim atributom
`User.joins(:projects).where(projects: { zipcode: 30332 }).uniq` ili, ako imaš scope u Project:
`User.joins(:projects).merge(Project.opened_recently).uniq`
Ovaj `uniq` je potreban jer bi u suprotnom bilo duplih usera.

Ako želimo eager-loadati koristimo includes `User.includes(:projects).where(projects: { zipcode: '30332' })`

U slučaju da se u `where` koristi sql string umjesto hasha, s `references` se navedu tablice koje treba joinati u istom queriju (a ne loadati odvojeno):
`User.includes(:projects).where('projects.deleted_at IS NOT NULL').references(:projects)`

Kad se koristi `includes`, `uniq` nije potreban.

## ActiveModel::Dirty

`obj.changed?` ako se bilo što u objektu promijenilo.
`obj.name_changed?` ako se atribut promijenio.
`obj.name_was` vraća prethodnu vrijednost atributa.
`obj.restore_attributes` undoa sve nesaveane vrijednosti.

## Validation

Sam `validates_uniqueness_of` neće te zaštiti od dvostrukih vrijednosti u tablici, jer je moguće da se dva unosa istovremeno provjere i onda dodaju u tablicu. Uvijek ga koristi u kombinaciji s pravim unique indexom na tablici, i napravi `rescue ActiveRecord::RecordNotUnique`.

## Rails 5 Attribute API

http://edgeapi.rubyonrails.org/classes/ActiveRecord/Attributes/ClassMethods.html

Za definiranje typa za neki column, umjesto da se koristi šugavi serialize.
`attribute :column_name, :integer, array: true`

Podržava custom typove i `changed_in_place?`

## size vs count vs length

`length` loada sve recorde i izračuna length arraya.
`count` uvijek radi count query.
`size` je pametan i koristi jedno ili drugo ovisno jesu li recordi loadani.

Koristi `size`! Jedino ako trebaš broj taman prije nego ćeš loadati sve recorde, koristi `length`.

Umjesto `User.count > 0` koristi `User.exists?` da db ne mora napraviti full table scan. Umjesto `User.count > 1` koristi `User.limit(2).count > 1`.

## Batches

`find_each` učitava recorde u batchevima i bloku prosljeđuje po jedan record. Korisno kako se ne bi svi recordi učitalu u memoriji odjednom. Ne dopušta korištenje `order` jer se orderaju po id-ju.

`find_in_batches` učitava recorde u batchevima i bloku prosljeđuje cijeli batch kao array.

`in_batches` učitava recorde u batchevima, ali bloku prosljeđuje batch kao `Relation` objekt, pa se na njemu može pozvati `delete_all` ili `update_all`.

## Callbacks

http://www.justinweiss.com/articles/a-couple-callback-gotchas-and-a-rails-5-fix/

Ako želiš u callbacku gurati stvari u background job (zar ne bi trebao koristiti servise?), nemoj koristiti `after_save` nego `after_commit`.

`after_save` se poziva prije nego se transakcija commitala, pa background job možda neće pronaći record u bazi.

## Connection Pool

Kako bi izbjegao otvaranje prevelikog broja konekcija, ActiveRecord koristi `ConnectionPool`. Connection pool je thread safe, i svaki thread dohvaća konekciju kada je koristi, i oslobađa je kada je gotov s njom. Pool je thread-safe i osigurava da konekciju ne mogu istovremeno koristiti dva threada.

Ako pokušaš iz poola dohvatiti konekciju dok su sve zauzete, ActiveRecord će blokirati upit i čekati dok se jedna ne oslobodi. Ako ne uspije dobiti konekciju u nekom roku (podesivo s `checkout_timeout: 5`), bacit će `ConnectionTimeoutError`.

Veličina poola se definira s `pool: 5` u `database.yml`. To ne znači da će se pri inicijalizaciji aplikacije odmah napraviti 5 konekcija, nego da će biti stvorene po potrebi.

U slučaju višeprocesnog servera, Rails stvara zasebni pool za svaki proces. TODO: Kako to radi? Koristi li se `establish_connection`?

Ako thread eksplicitno ne traži konekciju, pri prvom pozivu `ActiveRecord` metode konekcija će mu biti dodijeljena. Nakon što se response pošalje, Rails poziva `ActiveRecord::Base.clear_active_connections!` koja oslobađa sve konekcije trenutnog threada.

Ali ako stvaraš vlastite threadove unutar koda, automatsko oslobađanje se neće obaviti i pool će zauvijek ostati bez te konekcije. Zato kad god pokrećeš novi thread unutar requesta, ručno handlaj konekcije koristeći `checkout`, `checkin`ili `with_connection` s blokom. Više informacija na https://bibwild.wordpress.com/2014/07/17/activerecord-concurrency-in-rails4-avoid-leaked-connections/.

## Schema dumps

Kada deployaš aplikaciju na novi server ili je pokrećeš iznova na novom računalu, ne želiš pozivati sve migracije iznova. Umjesto toga, koristi `rake db:setup` koji izgradi bazu koristeći `db/schema.rb`.

## Schema Cache

http://blog.iempire.ru/2016/12/13/schema-cache

Rails pri pokretanju aplikacije radi `SHOW FULL FIELDS` request na bazu kako bi dohvatio informacije o svim stupcima. Ovaj request zna biti spor, pa ako imaš jako puno Unicorn procesa koje restartiraš u isto vrijeme, baza se može naći pod velikim opterećenjem.

Rješenje je pokrenuti rake `rake db:schema:cache:dump` koji će zapisati podatke o stupcima u file, kako procesi ne bi morali raditi upite na bazu.
