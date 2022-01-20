# Concurrency

*Concurrent* znači da se dvije radnje izvršavaju u istom *vremenskom intervalu*. *Parallel* znači da se dvije radnje izvršavaju u istom *trenutku*.

Concurrency je način strukturiranja programa kako bi se mogao (ali ne i nužno morao) paralelizirati.

## Multiple processes vs Multi-threaded

* Threadovi zauzimaju manje memorije od procesa
* Threadovi se brže stvaraju, uništavaju, i lakše se switcha između njih jer koriste istu memoriju.
* Threadovi uvijek umiru kad im parent proces umre; forkani procesi koji ne završe prije parenta postaju zombiji.
* Threadovi mogu komunicirati preko `Queue`, procesi moraju koristiti pipeove ili sockete.
* Procesi su memorijski izolirani pa se ne moraš brinuti za race conditione, lakši su za programiranje i debugiranje.
* Procesi pomažu izbjeći memory leakove jer se sva memorija oslobađa kada child proces završi.

Ukratko: threadovi su jeftiniji, ali moraš biti puno oprezniji s njima. Procesi su skuplji, ali daju ti sigurnost.

## Thread Pooling

Ako imaš jako puno malih zadataka koje želiš obavljati istovremeno, overhead stvaranja novog threada za svaki može biti velik. Usto, threadovi jesu jeftini, ali nisu besplatni. Ako kreiraš 1000 threadova odjednom, ostat ćeš bez resourca.

Da bi se to izbjeglo, koristi *thread pooling*. Pool ima određenu veličinu koja određuje koliko će threadova unaprijed stvoriti (ili u slučaju lazy poola, koji je maksimum koji će stvoriti). Pool zadatke koje dobije prosljeđuje trenutno idle threadu.

## Preemptive i Cooperative Scheduling

U *cooperative* schedulingu, procesi odlučuju kada su gotovi i predaju kontrolu OS-u. To je problematično jer se proces može zablokirati i onda svi čekaju.

U *preemptive* schedulingu, OS odlučuje koji se proces izvršava i kada će izvršavanje prebaciti na idućeg.

Mnogi jezici implementiraju svoj scheduling. Javascript implementira cooperative scheduling - ako se u async event loopu stavi `sleep`, cijeli će browser morati čekati na njega.

## Async Functions

Async funkcije su dosta nezgodnije od sync funkcija: pošto ne vraćaju rezultat direktno, moraju se pozivati s *callback* funkcijom. Stanje prije async poziva sprema se u closure callback funkcije, i jednom kad je async operacija gotova, callback se izvršava.

Jedan način lakšeg rada s async funkcijama je `promise`, koji je syntactic sugar oko callbacka. Još bolji je `await` koji u compileru razbija funkciju na dva dijela i dio ispod awaita stavlja u callback.

Drugi pristup je korištenje `threadova`. Ovdje će poziv async funkcije će  pauzirati izvršavanje trenutnog threada i prebaciti se na neki drugi (thread pritom ne mora biti nativni OS thread, može biti *green thread* ili *fiber*). Prednost ovog je da programer ne mora posebno handlati asinkroni kod: sinkrone i asinkrone funkcija se jednako koriste, a OS / event loop se pobrinu za scheduling.

## Concurrency models

Postoje različiti modeli concurrent sustava koji ne zahtjevaju korištenje lockova i garantiraju thread-safety.

**Actors**: Svaki actor može slati poruke, definirati ponašanje na primljenu poruku, i stvarati nove actore. Svako ima privatno stanje koje se ne sharea, pa nema potrebe za lockovima. Shareanje se ostvaruje komuniciranjem. Erlang i Scala implementiraju actore u samom jeziku. Ruby gem `Celluloid` implementira actore tako da je svaki u zasebnom threadu.

**Communicating Sequential Processes**: Kao i kod actora, komunicira se porukama bez dijeljene memorije, ali se ne identificiraju procesi već kanali kojima procesi komuniciraju. Svaki kanal može držati samo jednu poruku, pa garantiraju da poruke stižu redom koje su poslane. Koriste ga Go (`goroutines` i `channels`) i Clojure (`core.async`).

**Software Transactional Memory**: Koristi se dijeljena memorija, ali u atomarnim transakcijama (slično kao DB). Vrijednosti unutar transakcije se mijenjaju, ali ostali ih procesi ne vide dok se transakcija ne commita. Ako se transakcija ne može commitati zbog conflicta, retrya se dok ne uspije. Koriste ga Clojure (`Refs`) i `concurrent-ruby` gem (`TVar`).
