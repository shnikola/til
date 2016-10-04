# Rails Patterns

## Fat Models
http://blog.codeclimate.com/blog/2012/10/17/7-ways-to-decompose-fat-activerecord-models/
**Bitno:** umjesto nasljeđivanja (mixina), koristi kompoziciju (objekte). Tako je puno lakše pratiti odakle koja metoda dolazi.
Imaju sad neke različite vrste (*Value Object*, *Service Object*, *Form Object*...), ali bitno je samo da ih izbaciš kao zasebnu klasu.


## Odvajanje domain logike iz controllera
https://vimeo.com/44807822
Stvari poput `User.update` ne bi se smjeli mješati s `render @user`. Pogotovo ako `User.update` dio ima složeniju logiku.
Zato lik predlaže:
  * `UserCreator.new(self)` koji prima instancu controllera.
  * `create_for(current_user, params[:user])` obavlja sav posao i poziva metodu `listener.create_success(user)` ili `listener.create_failure(user)`, definirane u controlleru.
Prednost toga je:
  * controller više ne mora znati detalje kako se User createa.
  * create je sada lakše zasebno testirati
Mislim da to ima smisla koristiti, ali samo za stvarno složene slučajeve.


## Decorators
https://bibwild.wordpress.com/2012/12/19/the-simplest-rails-decorator-implementation-that-just-might-work/
Kada želiš dodati neku prezentacijsku logiku modelu, drži je u **helperima**. Nije OOP, ne možeš ih overridati, ali i dalje je bolje nego uvoditi `Decorator` kao još jedan arhitektonski element koji će netko morati razumjeti.

Ako baš ne ide sa helperima (npr. radiš gem i želiš da user može mijenjati prezentacijsku logiku), najjednostavniji `Decorator` je:
  * `MyDecorator < SimpleDelegator`. SimpleDelegator je Rubijev stdlib delegator.
  * `MyDecorator.new(@model, view_context)` za inicijalizaciju. `view_context` je potreban zbog poziva na `content_tag` i `current_user` helpere i slično.



# Random tips

## JSON API Schema
Kako validirati JSON podatke koji dolaze u API? strong_params su meh - ne provjeravaju tip, kod se drži u controlleru. Loš data ne bi uopće trebao doću do app layera. Postoji format definiranja ulaznih (i izlaznih) podataka koji se zove JSON Schema. Todo: Pogledaj njegove integracije u railsu.


## Ne pozivaj html_safe u viewu
https://bibwild.wordpress.com/2013/12/19/you-never-want-to-call-html_safe-in-a-rails-template/
Samo onaj koji generira string (helper, decorator) treba pozivati `html_safe`! Nitko drugi!


## Url escaping
Url helperi escapaju parametre s `CGI.escape` (escapa `;` i `&`),
a sve ostalo s `URI.escape` (ne escapa ove stvari koje se mogu pojaviti u uriju)


## ActiveRecord size vs count vs length
`length` loada sve recorde i izračuna length arraya.
`count` uvijek radi count query.
`size` je pametan i koristi jedno ili drugo ovisno jesu li recordi loadani.
Koristi `size`! Jedino ako trebaš broj taman prije nego ćeš loadati sve recorde, koristi `length`.


## ActiveRecord Callbacks
http://www.justinweiss.com/articles/a-couple-callback-gotchas-and-a-rails-5-fix/
Ako želiš u callbacku gurati stvari u background job (zar ne bi trebao koristiti servise?),
nemoj koristiti `after_save` nego `after_commit`. `after_save` se poziva prije nego se transakcija
commitala, pa background job možda neće pronaći record u bazi.
