# Concurrency

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

## Async

`Async` je gem koji omogućava asinkrono izvođenje metoda korištenjem fibera. Fiberi su mnogo više lightweight od threadova (može ih se stvoriti na tisuće u jednom threadu), a od `Ruby 3.0` postoji i ugrađen fiber scheduler napravljen upravo za integraciju s `async` gemom. Koristi se *cooperative scheduling*, gdje se fiber yielda kad se pozove određena metoda.

    Async do |task|
      task.async { URI.open("https://httpbin.org/delay/1.6") }
      task.async { URI.open("https://httpbin.org/delay/1.6") }
    end

Blokirajuće metode kao što su `sleep`, `read`, i `write` yieldat će fiber dok se operacije ne završi. Na taj način se može više sporih IO operacija obaviti istovremeno. Zahvaljujući novom fiber scheduleru, blokirajuće metode se automatski pretvaraju u neblokirajuće, bez da se mora pisati poseban kod.

## GVL

CRuby (standardna Ruby implementacija poznata kao i MRI) ima *global VM lock* (GVL, poznat kao i GIL). On ne dopušta da se više threadova istog Ruby procesa izvršava istovremeno. To znači da Ruby proces ne može koristiti više jezgri procesora u istom trenutku.

GVL se koristi jer Ruby VM internalsi nisu thread-safe (teško je napisati dobar thread-safe VM - i Python i JS imaju isti problem i koriste neku vrstu globalnog locka). GVL osigurava da se VM izvršava u thread-safe kontekstu, ali **ne garantira** da će i tvoj aplikacijski Ruby kod biti thread-safe. Thread scheduler i dalje može pauzirati thread u bilo kojem trenutku, i aktivirati drugi thread koji će npr. prepisati zajedničku varijablu.

Drugi Ruby interpreteri (JRuby, Rubinius) nemaju ovo ograničenje (umjesto GVLa imaju mnogo malih internih lockova), te će se kod njih multi-threaded programi izvršavati paralelno. Ali koristiti multi-threaded programe ima smisla i za MRI: ako thread mora čekati na IO (HTTPS request, DB query, čitanje i pisanje na disk), MRI će se prebaciti na drugi thread koji nije blokiran.

## Timeout i thread.raise

Ruby `Timeout` i `Rack::Timeout` rade tako da stvaraju novi thread koji spava zadano vrijeme, a kada se probudi poziva `thread.raise(Timeout::Error)` nad main threadom.

`thread.raise` je izuzetno nezgodan jer može izazvati exception u bilo kojem trenutku main threada. To može biti tijekom nekog spore radnje, ali može biti i unutar cleanupa te radnje, ili čak nevezanog `rescue` ili `ensure` bloka za kojeg pretpostavljaš da će se uvijek izvršiti do kraja.

Ovo može izazvati razne probleme poput neotpuštanja db konekcija, ili postavljanja threada u unrecoverable state dok mu server neznajući i dalje prosljeđuje requeste.

Ovo nije problem specifičan za Ruby - to je problem koncepta timeouta koji će prekinuti svaku radnju nakon određenog vremena. Sigurnije rješenje je koristiti timeout implementiran za sami request, npr. Mysql timeout. Više informacija na https://github.com/ankane/the-ultimate-guide-to-ruby-timeouts.

`Rack::Timeout` je i dalje bolja opcija nego dopuštati jako duge requeste koji će stvoriti zastoj na serveru. Samo treba biti svjestan da svaki `Timeout::Error` koji se pozove može izazvati probleme.

## Thread safety

Rails framework code je thread-safe. Isprva se koristio `Rack::Lock` kao mutex oko svakog requesta kako bi se osigurao thread safety, ali po cijenu toga da se requesti nisu mogli istovremeno izvršavati.

Od `4.0` lock je uklonjen što je omogućilo razvoj thread-based servera poput Pume. Ipak, pri pisanju aplikacijskog koda treba paziti na varijable koje se dijele između threadova: *globalne*, *class*, *class instance* i *konstante*.

Čak i memoizacija takvih dijeljenih varijabli može dovesti to race-conditiona. Ako se mijenja i memoizira vrijednost `@attr ||= super.merge(...)`, pazi da se u `super` ne memoizira variabla s istim nazivom, npr. `@attr ||= {}`. Ovo je bio uzrok dugo traženog buga pri inicijalizaciji Railsa [https://github.com/rails/rails/pull/9789].

## Guilds

Želimo omogućiti paralelizam, ali da ne moramo razmišljati o lockovima.
*Guild* je nova apstrakcija.

Svaki guild ima jedan ili više thread, i threadovi u različitim guildovima se mogu izvršavati paralelno.

Svi mutable objekti pripadaju jednom guildu i ne može im se pristupati iz drugoga. Zahvaljujući tome nema race conditiona, pa ni potrebe za lockovima.

`Guild::Channel.transfer` za prosljeđivanje immutable objekata (najbolji slučaj).
`Guild::Channel.copy` za slanje deep kopije objekta (sigurno, ali sporije).
`Guild::Channel.transfer_membership` za prebacivanje mutable objekata (brišu se iz prvog guilda).

Posebna struktura za dijeljenje mutable objekata - potrebni lockovi, ali to koristi se samo u rijetkim slučajevima.

# Literatura

* [https://www.speedshop.co/2020/05/11/the-ruby-gvl-and-scaling.html]
* [https://engineering.universe.com/introduction-to-concurrency-models-with-ruby-part-i-550d0dbb970]
