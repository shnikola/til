# Ruby Threads

## Rails Thread safety
https://bearmetal.eu/theden/how-do-i-know-whether-my-rails-app-is-thread-safe-or-not/
GIL ne dopušta da se dva threada istovremeno izvršavaju, ali to ne znači da je kod thread-safe. Thread se i dalje može pauzirati u bilo kojem trenutku, i drugi mu sjebat dijeljene podatke.

Rails i njegovi dependenciji su thread-safe. Kriv je tvoj kod! Ili gemovi.
Railsova arhitektura je takva da se po defaultu ništa ne dijeli između threadova, ali može se desiti da nešto slučajno podijelimo kroz:
  * globalne varijable
  * class varijable (koje su principu isto što i globalne)
  * class instance varijable (opet, klase se dijele, pa tako i njihove instance varijable)
  * konstante (ako ih nedajbože mijenjaš u kodu)
  * memoizaciju - koristi različita imena `@ivar`ova ako memoiziraš sa `super`: https://github.com/rails/rails/pull/9789/files


## ActiveRecord Concurrency
https://bibwild.wordpress.com/2014/07/17/activerecord-concurrency-in-rails4-avoid-leaked-connections/
`ConnectionPool` sadrži pojedinačne konekcije na DB. Svaku konekciju u jednom trenutku može koristiti samo jedan thread.
Thread može uzeti konekciju metodom `checkout`, i vratiti je metodom `checkin`. Metoda `with_connection` wrapa ovo ponašanje oko bloka.

Ako thread eksplicitno ne traži konekciju, pri prvom pozivu `ActiveRecord` metode konekcija će mu biti dodijeljena. Nakon što se response pošalje, Rails poziva `ActiveRecord::Base.clear_active_connections!` (možeš ga i sam pozvati) koji radi `checkin` svih konekcija trenutnog threada.

Ali, ako stvaraš vlastite `Thread`ove u kodu, automatski `checkin` se neće dogoditi, i nužno je da to handlaš ručno. Ako zaboraviš, konekcija se nikad neće osloboditi i `ConnectionPool` će zauvijek ostati bez nje. Pripazi ako stvaraš Threadove unutar requesta!


## Kamo je nestao config.threadsafe?
http://tenderlovemaking.com/2012/06/18/removing-config-threadsafe.html
Prije Rails 4.0 postojao je flag `config.threadsafe!` koji je sad po defaultu enablan. On radi dvije stvari: preloada framework i user kod pri bootu, te uklanja `Rack::Lock` middleware.

Preloading je, za razliku od lazy loadanja pomoću `autoload` metode, thread-safe. Kada je uključen, nema nikakve razlike za *multi-process* i *multi-threaded* sustave. Jedino će preload frameworka zauzeti nešto više memorije jer učitava cijeli framework, a ne samo dijelove koji se koriste. (Nadajmo se da će Rails biti malo pametniji s tim u budućnosti.)

`Rack::Lock` mutexom oko requesta brani različitim threadovima da u isto vrijeme izvršavaju kod. U *multi-process* sustavu (npr. Unicorn) nije potreban, jer svaki proces ima svoju memoriju i čak ni thread-unsafe kod neće imati problema. U *multi-threaded* sustavu (npr. Puma) mutex sprečava thread-unsafe situacije, što je super, ali istovremeno i ne dopušta da se threadovi istovremeno vrte, što je poanta *multi-threadanja*. Zato su odlučili ukloniti ga, i svima koji koriste *multi-threaded* servere reći: "Pazite da vam je kod thread-safe."

## Thread.kill i ensure.
http://blog.arkency.com/2016/04/how-i-hunted-the-most-odd-ruby-bug/
Kada se šalje `Thread.kill`, thread se neće instantno prekinuti, već će prije toga odraditi `ensure` block.
Kada se proces gasi (`exit`), svim threadovima pokrenutim iz njega šalje se `Thread.kill`.

U ovom slučaju, proces je u `at_exit` eksplicitno pozivao `Thread.kill` threadu kojeg je prethodno pokrenuo. Thread je u svom `ensure` bloku imao `sleep(10)`. Proces bi nekad obavio taj `sleep`, a nekad ne bi. Zašto?

Thread scheduling se ponaša *nedeterministično*. Ponekad se dogodi ovako:
  1. Proces pri izlasku pošalje `Thread.kill`. Thread uđe u `ensure` block.
  2. Proces unutar `at_exit` pošalje eksplicitni `Thread.kill`. Thread se izbaci iz `ensure` blocka i prekine `sleep`.
A ponekad se dogodi ovako:
  1. Proces pri izlasku pošalje `Thread.kill`. Thread uđe u `ensure` block.
  2. Izvršavanje se prebaci na thread i počne se izvršavati `sleep`.
  3. Izvršavanje se nikad ne prebaci na main thread procesa jer je označen kao *killed*.

Pouka: ne stavljaj `sleep` u `ensure` blokove threada :)
