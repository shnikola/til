# Concurrency

*Concurrent* znači da se dvije radnje izvršavaju u istom *vremenskom periodu*.
*Parellel* znači da se dvije radnje izvršavaju u istom *trenutku*.

## Multiple processes vs Multi-threaded
* Threadovi zauzimaju manje memorije od procesa
* Threadovi se brže stvaraju, uništavaju, i lakše se switcha između njih jer koriste istu memoriju.
* Threadovi uvijek umiru kad im parent proces umre; forkani procesi koji ne završe prije parenta postaju zombiji.
* Threadovi mogu komunicirati preko `Queue`, procesi moraju koristiti pipeove ili sockete.
* Procesi su memorijski izolirani pa se ne moraš brinuti za race conditione, lakši su za programiranje i debugiranje.

Ukratko: threadovi su jeftiniji, ali moraš biti puno oprezniji s njima. Procesi su skuplji, ali daju ti sigurnost.


## Procesi
- fork
- wait
- copy-on-write


## Thread
- thread.new
- join


## GIL
MRI (standardna Ruby implementacija) ima *global interpreter lock* (GIL). On ne dopušta da se više threadova istog Ruby procesa izvršava istovremeno. To znači da će Ruby proces u jednom trenutku moći koristiti samo jednu jezgru procesora.

GIL se koristi zbog thread-safetija samog MRI-a, ali on *ne garantira* da je sav tvoj Ruby code thread-safe. Thread scheduler i dalje može pauzirati thread u bilo kojem trenutku, i aktivirati drugi thread koji će pisati preko dijeljene varijable.

Drugi Ruby interpreteri (JRuby, Rubinius) nemaju ovo ograničenje, te će se kod njih multi-threaded programi izvršavati paralelno. Ali koristiti multi-threaded programe ima smisla i za MRI: ako thread mora čekati na IO, drugi se thread može izvršavati u međuvremenu.


## Thread Pooling
Ako imaš jako puno malih zadataka koje želiš obavljati istovremeno, overhead stvaranja novog threada za svaki može biti velik. Usto, threadovi jesu jeftini, ali nisu besplatni. Ako kreiraš 1000 threadova odjednom, ostat ćeš bez resourca.

Da bi se to izbjeglo, koristi *thread pooling*. Pool ima određenu veličinu koja određuje koliko će threadova unaprijed stvoriti (ili u slučaju lazy poola, koji je maksimum koji će stvoriti). Pool zadatke koje dobije prosljeđuje trenutno idle threadu.

Thread Pool je implementiran u librarijima kao što je `Celluloid`.


## Rails Thread safety
Rails framework code je thread-safe. Ako se svaki request obrađuje u svom threadu, defaltno nema zajedničkih varijabli koje bi mogle izazvati problem. Ipak, u našem kodu možemo nešto slučajno podijeliti kroz:
  * globalne varijable
  * class varijable (koje su principu isto što i globalne)
  * class instance varijable (opet, klase se dijele, pa tako i njihove instance varijable)
  * konstante (ako ih nedajbože mijenjaš u kodu)

Jedan neobičan thread-unsafe slučaj je memoizacija instance varijabli: https://github.com/rails/rails/pull/9789/files
Zaključak: koristi različita imena `@var`ova ako memoiziraš sa `super`.


## ActiveRecord Connection Pool
https://devcenter.heroku.com/articles/concurrency-and-database-connections
https://bibwild.wordpress.com/2014/07/17/activerecord-concurrency-in-rails4-avoid-leaked-connections/
Ako koristiš multi-threaded ili multi-process server, imaj na umu da svaki thread zahtjeva svoju database konekciju.
Kako bi izbjegao otvaranje prevelikog broja konekcija, ActiveRecord drži `ConnectionPool`, veličine podesive kroz database setting `pool` (default veličina je `5`). Ako pokušaš dohvatiti konekciju kada su sve zauzete, ActiveRecord će blokirati upit i čekati dok se prva ne oslobodi. Ako ne uspije dobiti konekciju, bacit će `ConnectionTimeoutError`.

Za multi-threader servere, dovoljno je postaviti `pool` u `database.yml`

Usto, svaki database ima maksimalan broj konekcija koje može podržavati.

- ActiveRecord će stvoriti poseban connection pool za svaki proces

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


## Ruby 3 Guild Proposal
http://www.atdot.net/~ko1/activities/2016_rubykaigi.pdf
Želimo omogućiti paralelizam, ali da ne moramo razmišljati o lockovima.
*Guild* je nova apstrakcija:
  * Svaki guild ima jedan ili više thread, i threadovi u različitim guildovima se mogu izvršavati paralelno.
  * Svi mutable objekti pripadaju jednom guildu i ne može im se pristupati iz drugoga. Zahvaljujući tome nema race conditiona, pa ni potrebe za lockovima.
  * `Guild::Channel.transfer` za prosljeđivanje immutable objekata (najbolji slučaj)
  * `Guild::Channel.copy` za slanje deep kopije objekta (sigurno, ali sporije)
  * `Guild::Channel.transfer_membership` za prebacivanje mutable objekata (brišu se iz prvog guilda)
  * Posebna struktura za dijeljenje mutable objekata - potrebni lockovi, ali to koristi se samo u rijetkim slučajevima.


## Thread safe collections
https://github.com/hamstergem/hamster
Gem s immutable strukturama podataka (Hash, Vector, Set, SortedList, List, Deque) koje su po definiciji thread safe.


# TODO
rack.hijack i message_bus
background jobs (resque koristi procese, sidekiq threadove, suckerpunch)


# Literatura
* http://www.jstorimer.com/blogs/workingwithcode/8085491-nobody-understands-the-gil
* https://www.toptal.com/ruby/ruby-concurrency-and-parallelism-a-practical-primer
