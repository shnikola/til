# Action View

## Form Helpers

`form_tag users_url do ... end` koristi ako nemaš model.
`form_for @user do |f| ... end` automatski popunjava fieldove forme s atributima modela.

Rails 5.1. oba zamjenjuje s metodom `form_with` u dvije varijante: `form_with url: users_url do |f|` i `form_with model: @user do |f|`. Oba koriste form objekt za generiranje inputa, npr. `f.text_field :email`.

`form_with` ne postavlja `id` i `class` automatski pa ih trebaš dodati sam.

`form_with` je po defaultu `remote: true`. Želiš li to disablati, koristi `local: true`.

`form_tag` bez bloka samo otvara form tag, što zna poremetiti sve sljedeće forme. Koristi `form_tag do; end` za praznu formu.

## Select

`options_for_select` treba se koristiti samo za `select_tag`. Ako ga želiš izbjeći možeš koristiti
`select(nil, :city, ["A", "B", "C"])` ako imaš array ili
`collection_select(nil, :user_id, User.all, :id, :name)` ako imaš objekte.

Za slučaj arraya, dodatne atribute na option možeš dodati s
`['name', 'value', 'data-extra' => '...']`.

Ako nisi vezan za `f.object`, vrijednost predselektiraj s
`select(nil, :city, options, { prompt: '...', selected: 'id' })`

Za asocijacije koje su unaprijed stavljene u bazu (samo ih treba povezati, npr. `Post has_many :categories`) koristi:
* `f.collection_select :category_id, Category.all, :id, :name` za `has_one`
* `f.collection_check_boxes :category_ids, Category.all, :id, :name` za `has_many`. Koristi blok za definiranje kako će se label, checkbox, i dodatni elementi generirati.

## Rendering partials

Ako renderiraš velik broj partiala u loopu, koristi `render @products` jer je brži od `render product` u loopu (ne mora raditi lookup svaki put iznova). Ako trebaš nestandardni template, koristi `render partial: "product", collection: @products, as: :product_item`.

## Helperi s blokovima

Za spajanje više elemenata u helper metodi, koristi: `concat hidden_field_tag :field`.

Ako trebaš primiti i blok, nemoj koristit `yield` jer će on uvijek dodati html u body. Umjesto toga koristi: `capture(&block) if block_given?`, ili `capture(f, &block)` ako u blok prosljeđuješ argument.

## Friendly URLs

Najjednostavniji način za dodati SEO friendly urlove je u modelu definirati metodu `to_param` koja vraća `[id, title.parameterize].join("-")`.

Linkove generiraj s `link_to "Title", @article` koji će pozvati `to_param` nad objektom. U controlleru objekt dohvaćaj uobičajeno s `Article.find(params[:id])` koji će friendly url pretvoriti u integer.

## Time formats

Umjesto da koristiš `strftime` u viewovima, definiraj formate u `config/initializers/time_formats.rb`, npr:

`Date::DATE_FORMATS[:stamp] = "%Y%m%d"`
`Time::DATE_FORMATS[:stamp] = "%Y%m%d%H%M%S"`

I onda ih koristi u viewu s `date.to_s(:stamp)`.

## Highlight

`highlight(text, string)` će označiti string unutar teksta s `<mark>` tagovima.

## Asset Host

`config.asset_host = 'blah.cloudfront.net'` postavlja host za sve assete koji se serviraju iz aplikacije.

Ako želiš u mailovima koristiti slike iz `assets` foldera, mora biti definiran `config.asset_host` ili `config.action_mailer.asset_host`. Alternativno, možeš servirati diretkno s clouda, npr. `image_tag 'https://assets.s3.com/image.png'`.

## html_safe

Samo onaj koji generira string (helper, decorator) treba pozivati `html_safe`. Nemoj ga pozivati u viewu.

