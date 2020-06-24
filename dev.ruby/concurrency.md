# Concurrency

## Procesi

`fork { ... }` stvara novi `ruby` proces koji će izvoditi naredbe unutar bloka, dok će parent nastaviti izvoditi naredbe nakon `fork`. Vraća `pid` stvorenog subprocesa. Ruby 2.0+ podržava copy on write.

`if pid = fork ... else  ...` stvara novi proces koji će vratiti `nil` i izvoditi `else` dio izraza, dok će parent vratiti `pid` i izvoditi gornji `if` dio.

`exit(code)` prekida proces i vraća status `code`. Poziva se `at_exit` handler.
`exit!(code)` prekida proces, vraća `code`, ali ne poziva `at_exit`.

`Process.wait(pid)` blokira trenutni proces dok se proces `pid` ne završi.
`Process.wait` blokira dok se bilokoji subproces ne završi, vraća njegov `pid`.
`Process.wait2` vraća `pid` i `exit_code` završenog subprocesa.
`Process.waitall` blokira dok svi subprocesi ne završe.

Ukoliko se subproces završi prije nego parent pozove `wait`, kernel će tu informaciju queuati i bit će dostupna kada parent konačno pozove `wait`. Tako se izbjegavaju bilo kakvi race conditioni.

Ukoliko parent ne čeka svoj subproces da završi, njegov exit status će ostati queuean i zauzimati resurse - postat će zombie proces. Stoga, ako ne koristiš `Process.wait(pid)`, koristi `Process.detach(pid)` koji interno stvara novi thread čiji je jedini zadatak da čeka child process i pokupi njegov exit status.

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

Svaki Ruby proces ima barem jedan thread: main thread. `Thread.main` ga vraća.

`Thread.new { ... }` stvara novi thread koji izvodi naredbe u bloku.
Threadovi imaju closure, i vidljive su im sve varijable u scopeu. Da bi izbjegao thread unsafety, za predaju parametara threadu koristi `Thread.new(param) { |x| ... }` koji radi kopiju lokalne varijable, pa je možeš bez straha mijenjati.

`thread.join` blokira main thread dok se `thread` ne završi. U suprotnom, svi threadovi će biti prekinuti kada završi main thread.

`Thread.main` je main thread.
`Thread.current` je thread koji se trenutno izvršava.
`Thread.list` vraća array neprekinutih threadova.

`Thread.stop(thread)` privremeno zaustavlja izvođenje threada. `thread.wakeup` ga ponovno označava za nastavak izvođenja.
`Thread.pass` predlaže scheduleru da preuzme neki drugi thread, ali ne garantira da će se to dogoditi.

`thread.exit` (`thread.kill`) prekida `thread` s `exit`. Ukoliko je to zadnji ili main thread, prekida se proces. Kada main thread završi, gasi se i proces, a svim ostalim threadovima se šalje `thread.exit`.

`thread.status` vraća trenutno stanje threada: `sleep`, `run`, `aborting`; `false` ako je prekinut normalno, `nil` ako je prekinut s exceptionom.
`thread.alive?` ako se izvršava ili spava, `thread.stop?` ako je prekinut ili spava.

`thread[:key]` spremanje thread varijabli kojima i drugi threadovi mogu pristupati. Zapravo su Fiber-local.

Ako se u threadu dogodi exception, ispisati će se u stderr (u starijim verzijama Rubija će samo silently failati). Koristi globalni `Thread.abort_on_exception = true` da se exception propagira u main thread.

Za sinkorinizaciju koristi, stvori `mutex = Mutex.new` izvan threadova.
* `mutex.synchronize do ...` unutar threada. Ako je mutex slobodan, thread će izvršiti blok; ako nije, blokirat će se dok se ne izvrši.
* `mutex.try_lock` ne blokira, nego ili locka mutex i vrati `true`, ili vrati `false`.

`Queue` je threadsafe struktura. `enq` dodaje element, `deq` uzima ili blokira dok ne stigne novi.

`hamster` gem sadrži immutable strukture podataka (Hash, Vector, Set, SortedList, List, Deque) koje su po definiciji thread safe.

Najčešće ne želiš sam handlati pokretanje threadova, nego koristi `concurrent-ruby` gem koji riješava probleme kao što je thread pooling.

## Fibers

Fiberi omogućuju prekid izvršavanja nekog bloka i nastavak po pozivu.

`fiber = Fiber.new { ... }` stvara novi fiber koji se ne pokreće dok ne pozoveš `fiber.resume`. Blok se izvršava dok ne dođe do `Fiber.yield(res)`, pri čemu izlazi iz bloka i vrati `res`. S `fiber.resume` može opet nastaviti izvršavanje u bloku dok god ne dođe do `yield` ili kraja bloka.

S `fiber.transfer(res)` možeš prebaciti izvršavanje s jednog fibera na drugi.
Fiberi se time mogu kao threadovi izvoditi neovisno od toka programa, samo threadovima upravlja scheduler, a fiberima programer. Time se mogu upotrijebiti za pisanje asinkronog concurrent koda, kao što je npr. server `falcon`.

`Enumerator` zapravo interno koristi fibere, pri čemu se fiber pauzira dok ne dohvatiš idući element.

## Continuations

Continuation omogućuje skok na neko prijašnje mjesto u kodu. Da, kao `goto`.
Continuation objekt sadrži snapshot stack framea.

`callcc { |cc| $label = cc } ...` postavlja da svaki vanjski `$label.call` vrati izvođenje na kraj cc bloka.

`callcc { |cc| ... cc.call ...}` postavlja izvođenje na kraj bloka, tj. iskače iz bloka.

## GVL

CRuby (standardna Ruby implementacija poznata kao i MRI) ima *global VM lock* (GVL, poznat kao i GIL). On ne dopušta da se više threadova istog Ruby procesa izvršava istovremeno. To znači da Ruby proces ne može koristiti više jezgri procesora u istom trenutku.

GVL se koristi jer Ruby VM internalsi nisu thread-safe (teško je napisati dobar thread-safe VM - i Python i JS imaju isti problem i koriste neku vrstu globalnog locka). GVL osigurava da se VM izvršava u thread-safe kontekstu, ali **ne garantira** da će i tvoj aplikacijski Ruby kod biti thread-safe. Thread scheduler i dalje može pauzirati thread u bilo kojem trenutku, i aktivirati drugi thread koji će npr. prepisati zajedničku varijablu.

Drugi Ruby interpreteri (JRuby, Rubinius) nemaju ovo ograničenje (umjesto GVLa imaju mnogo malih internih lockova), te će se kod njih multi-threaded programi izvršavati paralelno. Ali koristiti multi-threaded programe ima smisla i za MRI: ako thread mora čekati na IO (HTTPS request, DB query, čitanje i pisanje na disk), MRI će se prebaciti na drugi thread koji nije blokiran.

## Signals

U slučaju da se multithreaded procesu pošalje signal, kernel će nasumično odabrati jedan thread kojem će prenijeti taj signal. Kako bi izbjegao probleme koje to može izazvati, MRI (i druge Ruby implementacije) pri pokretanju stvaraju poseban thread koji je zadužen za handlanje signala i pipeom ih šalje main threadu.

`Process.kill("INT", pid)` šalje signal procesu s danim `pid`. `INT` će uzrokovati isti signal kao da si stisnuo `CTRL+C` u terminalu.

`Signal.trap("INT") do ... end` definira globalno handlanje signala u
trenutnom procesu. Moguće je definirati samo jedan handler po signalu, pa pripazi da ne overridaš handler koji je neki gem dodao.

`Signal.trap("INT", "IGNORE")` definira ignoriranje `INT` signala u trenutnom procesu.

Neuhvaćeni i neignorirani signali podižu `SignalException`, pa ako želiš handlati signal samo u određenom dijelu koda možeš koristiti `rescue`. Npr. `rescue Interrupt` za handlanje `INT` signala.

Imaj na umu da je dojava signala nepouzdana. Npr. ako proces handla `INT` signal dok mu se šalje drugi `INT` signal, ovaj drugi signal možda neće ni primiti.

## Timeout i thread.raise

Ruby `Timeout` i `Rack::Timeout` rade tako da stvaraju novi thread koji spava zadano vrijeme, a kada se probudi poziva `thread.raise(Timeout::Error)` nad main threadom.

`thread.raise` je izuzetno nezgodan jer može izazvati exception u bilo kojem trenutku main threada. To može biti tijekom nekog spore radnje, ali može biti i unutar cleanupa te radnje, ili čak nevezanog `rescue` ili `ensure` bloka za kojeg pretpostavljaš da će se uvijek izvršiti do kraja.

Ovo može izazvati razne probleme poput neotpuštanja db konekcija, ili postavljanja threada u unrecoverable state dok mu server neznajući i dalje prosljeđuje requeste.

Ovo nije problem specifičan za Ruby - to je problem koncepta timeouta koji će prekinuti svaku radnju nakon određenog vremena. Sigurnije rješenje je koristiti timeout implementiran za sami request, npr. Mysql timeout. Više informacija na https://github.com/ankane/the-ultimate-guide-to-ruby-timeouts.

`Rack::Timeout` je i dalje bolja opcija nego dopuštati jako duge requeste koji će stvoriti zastoj na serveru. Samo treba biti svjestan da svaki `Timeout::Error` koji se pozove može izazvati probleme.

## Rails Thread safety

Rails framework code je thread-safe. Ako se svaki request obrađuje u svom threadu, defaultno nema zajedničkih varijabli koje bi mogle izazvati problem. Ipak, u našem kodu možemo nešto slučajno podijeliti kroz:
* globalne varijable
* class varijable (koje su principu isto što i globalne)
* class instance varijable (opet, klase se dijele, pa tako i njihove instance varijable)
* konstante (ako ih nedajbože mijenjaš u kodu)

Jedan neobičan thread-unsafe slučaj je memoizacija instance varijabli. Ako radiš `@attr ||= super.merge(...)`, pripazi da koristiš različito ime `@attr` od onog koje se koristi u `super` metodi. (https://github.com/rails/rails/pull/9789/files)

## Kamo je nestao config.threadsafe?

http://tenderlovemaking.com/2012/06/18/removing-config-threadsafe.html

Prije Rails 4.0 postojao je flag `config.threadsafe!` koji je sad po defaultu enablan. On radi dvije stvari: preloada framework i developer code pri bootu, te uklanja `Rack::Lock` middleware.

Preloading je, za razliku od lazy loadanja pomoću `autoload` metode, thread-safe. Kada je uključen, nema nikakve razlike za *multi-process* i *multi-threaded* sustave. Jedino će preload frameworka zauzeti nešto više memorije jer učitava cijeli framework, a ne samo dijelove koji se koriste. (Nadajmo se da će Rails biti malo pametniji s tim u budućnosti.)

`Rack::Lock` mutexom oko requesta brani različitim threadovima da u isto vrijeme izvršavaju kod. U *multi-process* sustavu (npr. Unicorn) nije potreban, jer svaki proces ima svoju memoriju i čak ni thread-unsafe kod neće imati problema. U *multi-threaded* sustavu (npr. Puma) mutex sprečava thread-unsafe situacije, što je super, ali istovremeno i ne dopušta da se threadovi istovremeno vrte, što je poanta *multi-threadanja*. Zato su odlučili ukloniti ga, i svima koji koriste *multi-threaded* servere reći: "Pazite da vam je kod thread-safe."

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

# Literatura

* https://www.speedshop.co/2020/05/11/the-ruby-gvl-and-scaling.html
* https://engineering.universe.com/introduction-to-concurrency-models-with-ruby-part-i-550d0dbb970
