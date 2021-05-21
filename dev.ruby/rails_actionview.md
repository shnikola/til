# Action View

## Form Helpers

`form_tag users_url do ... end` koristi ako nemaš model.
`form_for @user do |f| ... end` automatski popunjava fieldove forme s atributima modela.

Rails 5.1. oba zamjenjuje s metodom `form_with` u dvije varijante: `form_with url: users_url do |f|` i `form_with model: @user do |f|`. Oba koriste form objekt za generiranje inputa, npr. `f.text_field :email`.

`form_with` ne postavlja `id` i `class` automatski pa ih trebaš dodati sam.

`form_with` je po defaultu `remote: true`. Želiš li to disablati, koristi `local: true`.

`form_tag` bez bloka samo otvara form tag, što zna poremetiti sve sljedeće forme. Koristi `form_tag do; end` za praznu formu.

## Form Inputs

`options_for_select` trebaš koristiti samo za `select_tag`. Za ostale slučajeve koristi `f.select(:city, ["Zagreb", "Morocco", "Paris"])` ili `f.collection_select :user_id, User.all, :id, :name`.

Za asocijacije koje su unaprijed stavljene u bazu (samo ih treba povezati, npr. `Post has_many :categories`) koristi:
* `f.collection_select :category_id, Category.all, :id, :name` za `has_one`
* `f.collection_check_boxes :category_ids, Category.all, :id, :name` za `has_many`. Koristi blok za definiranje kako će se label, checkbox, i dodatni elementi generirati.

## Rendering partials

Ako renderiraš velik broj partiala u loopu, koristi `render @products` jer je brži od `render product` u loopu (ne mora raditi lookup svaki put iznova). Ako trebaš nestandardni template, koristi `render partial: "product", collection: @products, as: :product_item`.

## Helperi s blokovima

Za spajanje više elemenata u helper metodi, koristi: `concat hidden_field_tag :field`.

Ako trebaš primiti i korisnikov blok, koristi: `concat capture(&block) if block_given?`, ili `capture(f, &block)` ako prosljeđuješ argument u blok.

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

Serviranje static asseta bezveze umara naše servere, želimo da to netko drugi radi. S3 je ok, ali je napravljen za storage, a ne za delivery. Rješenje je cloudfront - daš mu origin domenu, i samo postaviš
`config.action_controller.asset_host = "9e1nf93n.cloudfront.net"`

Prvi request na asset cloudfront će proslijediti na origin domenu (naš server), a svi ostali će biti cachirani. Samo pripazi da fileovi imaju hash u imenu kako ne bi bili stale.

## html_safe

Samo onaj koji generira string (helper, decorator) treba pozivati `html_safe`. Nemoj ga pozivati u viewu.

