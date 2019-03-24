# Memory

## Measuring memory

`ObjectSpace.memsize_of(obj)` vraća otprilike broj byteova koje objekt koristi

`ObjectSpace.allocation_sourcefile(obj)`, `ObjectSpace.allocation_sourceline(obj)` za otkrivanje gdje je objekt alociran.

`report = MemoryProfiler.report { obj = ... }` mjeri koliko se bytova alociralo, a koliko je ih je zadržano u memoriji nakon završetka bloka.

## Ruby GC

Svi objekti se spremaju na heap kojeg kontrolira MRI. Heap se sastoji od pageva. Svaki page je velik `16KB`, i sadrži `408` slotova u koje se spremaju informacije o jednom objektu. Svaki objekt isprva zauzima `40` byteova. Kada se objekt stvara, MRI traži slobodno mjesto na heapu. Ako ga nema, dodaje se novi page.

Ruby `2.1` uveo je *generacijski GC*. On radi u dvije faze: *minor GC* čisti nedavno stvorene objekte (jer većina objekata traje kratko), a *major GC* prolazi kroz *sve* objekte, ali se poziva rjeđe.

Generacijski GC i dalje koristi stop-the-world implementaciju - kad se GC pokrene, izvršavanje programi se zaustavlja. To može trajati i do `100ms`, što je prilično loše ako se dogodi tijekom handlanja requesta.

Ruby `2.2` uveo je *inkrementalni GC* koji dijeli GC u sitne procese. Umjesto jedne duge pauze, dogodit će se više kratkih pauza. Zahvaljujući tome performance je mnogo konzistentniji.

GC se ne pokreće svakih X sekunda ili alokacija. Minor GC se pokreće kada se ostane bez slobodnih slotova. Major GC se pokreće ako i nakon minor GC-a nema slobodnih slotova, ili kada broj starih objekata dođe do zadanog limita.

### GC.stat

`GC.stat` vraća hash s različitim statistikama vezanim za GC.

`count` je ukupan broj poziva GC-a. Podijeljen je na `minor_gc_count` i `major_gc_count`. Pomoću njega možeš detektirati poziva li neki job GC (ali pripazi da nema neki drugi thread koji ga može pozvati istovremeno).

`heap_allocated_pages` je broj alociranih pageva. `heap_sorted_length` je broj pageva zauzetih u memoriji (ako se od deset pageva peti oslobodi, `length` ostaje isti jer se pagevi ne mogu premještati). `heap_allocatable_pages` su dijelovi memorije koje je Ruby alocirao, ali nije još pridružio pageu.

`heap_available_slots` je ukupan broj slotova u heapu. Dijeli se na `heap_live_slots` (live objekti), `heap_free_slots` (prazni slotovi) i `heap_final_slots` gdje su finalizeri objekata (nešto kao destruktori, to nitko ne koristi). `heap_marked_slots` je broj starih objekata (koji su preživjeli više od 3 GC ciklusa) plus *write barrier unprotected* objekata.

`tomb_pages` su pagevi koji nemaju live objekata, `eden_pages` imaju bar jedan. Ruby može OS-u vraćati samo tomb pageve.

## Memory Tips

Memorija ne treba izgledati kao ravna crta, nego raste logaritamski. Na početku se requirea aplikacijski kod, pune se cachevi i connection poolovi. Najčešće kad ti se čini da aplikacija leaka memoriju, zapravo gledaš prekratak interval ili ti je worker killer postavljen na prenizak prag.

Smanji broj procesa koje koristiš i ciljaj zauzeti oko 300 Mb po procesu.

Alociriranje puno objekata odjednom može uzrokovati fragmentaciju heapa zbog kojeg se djelovi memorije ne mogu osloboditi. Zbog toga se čini da potrošnja memorije konstatno raste.

Alociraj manje. Za profiliranje memorije koristi scout ili skylight (bolji od new relica za memoriju); ili `oink` i `memory_profiler` ako ne želiš plaćati servis.

U krajnjem slučaju, jobove koji puno alociraju prebaci u rake task i vrti u zasebnom VM-u kako ne bi neredili memoriju glavnih instanci.

Nijedan dependancy nije besplatan. `derailed` prolazi kroz gemove i ispisuje koliko koji troši memorije. Pokušaj koristiti `require: false` za assete, ne trebaju ti u produkciji, a troše memoriju.

Koristi preloading na pumi i unicornu. Forkanje nakon inicijalizacije omogućuje shareanje copy-on-write memorije.

Nemoj mijenjati GC settingse osim ako stvarno znaš što radiš, vjerojatno ćeš samo pogoršati situaciju, a dobitci su minimalni.

Velike aplikacije s puno threadova (npr. Sidekiq workeri koji imaju defaultno 25 threadova) izazivaju veliku fragmentaciju jer Ruby koristi glibcmalloc koji je loš. Ili smanji broj threadova, ili postavi `MALLOC_ARENA_MAX = 2`, ili se nadaj da će Ruby uskoro shippati s jemallocom.

## String memory reduction

Stringovi su najčešći krivac za veliku potrošnju memorije. Stavljanje komentara `frozen_string_literal: true` na početak filea će tretirati sve stringove u kodu kao immutable.

Za freezanje pojedinačnih stringova, koristi `-` (npr. `-"frozen string"`). Ruby će automatski deduplicirati frozen stringove, pa će više pojavljivanja istog stringa biti samo jednom alocirano.

Korištenje stringova za spremanje ili dohvaćanje iz hasha se automatski freeza, pa je umjesto `foo["a".freeze]` dovoljno pisati `foo["a"]`. Isto vrijedi i za `case when` izraze.

## Memory Leaks

Objekti se mogu podijeliti u 3 grupe:
* Statics: framework, svi gemovi, i aplikacijski kod. Jednom kad se učitaju ne oslobađaju se.
* Slow Dynamics: objekti koji dugo žive, npr. cache prepared SQL izraza u ActiveRecordu. Sporo raste, ali do zadanog limita.
* Fast Dynamics: objekti kreirani prilikom parsiranja requesta i generiranja responsa. Kad je response spreman, ovi objekti se mogu osloboditi.

Minimalna veličina heapa je zbroj prve i druge grupe. Ako se 3. grupa ne oslobodi, imamo leak i heap konstatno raste. Drugim rječima, ako je zauzeće memorije stalno uzlazno, postoji leak kojeg GC ne uspjeva počistiti.

Da bi dokazao da leak postoji, prebaci produkcijske podatke u lokalni server za testiranje. Dohvati stvarne requeste iz `heroku logs -t`. Simuliraj stvarni promet sa `siege -v -c 15 --log=/tmp/siege.log -f urls.txt` koji šalje po 15 paralelnih poziva. `ps -eo pid,rss | grep #{Process.pid}` dohvaća *Resident Set Size* (koliko memorije zauzima u RAMu) za trenutni proces. Ako se *RSS* konstantno povećava, dokazano postoji leak.

Provjeri je li leak u Ruby kodu. Koristi `gc_tracer` gem koji se ubaci kao Rack Middleware i ispisuje milijun podataka u svoj log. Nas zanimaju *RSS* i broj starih objekata (koje će samo major GC gledati) kroz vrijeme. Plotamo ih s `gnuplot` i ako je broj starih objekata stabilan, a RSS raste - to znači da leak nije u Ruby kodu. Ako broj starih objekata raste, treba pronaći gdje se ti objekti alociraju.

`ObjectSpace.trace_object_allocations_start` bilježi alokacije (ovo će dosta usporiti proces, pa ne koristi u produkciji). Prati RSS, i nakon što je memorija očito leakala, pozovi `ObjectSpace.dump_all(output: file)`. On vraća JSON s objektima sortiranim po GC generacijama. Tu možeš pronaći koji kod alocira koliko objekata.

Ako leak nije u Ruby kodu, onda je u C kodu, tj, vjerojatno je neki Gem u pitanju.

## Real World Memory Optimizations

### mime-types (https://github.com/mime-types/ruby-mime-types/issues/94)

`mime-types` je držao sve tipove u jednom velikom jsonu. Od tog hasha su se onda stvarali `Mime::Type` objekti koji su se čuvali u memoriji. Sve skupa gem je zauzimao 15 MB memorije, što je poprilično.

Umjesto jsona, tipove su podijelili u fileove, po jedan file za svaki atribut, po jedan red za svaki tip. Pošto velika većina aplikacija ne treba sve tipove, `Mime::Type` objekti se lazy loadaju. To je malo usporilo loadanje, ali 10x smanjilo korištenu memoriju.

### ReadCube

ReadCube dashboard reportovi trošili su previše memorije. S `memory_profiler` izmjerio sam da najviše alociraju `number_to_currency` iz ActiveSupporta koji sam includao, `Carmen` koji defaultno učitava sve zemlje na svim jezicima, i `CSV.generate_line` koji svaki put iznova radi `CSV.new`. Svaki pojedinačno nije puno trošio, ali kad se svaki zove 200.000 puta, svaka pretjerana alokacija se osjeti.

# Literatura

* https://www.speedshop.co/2017/03/09/a-guide-to-gc-stat.html
* http://www.be9.io/2015/09/21/memory-leak/
* https://samsaffron.com/archive/2015/03/31/debugging-memory-leaks-in-ruby
* https://www.youtube.com/watch?v=kZcqyuPeDao - Halve Your Memory Usage With These 12 Weird Tricks
* https://rubytalk.org/t/psa-string-memory-use-reduction-techniques/74477
