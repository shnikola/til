# Random tips

## Rails Console

`app` - trenutni session instance, ima `app.get('/projects/6')` i `app.project_path(Project.first)`
`helper` - svi view helperi, npr. `helper.link_to` i `helper.truncate`

## JSON API Schema

Kako validirati JSON podatke koji dolaze u API? strong_params su meh - ne provjeravaju tip, kod se drži u controlleru. Loš data ne bi uopće trebao doću do app layera. Postoji format definiranja ulaznih (i izlaznih) podataka koji se zove JSON Schema. Todo: Pogledaj njegove integracije u railsu.

## Ne pozivaj html_safe u viewu

https://bibwild.wordpress.com/2013/12/19/you-never-want-to-call-html_safe-in-a-rails-template/

Samo onaj koji generira string (helper, decorator) treba pozivati `html_safe`! Nitko drugi!

## Constantize security

Nikad ne koristi `constantize` na stringovima koji su user input, jer to omogućava remote code execution (pomoću `Logger` klase).

## Url escaping

Url helperi escapaju parametre s `CGI.escape` (escapa `;` i `&`),
a sve ostalo s `URI.escape` (ne escapa ove stvari koje se mogu pojaviti u uriju).

## Rails Journey Router

https://www.youtube.com/watch?v=5hKkkQMOj3c

Railsov novi router je lud, izgenerira cijeli DFA (deterministic finite automata) i radi puno bolje nego niz ifova s regexima.

## ActiveRecord size vs count vs length

`length` loada sve recorde i izračuna length arraya.
`count` uvijek radi count query.
`size` je pametan i koristi jedno ili drugo ovisno jesu li recordi loadani.

Koristi `size`! Jedino ako trebaš broj taman prije nego ćeš loadati sve recorde, koristi `length`.

Umjesto `User.count > 0` koristi `User.exists?` da db ne mora napraviti full table scan.

## ActiveRecord Callbacks

http://www.justinweiss.com/articles/a-couple-callback-gotchas-and-a-rails-5-fix/

Ako želiš u callbacku gurati stvari u background job (zar ne bi trebao koristiti servise?), nemoj koristiti `after_save` nego `after_commit`.

`after_save` se poziva prije nego se transakcija commitala, pa background job možda neće pronaći record u bazi.

## Amazon Cloudfront

Serviranje static asseta bezveze umara naše servere, želimo da to netko drugi radi. S3 je ok, ali je napravljen za storage, a ne za delivery. Rješenje je cloudfront - daš mu origin domenu, i samo postaviš
`config.action_controller.asset_host = "<YOUR DISTRIBUTION SUBDOMAIN>.cloudfront.net"`

Prvi request na asset cloudfront će proslijediti na origin domenu (naš server), a svi ostali će biti cachirani. Samo pripazi da fileovi imaju hash u imenu kako ne bi bili stale.
