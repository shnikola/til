# Action View

## Form Helpers

`form_tag users_url do ... end` koristi ako nemaš model.
`form_for @user do |f| ... end` automatski popunjava fieldove forme s atributima modela.

Rails 5.1. oba zamjenjuje s metodom `form_with` u dvije varijante: `form_with url: users_url do |f|` i `form_with model: @user do |f|`. Oba koriste form objekt za generiranje inputa, npr. `f.text_field :email`.

`form_with` ne postavlja `id` i `class` automatski pa ih trebaš dodati sam.

`form_with` je po defaultu `remote: true`. Želiš li to disablati, koristi `local: true`.



## Asset Host

Serviranje static asseta bezveze umara naše servere, želimo da to netko drugi radi. S3 je ok, ali je napravljen za storage, a ne za delivery. Rješenje je cloudfront - daš mu origin domenu, i samo postaviš
`config.action_controller.asset_host = "9e1nf93n.cloudfront.net"`

Prvi request na asset cloudfront će proslijediti na origin domenu (naš server), a svi ostali će biti cachirani. Samo pripazi da fileovi imaju hash u imenu kako ne bi bili stale.

## Ne pozivaj html_safe u viewu

https://bibwild.wordpress.com/2013/12/19/you-never-want-to-call-html_safe-in-a-rails-template/

Samo onaj koji generira string (helper, decorator) treba pozivati `html_safe`! Nitko drugi!

