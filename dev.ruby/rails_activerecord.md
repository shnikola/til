# ActiveRecord

## UUID

Korištenje UUID-a olakšava skaliranje pošto se idjevi više ne generiraju sekvencijalno. Još jedna prednost je što ćeš znati id modela prije nego ga saveaš u bazu, pa ga možeš poslati u cache ili search engine prije nego se db transakcija izvrši.

`config.active_record.primary_key = :uuid` defaultno koristi UUID za sve modele.

`create_table :users, id: :uuid` koristi UUID za tablicu u migraciji.

## Foreign keys

`add_foreign_key :comments, :posts, on_delete: :cascade` dodaje foreign key constraint na `post_id` stupac tablice `comments`.
`t.references :post, foreign_key: { on_delete: :cascade }, index: true` za dodavanje pri kreiranju tablice.

Uvijek koristi `foreign_key` u kombinaciji s indeksom. Uvijek koristi `on_delete` za ponašanje ako se parent obriše: `:restrict` (default) baca error, `:cascade` briše child red, `:nullify` postavlja foreign key vrijednost na NULL. `on_update` nije potreban jer se primary key u parentu rijetko mijenja.

## Association persisting

Kada se assigna `belongs_to` asocijacija (npr. `comment.post = post`), ni child ni parent se neće automatski perzistirati u bazu.

Kada se assigna `has_one` ili `has_many` asocijacija, (npr. `post.comments = comments`) novi child objekti se automatski saveaju s novim foreign keyem. Prethodni child objekti se također perzistiraju: foreign key im se postavlja na `nil`, ili se brišu ako je postavljen `dependent: :destroy`. Ako parent još nije perzistiran, child objekti će se saveati tek kada se i parent savea.

`post.comments << comment` (ili `post.comments.push`) dodaje novi child objekt i automatski ga perzistira.

Za dodavanje asocijacija bez automatskog saveanja, koristi `post.comments.build`.

## Bi-direction associations i inverse_of

`post` i `post.comments.first.post` bi trebali biti isti objekt. Kada se `post.name` promijeni, to bi trebalo biti vidljivo i u `comment.post.name`.

Rails (od verzije 4) automatski povezuje ovakve asocijacije gdjegod može. Ipak, u slučajevima kad se koriste opcije `foreign_key`, `primary_key` ili `class_name` potrebno je pomoći i dodati `inverse_of` opciju, npr. `belongs_to :post, inverse_of: :comments`.

## Preloading

Za izbjegavanje N+1 querija, koristi neku od metoda koje preloadaju asocijacije objekta.

`posts.preload(:comments)` dohvaća komentare u zasebnom queriju.
`posts.eager_load(:comments)` koristi LEFT OUTER JOIN.

`posts.includes(:comments)` do Railsa 5 je sam donosio odluku. Od Railsa 5 se ponaša kao `preload(:comments)`, a ako mu dodaš `.references(:comments)` kao `eager_load(:comments)`

Ne koristi scope na asocijacijama, npr. `post.comments.active` jer se ne preloadaju. Umjesto toga, napravi novu asocijaciju koju onda možeš preloadati, npr. `has_many :active_comments, -> { active }, class_name: "Comment"`.

Ne stavljaj query metode poput `where` i `find` u instance metode modela. Prije ili kasnije ćeš ih pozvati unutar petlje, i onda će se napraviti N+1 upit.

## has_many i conditions

Ako želimo dohvatiti sve usere koji sudjeluju u projektu s nekim atributom
`User.joins(:projects).where(projects: { zipcode: 30332 }).distinct` ili, ako imaš scope u Project:
`User.joins(:projects).merge(Project.opened_recently).distinct`
Ovaj `distinct` je potreban jer bi u suprotnom bilo duplih usera.

Ako želimo eager-loadati koristimo includes `User.includes(:projects).where(projects: { zipcode: '30332' })`

U slučaju da se u `where` koristi sql string umjesto hasha, s `references` se navedu tablice koje treba joinati u istom queriju (a ne loadati odvojeno):
`User.includes(:projects).where('projects.deleted_at IS NOT NULL').references(:projects)`

Kad se koristi `includes`, `distinct` nije potreban.

## has_many i delete

`user.posts.delete_all` po defaultu neće obrisati sve postove, samo će im postaviti `user_id` na NULL. Da bi brisanje radilo, potrebno je postaviti `has_many :posts, dependent: :delete_all`.

## Brojanje i postojanje

`length` loada sve recorde i izračuna length arraya.
`count` uvijek radi count query.
`size` je pametan i koristi jedno ili drugo ovisno jesu li recordi loadani.

Koristi `size`. Jedino ako trebaš broj taman prije nego ćeš loadati sve recorde, koristi `load.size`.

`present?` i `blank?` loada sve recorde i napravi upit na arrayu.
`exists?` uvijek radi count query

Umjesto `User.count > 0` koristi `User.exists?` da db ne mora napraviti full table scan. Umjesto `User.count > 1` koristi `User.limit(2).count > 1`.

## Batches

`find_each` učitava recorde u batchevima i bloku prosljeđuje po jedan record. Korisno kako se ne bi svi recordi učitalu u memoriji odjednom. Ne dopušta korištenje `order` jer se orderaju po id-ju.

`find_in_batches` učitava recorde u batchevima i bloku prosljeđuje cijeli batch kao array.

`in_batches` učitava recorde u batchevima, ali bloku prosljeđuje batch kao `Relation` objekt, pa se na njemu može pozvati `delete_all` ili `update_all`.

## Bulk

`User.insert_all([{id: 1, name: "A"}, {id: 2, name: "B"}])` inserta više recorda u bazu odjednom. Recordi koji su već u bazi se ignoriraju.

`User.upsert_all([{id: 1, name: "A"}, {id: 2, name: "B"}])` inserta više recorda u bazu odjednom. Recordi koji su već u bazi se updateaju.

Za Rails verzije prije 6, koristi `activerecord-import` gem.

## Callbacks

Ako želiš u callbacku gurati stvari u background job (što ionako ne bi trebao), nemoj koristiti `after_save` nego `after_commit`. `after_save` se poziva prije nego se transakcija commitala, pa background job možda neće pronaći record u bazi.

## Only

`User.order(:name).only(:where)` discarda sve uvjete querija osim danih. Korisno za librarije.

## Reloading

Jednom kad se objekt iz baze učita u memoriju, vrijednosti atributa se cachiraju. Ako je netko u drugom requestu zapisao nove vrijednosti u bazu, objekt možeš reloadati s `comment.reload`. Isto vrijedi i za asocijacije, npr. `post.comments.reload`.

## ActiveModel::Dirty

`obj.changed?` ako se bilo što u objektu promijenilo.
`obj.name_changed?` ako se atribut promijenio.
`obj.changed_from_active?` ako je u pitanju enum koji je bio `:active`.
`obj.name_was` vraća prethodnu vrijednost atributa.
`obj.restore_attributes` undoa sve nesaveane vrijednosti.

## Validation

Sam `validates_uniqueness_of` neće te zaštiti od dvostrukih vrijednosti u tablici, jer je moguće da se dva unosa istovremeno provjere i onda dodaju u tablicu. Uvijek ga koristi u kombinaciji s pravim unique indexom na tablici, i napravi `rescue ActiveRecord::RecordNotUnique`.

## Rails 5 Attribute API

http://edgeapi.rubyonrails.org/classes/ActiveRecord/Attributes/ClassMethods.html

Za definiranje typa za neki column, umjesto da se koristi šugavi serialize.
`attribute :column_name, :integer, array: true`

Podržava custom typove i `changed_in_place?`

## Connection Pool

Kako bi izbjegao otvaranje prevelikog broja konekcija, ActiveRecord koristi `ConnectionPool`. Connection pool je thread safe, i svaki thread dohvaća konekciju kada je koristi, i oslobađa je kada je gotov s njom. Pool je thread-safe i osigurava da konekciju ne mogu istovremeno koristiti dva threada.

Ako pokušaš iz poola dohvatiti konekciju dok su sve zauzete, ActiveRecord će blokirati upit i čekati dok se jedna ne oslobodi. Ako ne uspije dobiti konekciju u nekom roku (podesivo s `checkout_timeout: 5`), bacit će `ConnectionTimeoutError`.

Veličina poola se definira s `pool: 5` u `database.yml`. To ne znači da će se pri inicijalizaciji aplikacije odmah napraviti 5 konekcija, nego da će biti stvorene po potrebi.

U slučaju višeprocesnog servera, Rails stvara zasebni pool za svaki proces. TODO: Kako to radi? Koristi li se `establish_connection`?

Ako thread eksplicitno ne traži konekciju, pri prvom pozivu `ActiveRecord` metode konekcija će mu biti dodijeljena. Nakon što se response pošalje, Rails poziva `ActiveRecord::Base.clear_active_connections!` koja oslobađa sve konekcije trenutnog threada.

Ali ako stvaraš vlastite threadove unutar koda, automatsko oslobađanje se neće obaviti i pool će zauvijek ostati bez te konekcije. Zato kad god pokrećeš novi thread unutar requesta, ručno handlaj konekcije koristeći `checkout`, `checkin`ili `with_connection` s blokom. Više informacija na https://bibwild.wordpress.com/2014/07/17/activerecord-concurrency-in-rails4-avoid-leaked-connections/.

## Migracije

Kada deployaš aplikaciju na novi server ili je pokrećeš iznova na novom računalu, ne želiš pozivati sve migracije iznova. Umjesto toga, želiš učitati tablice iz `db/schema.rb`.

`rails db:setup` kreira bazu, stvara tablice koristeći `db/schema.rb` (ne pokreće migracije), izvršava `rails db:seed` ako postoji.

`rails db:prepare` će izvršiti `db:setup` ako baza ne postoji, ili izvršiti `db:migrate` ako baza postoji. Tako da ga možeš uvijek zvati.

## Schema Cache

Rails pri pokretanju aplikacije radi `SHOW FULL FIELDS` request na bazu kako bi dohvatio informacije o svim stupcima. Ovaj request zna biti spor, pa ako imaš jako puno Unicorn procesa koje restartiraš u isto vrijeme, baza se može naći pod velikim opterećenjem.

Rješenje je pokrenuti `rails db:schema:cache:dump` koji će zapisati podatke o stupcima u file, kako procesi ne bi morali raditi upite na bazu.

# Literatura

* http://ducktypelabs.com/four-ways-to-filter-has_many-associations/
* https://www.speedshop.co/2019/01/10/three-activerecord-mistakes.html
