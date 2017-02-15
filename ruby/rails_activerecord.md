# ActiveRecord

## UUID

Korištenje UUID-a olakšava skaliranje pošto se idjevi više ne generiraju sekvencijalno. Još jedna prednost je što ćeš znati id modela prije nego ga saveaš u bazu, pa ga možeš poslati u cache ili search engine prije nego se db transakcija izvrši.

`config.active_record.primary_key = :uuid` defaultno koristi UUID za sve modele.

`create_table :users, id: :uuid` koristi UUID za tablicu u migraciji.

## Associations

Kada se model savea, automatski će saveati sve `has_one` i `has_many` associatione koji još nisu persisted. Želiš li izbjeći to ponašenje, dodaj `has_one :user, autosave: false`.

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

Umjesto `User.count > 0` koristi `User.exists?` da db ne mora napraviti full table scan.

## Callbacks

http://www.justinweiss.com/articles/a-couple-callback-gotchas-and-a-rails-5-fix/

Ako želiš u callbacku gurati stvari u background job (zar ne bi trebao koristiti servise?), nemoj koristiti `after_save` nego `after_commit`.

`after_save` se poziva prije nego se transakcija commitala, pa background job možda neće pronaći record u bazi.

