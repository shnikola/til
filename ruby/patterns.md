# Patterns

## Plugin System of Sequel and Roda

https://twin.github.io/the-plugin-system-of-sequel-and-roda/

Gemovi ili dolaze s mnogo featurea, zbog čeka troše nepotrebno mnogo resursa, ili su vrlo jednostavni pa moraš sve sam implementirati. Ovo rješenje omogućuje da u gem uključiš sve feature koje želiš, a da korisnik odabere koje od njih će koristiti.

Featuri su podijeljeni u pluginove, svaki u svom fileu. Dodaje se `self.plugin(name)` class metoda koja će napraviti `require "your_gem/plugins/#{name}"`, includati `plugin::InstanceMethods` i extendati `plugin::ClassMethods`, za sve "base" module s kojima gem po defaultu dolazi. Nakon includanja poziva se `plugin.configure` s opcijama proslijeđenim kroz `self.plugin` metodu.

Kako bi pluginovi mogli overridati "base" module (`include` i `extend` se stavljaju ispod base metode u inheritance chainu), i base moduli su sami definirani kao plugin koji se na početku loada. Što je dosta elegatno - sve se definira preko istog mehanizma, ostatak koda su prazne ljuske modula.

Na ovaj način gem može po defaultu imati samo core funkcionalnosti, a korisnik može loadati samo feature koje treba.

## Rails API Errors

https://blog.rebased.pl/2016/11/07/api-error-handling.html

Handlanje errora i različith statusa u controlleru se može vrlo lako zakomplicirati. Ovdje se predlaže u controlleru držati samo "happy path" u kojem se koriste `find` i `update!`, a errore handlati s `rescue_from`.

Jedino treba malo adaptirati serijalizaciju `user.errors` objekta pomoću `user.errors.details` (Rails 5) i custom `:api` lokalizacije.

## Fat Models

http://blog.codeclimate.com/blog/2012/10/17/7-ways-to-decompose-fat-activerecord-models/

Umjesto nasljeđivanja (mixina), koristi kompoziciju (objekte). Tako je puno lakše pratiti odakle koja metoda dolazi.

Imaju sad neke različite vrste (*Value Object*, *Service Object*, *Form Object*...), ali bitno je samo da ih izbaciš kao zasebnu klasu.

## Odvajanje domain logike iz controllera

https://vimeo.com/44807822

Stvari poput `User.update` ne bi se smjeli mješati s `render @user`. Pogotovo ako `User.update` dio ima složeniju logiku.

Zato lik predlaže:
* `UserCreator.new(self)` koji prima instancu controllera.
* `user_creator.create_for(current_user, params[:user])` obavlja sav posao i poziva metodu `listener.create_success(user)` ili `listener.create_failure(user)`, definirane u controlleru.

Prednost toga je što controller više ne mora znati detalje kako se User createa. A create je sada lakše zasebno testirati.

Mislim da to ima smisla koristiti, ali samo za stvarno složene slučajeve.

## Decorators

https://bibwild.wordpress.com/2012/12/19/the-simplest-rails-decorator-implementation-that-just-might-work/

Kada želiš dodati neku prezentacijsku logiku modelu, drži je u **helperima**. Nije OOP, ne možeš ih overridati, ali i dalje je bolje nego uvoditi `Decorator` kao još jedan arhitektonski element koji će netko morati razumjeti.

Ako baš ne ide sa helperima (npr. radiš gem i želiš da user može mijenjati prezentacijsku logiku), najjednostavniji `Decorator` je `MyDecorator < SimpleDelegator`. SimpleDelegator je Rubijev stdlib delegator.

`MyDecorator.new(@model, view_context)` za inicijalizaciju. `view_context` je potreban zbog poziva na `content_tag` i `current_user` helpere i slično.

## Null Object

Isplati se ako imaš puno conditionala, npr. `if (user) { user.name } else { 'No name.' }`. Downside je što treba držati public interface `User` i `NullUser` usklađenim.



