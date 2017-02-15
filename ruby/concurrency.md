# Concurrency

*Concurrent* znači da se dvije radnje izvršavaju u istom *vremenskom intervalu*. *Parellel* znači da se dvije radnje izvršavaju u istom *trenutku*.

Concurrency je način strukturiranja programa kako bi se mogao (ali ne i nužno morao) paralelizirati.

## Multiple processes vs Multi-threaded

* Threadovi zauzimaju manje memorije od procesa
* Threadovi se brže stvaraju, uništavaju, i lakše se switcha između njih jer koriste istu memoriju.
* Threadovi uvijek umiru kad im parent proces umre; forkani procesi koji ne završe prije parenta postaju zombiji.
* Threadovi mogu komunicirati preko `Queue`, procesi moraju koristiti pipeove ili sockete.
* Procesi su memorijski izolirani pa se ne moraš brinuti za race conditione, lakši su za programiranje i debugiranje.

Ukratko: threadovi su jeftiniji, ali moraš biti puno oprezniji s njima. Procesi su skuplji, ali daju ti sigurnost.

## Procesi

`fork { ... }` stvara novi `ruby` proces koji će izvoditi naredbe unutar bloka, dok će parent nastaviti izvoditi naredbe nakon `fork`. Vraća `pid` stvorenog subprocesa.

`if pid = fork ... else  ...` stvara novi proces koji će vratiti `nil` i izvoditi `else` dio izraza, dok će parent vratiti `pid` i izvoditi gornji `if` dio.

`exit(code)` prekida proces i vraća status `code`. Poziva se `at_exit` handler.
`exit!(code)` prekida proces, vraća `code`, ali ne poziva `at_exit`.

`Process.wait(pid)` blokira trenutni proces dok se proces `pid` ne završi.
`Process.waitall` blokira dok svi subprocesi ne završe.

Ukoliko parent ne čeka svoj subproces da završe, on će postati zombie.
`Process.detach(pid)` stvara novi thread koji će se pobrinuti da subproces završi bez da se mora pozivati `wait`.

Kada se stvara novi proces u Unixu, on dobija kopiju cijele memorijskog prostora parent procesa. Kako je to skupo, koristi se *Copy on write* mehanizam, koji isprva dopušta procesima da koriste istu memoriju, a radi kopiju tek kada jedan od njih pokuša pisati u nju. Ruby 2.0+ podržava copy on write.

Procesi komuniciraju streamovima preko *pipea* ili porukama preko *socketa*.

`reader, writer = IO.pipe` stvara pipe za jednosmjernu komunikaciju. Proces koji piše učinit će `reader.close` i `writer.write`, a proces koji čita `writer.close` i `reader.read`.

`child, parent = Socket.pair(:UNIX, :DGRAM, 0)` stvara par Unix socketa za dvosmjernu komunikaciju. Child proces koji piše učinit će `parent.close`, primati poruke s `child.recv(maxlen)` i slati ih s `child.send(msg, 0)`. Parent radi isto, samo radi `child.close` i komunicira s `parent` socketom.

## Pozivanje vanjskih procesa

``ls`` ili `%x{ls}` forka novi proces i izvršava ga.
* vraća `stdout` novog procesa kao string. Status možeš dohvatiti iz `$?.success?` (bleh)
* blokira dok se proces ne završi.
* exceptioni se prosljeđuju masteru.

`system("ls")` isto forka novi proces i izvršava ga.
* vraća `true` ili `false` prema exit statusu. Output printa na `stdout`.
* blokira dok se proces ne završi.
* exceptioni *ne* dolaze do mastera.

`exec("ls")` prekida Ruby proces s `exit` i zamjenjuje ga danom naredbom. Ništa se ne vraća jer više nismo u Rubyju.

`Process.spawn("ls")` pokreće proces u subshellu i odmah vraća PID. Ne blokira.

`IO.popen("ls", "r+")` pokreće danu naredbu kao podproces čiji su input i output povezani na IO objekt kojeg metoda vraća. Korisno za proslijeđivanje podataka, ali ne hvata `stderr`.

## Open3 (TODO)

`Open3` je dio standardnog librarija koji omogućava detaljniji pristup vanjskim procesima.

* `Open3.capture2('ls', options)` vraća `stdout` i `status`.
* `Open3.capture3('ls', options)` vraća `stdout`, `stderr` i `status`.
* `Open3.pipeline('sort', 'uniq -c', in: 'file.txt', out: 'count.txt')` za pipeline.
* `Open3.popen3('grep foo') do |stdin, stdout, stderr, wait_thr|` čini dostupnima input, output i error IO stream.
* `:stdin_data` opciju koristi za slanje stdina.

## Threads

Svaki Ruby proces ima barem jedan thread: main thread. `Thread.main` ga vraća. Kada main thread završi, svi ostali threadovi i proces se isto prekidaju.

`Thread.new { ... }` stvata novi thread koji izvodi naredbe u bloku.
Threadovi imaju closure, i vidljive su im sve varijable u scopeu. Da bi izbjegao thread unsafety, da predaš parametre threadu koristi `Thread.new(param) { |x| ... }` koji radi kopiju lokalne varijable, pa je možeš bez straha mijenjati.

`thread.join` blokira main thread dok se `thread` ne završi. U suprotnom, svi threadovi će biti prekinuti kada završi main thread.

`Thread.main` je main thread.
`Thread.current` je thread koji se trenutno izvršava.
`Thread.list` je array neprekinutih threadova.

`Thread.stop(thread)` privremeno zaustavlja izvođenje threada. `thread.wakeup` ga ponovno označava za nastavak izvođenja.
`Thread.pass` predlaže scheduleru da preuzme neki drugi thread, ali ne garantira da će se to dogoditi.

`thread.exit` prekida `thread` s `exit`. Ukoliko je to zadnji ili main thread, prekida se proces.

`thread.status` vraća trenutno stanje threada: `sleep`, `run`, `aborting`; `false` ako je prekinut normalno, `nil` ako je prekinut s exceptionom.
`thread.alive?` ako se izvršava ili spava, `thread.stop?` ako je prekinut ili spava.

`thread[:key]` spremanje thread varijabli kojima i drugi threadovi mogu pristupati. Zapravo su Fiber-local.

Ako se u threadu dogodi exception, defaultno će samo tiho biti prekinut. Koristi `Thread.abort_on_exception = true` da se exception iz propagira u main thread, ili `Thread.report_on_exception = true` za ispisivanje exceptiona.

Za sinkorinizaciju koristi, stvori `mutex = Mutex.new` izvan threadova.
* `mutex.synchronize do ...` unutar threada. Ako je mutex slobodan, thread će izvršiti blok; ako nije, blokirat će se dok se ne izvrši.
* `mutex.try_lock` ne blokira, nego ili locka mutex i vrati `true`, ili vrati `false`.

`Queue` je threadsafe struktura. `enq` dodaje element, `deq` uzima ili blokira dok ne stigne novi.

## GIL

MRI (standardna Ruby implementacija) ima *global interpreter lock* (GIL). On ne dopušta da se više threadova istog Ruby procesa izvršava istovremeno. To znači da će Ruby proces u jednom trenutku moći koristiti samo jednu jezgru procesora.

GIL se koristi kako bi se izbjegli race conditioni MRI internog koda, ali on *ne garantira* da je sav tvoj Ruby code thread-safe. Thread scheduler i dalje može pauzirati thread u bilo kojem trenutku, i aktivirati drugi thread koji će npr. prepisati zajedničku varijablu.

Drugi Ruby interpreteri (JRuby, Rubinius) nemaju ovo ograničenje (umjesto GILa imaju mnogo malih internih lockova), te će se kod njih multi-threaded programi izvršavati paralelno. Ali koristiti multi-threaded programe ima smisla i za MRI: ako thread mora čekati na IO, MRI će se prebaciti na drugi thread koji nije blokiran.

## Signals

U slučaju da se multithreaded procesu pošalje signal, kernel će nasumično odabrati jedan thread kojem će prenijeti taj signal. Kako bi izbjegao probleme koje to može izazvati, MRI (i druge Ruby implementacije) pri pokretanju stvaraju poseban thread koji je zadužen za handlanje signala i pipeom ih šalje main threadu.

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

Jedan neobičan thread-unsafe slučaj je memoizacija instance varijabli. Ako radiš `@attr ||= super.merge(...)`, pripazi da koristiš različito ime `@attr` od onog koje se koristi u `super` metodi. (https://github.com/rails/rails/pull/9789/files)

## ActiveRecord Connection Pool

https://devcenter.heroku.com/articles/concurrency-and-database-connections

Ako koristiš multi-threaded ili multi-process server, imaj na umu da svaki thread zahtjeva svoju database konekciju. Svaki database ima ograničenje koliko maksimalno konekcija može podržavati.

Kako bi izbjegao otvaranje prevelikog broja konekcija, ActiveRecord drži `ConnectionPool`, veličine podesive kroz database setting `pool` (default veličina je `5`). Ako pokušaš dohvatiti konekciju kada su sve zauzete, ActiveRecord će blokirati upit i čekati dok se prva ne oslobodi. Ako ne uspije dobiti konekciju, bacit će `ConnectionTimeoutError`.

Za multi-threaded servere, pool će se konfigurirati pri inicijalizaciji aplikacije koristeći property `pool` u `config/database.yml`. Kod Pume, svaki proces ima svoj pool, pa je dovoljno je da postaviš `pool: ENV['RAILS_MAX_THREADS']`, po konekciju za svaki thread.

Kod multi-process servera, master proces inicijalizira aplikaciju i tek onda forka workere. Pošto njemu samom ne treba konekcija, možeš u `config/unicorn.rb` dodati:
* `ActiveRecord::Base.connection.disconnect!` unutar `before_fork`
* `ActiveRecord::Base.establish_connection` u `after_fork`

## Connection Pool Internals

https://bibwild.wordpress.com/2014/07/17/activerecord-concurrency-in-rails4-avoid-leaked-connections/

Thread traži konekciju metodom `checkout`, i oslobađa je metodom `checkin`. Metoda `with_connection` wrapa ovo ponašanje oko bloka.

Ako thread eksplicitno ne traži konekciju, pri prvom pozivu `ActiveRecord` metode konekcija će mu biti dodijeljena. Nakon što se response pošalje, Rails poziva `ActiveRecord::Base.clear_active_connections!` (možeš ga i sam pozvati) koji radi `checkin` svih konekcija trenutnog threada.

Ali, ako stvaraš vlastite `Thread`ove u kodu, automatski `checkin` se neće dogoditi, i nužno je da to handlaš ručno. Ako zaboraviš, konekcija se nikad neće osloboditi i `ConnectionPool` će zauvijek ostati bez nje. Pripazi ako stvaraš Threadove unutar requesta!

## Kamo je nestao config.threadsafe?

http://tenderlovemaking.com/2012/06/18/removing-config-threadsafe.html

Prije Rails 4.0 postojao je flag `config.threadsafe!` koji je sad po defaultu enablan. On radi dvije stvari: preloada framework i developer code pri bootu, te uklanja `Rack::Lock` middleware.

Preloading je, za razliku od lazy loadanja pomoću `autoload` metode, thread-safe. Kada je uključen, nema nikakve razlike za *multi-process* i *multi-threaded* sustave. Jedino će preload frameworka zauzeti nešto više memorije jer učitava cijeli framework, a ne samo dijelove koji se koriste. (Nadajmo se da će Rails biti malo pametniji s tim u budućnosti.)

`Rack::Lock` mutexom oko requesta brani različitim threadovima da u isto vrijeme izvršavaju kod. U *multi-process* sustavu (npr. Unicorn) nije potreban, jer svaki proces ima svoju memoriju i čak ni thread-unsafe kod neće imati problema. U *multi-threaded* sustavu (npr. Puma) mutex sprečava thread-unsafe situacije, što je super, ali istovremeno i ne dopušta da se threadovi istovremeno vrte, što je poanta *multi-threadanja*. Zato su odlučili ukloniti ga, i svima koji koriste *multi-threaded* servere reći: "Pazite da vam je kod thread-safe."

## Thread.kill i ensure.

http://blog.arkency.com/2016/04/how-i-hunted-the-most-odd-ruby-bug/

Kada se šalje `Thread.kill`, thread se neće instantno prekinuti, već će prije toga odraditi `ensure` block.

Kada se proces gasi (`exit`), svim threadovima pokrenutim iz njega šalje se `Thread.kill`.

U ovom slučaju, proces je u `at_exit` eksplicitno pozivao `Thread.kill` threadu kojeg je prethodno pokrenuo. Thread je u svom `ensure` bloku imao `sleep(10)`. Proces bi nekad obavio taj `sleep`, a nekad ne bi. Zašto?

Thread scheduling se ponaša nedeterministički. Ponekad se dogodi ovako:
1. Proces pri izlasku pošalje `Thread.kill`. Thread uđe u `ensure` block.
2. Proces unutar `at_exit` pošalje eksplicitni `Thread.kill`. Thread se izbaci iz `ensure` blocka i prekine `sleep`.

A ponekad se dogodi ovako:
1. Proces pri izlasku pošalje `Thread.kill`. Thread uđe u `ensure` block.
2. Izvršavanje se prebaci na thread i počne se izvršavati `sleep`.
3. Izvršavanje se nikad ne prebaci na main thread procesa jer je označen kao *killed*.

Pouka: ne stavljaj `sleep` u `ensure` blokove threada :)

## Fibers

Fiberi omogućuju prekid izvršavanja nekog bloka i nastavak po pozivu.

`fiber = Fiber.new { ... }` stvara novi fiber koji se ne pokreće dok ne pozoveš `fiber.resume`. Blok se izvršava dok ne dođe do `Fiber.yield(res)`, pri čemu izlazi iz bloka i vrati `res`. S `fiber.resume` može opet nastaviti izvršavanje u bloku dok god ne dođe do `yield` ili kraja bloka.

S `fiber.transfer(res)` možeš prebaciti izvršavanje s jednog fibera na drugi.
Fiberi se time mogu kao threadovi izvoditi neovisno od toka programa, samo threadovima upravlja scheduler, a fiberima programer.

## Continuations

Continuation omogućuje skok na neko prijašnje mjesto u kodu. Da, kao `goto`.
Continuation objekt sadrži snapshot stack framea.

`callcc { |cc| $label = cc } ...` postavlja da svaki vanjski `$label.call` vrati izvođenje na kraj cc bloka.

`callcc { |cc| ... cc.call ...}` postavlja izvođenje na kraj bloka, tj. iskače iz bloka.

## Ruby 3 Guild Proposal

http://www.atdot.net/~ko1/activities/2016_rubykaigi.pdf

Želimo omogućiti paralelizam, ali da ne moramo razmišljati o lockovima.
*Guild* je nova apstrakcija.

Svaki guild ima jedan ili više thread, i threadovi u različitim guildovima se mogu izvršavati paralelno.

Svi mutable objekti pripadaju jednom guildu i ne može im se pristupati iz drugoga. Zahvaljujući tome nema race conditiona, pa ni potrebe za lockovima.

`Guild::Channel.transfer` za prosljeđivanje immutable objekata (najbolji slučaj).
`Guild::Channel.copy` za slanje deep kopije objekta (sigurno, ali sporije).
`Guild::Channel.transfer_membership` za prebacivanje mutable objekata (brišu se iz prvog guilda).

Posebna struktura za dijeljenje mutable objekata - potrebni lockovi, ali to koristi se samo u rijetkim slučajevima.

## Thread safe collections

https://github.com/hamstergem/hamster

Gem s immutable strukturama podataka (Hash, Vector, Set, SortedList, List, Deque) koje su po definiciji thread safe.

# TODO

rack.hijack i message_bus
background jobs (resque koristi procese, sidekiq threadove, suckerpunch)

# Literatura

* http://www.jstorimer.com/blogs/workingwithcode/8085491-nobody-understands-the-gil
* https://www.toptal.com/ruby/ruby-concurrency-and-parallelism-a-practical-primer
