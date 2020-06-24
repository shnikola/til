# Processes

*Proces* sadrži sve resurse koji su potrebni da se izvrši program: memorijski prostor, kod za izvršenje, handlove na sistemske objekte, environment varijable, i *glavni thread* izvođenja.

Svaki proces ima unikatni identifikator `pid`. `pid` trenutnog procesa zapisan je u `$$` varijabli u Bashu. Svaki proces sadrži i `ppid`, identifikator svog parent procesa.

Svaki proces sadrži file deskriptore korištenih sistemskih resursa (fileova, deviceova, pipeova, socketa). File deskriptori su integeri koji se sekvencijalno pridružuju otvorenim resursima. Zatvaranje resursa ih oslobađa. `0`, `1`, i `2` su redom pridruženi `stdin`, `stdout` i `stderr` resursima.

Procesi imaju deafultna ograničenja o količini resursa koje mogu koristiti (npr. broj otvorenih file descriptora, veličina filea, veličina stacka). U rijetkim slučajevima kada ih želiš promijeniti, koristi `ulimit`.

Svaki proces sadrži key-value set environment varijabli. Varijable postavljene u parent procesu nasljeđuju svi child procesi, npr. `RAILS_ENV=production rails s`. Drugi način prosljeđivanja parametara je kroz argumente koji se zapisuju u `$@` varijablu.

Svaki proces sadrži barem jedan thread. Thread je zadužen za samo izvršavanje koda. Threadovi sadrže malu količinu resursa: thread-local varijable i stack s pointerom na liniju koda koju izvršavaju. Zato se mogu brzo stvarati i gasiti.

## /proc

Svi procesi nalaze se u `/proc` directoriju (zapravo virtualni filesystem čiji deskriptori pokazaju na memoriju, a ne na disk).

Svaki proces živi u subdirecotoriju sa svojim PID-om, npr. `/proc/42`:
* `/proc/42/environ` sadrži env varijable procesa.
* `/proc/42/fd` sadrži linkove na otvorene fileove.
* `/proc/42/cmdline` sadrži argumente s kojima je pokrenut.

## Forking

`fork(2)` stvara child proces koji od parenta nasljeđuje sve file descriptore, te kopiju svega što parent ima u memoriji. Svaki proces ima odvojen memorijski prostor, pa je može slobodno mijenjati bez da utječe na ostale.

Kako je skupo alocirati veliki komad memorije za svaki fork, koristi se *Copy on write* mehanizam. Isprva se dopušta da procesi koriste istu memoriju, a radi kopiju tek kada jedan od njih pokuša pisati u nju.

`wait(2)` parenta blokira dok se child proces ne završi. Ukoliko se child proces završi, a parent ne pozove `wait`, on postaje zombie jer njegov exit status ostaje zapisan u kernelu i zauzima resurse. Uvijek parentom očisti exit statuse koristeći `wait`.

Ako parent proces završi prije childa, child postaje orphaned proces. Njegov `ppid` se postavlja na `1`, što je `pid` uvijek prisutnog `init` procesa. OS ne tretira orphaned procese posebno i pušta ih da se izvode kao svi ostali.

## Process Groups and Session Groups

Svaki proces pripada u jedan *process group*. Grupe sadrže povezane procese (npr. parent i njegovi childovi pripadaju jednoj grupi), ali proces se može po potrebi prebaciti iz jedne grupe u drugu.

*Session group* sadrži više *process groupa*. Naredba `git log | grep abc | less` u terminalu stvara session group, a svaki `git`, `grep` i `less` svaki svoj process group.

## Signals

Signali su oblik asinkrone komunikacije jednog procesa prema drugom. Svaki signal uzrokuje defaultno ponašanje, a proces može posebno definirati da ignorira signal ili reagira na određeni način.

Npr. `INT` signal se koristi za prekidanje rada procesa. `USR1` i `USR2` signali se koriste za korisničko definirano ponašanje.

`kill -<sig> <pid>` šalje signal procesu s danim pidom, npr. `kill -INT 12`.

Kad se interrupt signal šalje pomoću `kill`, interrupta se samo odabrani proces, a ne i njegovi child procesi. Ako process sam ne handla prosljeđivanje signala do djece, ostat će orphaned procesi. U tom je slučaju bolje poslati signal cijelog grupi s `kill -INT -- -<pgid>`.

Kada se signal šalje iz terminala (npr. `CTRL+C`), prosljeđuje se svim procesima u foreground session grupi, što uključuje foreground proces i sve njegove child procese. Zbog toga keyboard interrupt prekida sve pipeane procese i ne ostavlja orphaned procese.

## Communication

Najjednostavniji način komunikacije među procesima je mijenjanje imena procesa. Ime procesa se može promijeniti bilo kad i neće utjecati na njegov `pid`.

Drugi način komunikacije su exit codes. Proces pri svom završetku vraća numeričku vrijedtnost: `0` se smatra uspješnim završetkom, a `1-255` različitim error codeovima, ovisno o programu.

Za streamanje veće količine podataka koristi pipeove. Pipeovi su resursi s dva file descriptora, jednim za čitanje i jednim za pisanje. Jednosmjerni su, pa ih koristi ako jedan proces uvijek piše, a drugi uvijek čita.

Za komunikaciju cjelovitim porukama koristi sockete. Unix socketi su slični kao TCP socketi, samo omogućuju komunikaciju unutar istog stroja. Dvosmjerni su pa oba procesa mogu istovremeno čitati i pisati.

## Background Jobs and Daemons

Shell svaku pokrenutu naredbu (proces ili više povezanih s `|`) veže za *job*. Job može biti foreground (zauzima shell dok ne završi, može biti samo jedan) ili background (može ih više biti pokrenuti odjednom).

Svaki job nasljeđuje stdin, stdout i stderr od terminala koji ih je stvorio. Background jobovi se ispisuju na stdout, a pri pokušaju da čitaju sa stdin se pauziraju.

`CTRL + C` šalje `SIGINT` foreground jobu, završava izvođenje.
`CTRL + Z` šalje `SIGTSTP` foreground jobu, pauzira izvođenje, šalje ga u background i vraća kontrolu terminalu.
`<cmd> &` pokreće naredbu u backgroundu. Ispisuje `[1] 4287`: job number i `pid`.

`jobs` ispisuje sve aktivne jobove (pauzirane i background procese) ovog terminala.
`fg` uzima prvi aktivni job i stavlja ga u foreground. `fg %42` ako znaš job number.
`bg` uzima prvi aktivni job i stavlja ga u background (kao da je s `&` pozvan). Korisno u kombinaciji s `CTRL+Z`.
`kill %42` završava izvođenje joba po job numberu.

Kada se terminal zatvara, svim svojim jobovima šalje `SIGHUP` signal da se prekinu izvoditi. Postoje dva načina da job preživi zatvaranje terminala.

`disown %42` uklanja pokrenuti job iz `jobs` liste terminala, pa mu se neće poslati `SIGHUP`. Job je i dalje spojen na stdin i stdout terminala, pa mu ih treba redirectati jer terminal više neće postojati.

`nohup <cmd> &` pokreće novi background job imun na `SIGHUP`. Zatvara stdin, a stdout i stderr redirecta u `nohup.out` file. Korisno ako tek pokrećeš job.

Orphaned procese odspojene od terminala koji se trajno vrte u pozadini nazivamo *daemon procesi*. Za njihovo upravljanje koristi se naredba `service`, npr. `service nginx restart`. `service --status-all` ispisuje status svih servisa.

## Killing

`kill <pid>` šalje `SIGTERM` (`15`) koji daje priliku procesu da počisti za sobom. Čekaj par sekundi. Ako ne uspije:
`kill -2 <pid>` šalje `SIGINT` (`CRTL+C`). Čekaj par sekundi. Ako ne uspije:
`kill -9 <pid>` šalje `SIGKILL` kojeg proces ne može uhvatiti ni ignorirati. Može izazvati corrupted data, pa ga koristi samo kao last resort.

### Pausing

`kill -TSTP <pid>` pauzira proces (`CTRL+Z`). Za razliku od `-STOP` daje priliku procesu da počisti za sobom.
`kill -CONT <pid>` nastavlja izvršavanje.

## Pinning

Za kritične procese kojima je preformance jako bitan, može pomoći ako ih se *pina* na određeni CPU.

`taskset -c 1 <pid>` postavlja CPU affinity procesa na 1. core.
`taskset -c 1 -- <cmd>` pokreće naredbu na 1. coru.

## ps

`ps` ispisuje procese tvog usera: pid, controlling terminal (tty), CPU time, i naredbu koja ih je pokrenula.

`ps aux` ispisuje svačije procese (`a`), s userom (`u`), uključuje i procese koji nisu pokrenuti iz terminala (`x`).

`ps m` ispuje i threadove svakog procesa.

`ps -eo pid,user,rss` ispisuje samo dane propertije.

`pstree <pid ili user>` prikazuje stablo procesa. `-p` prikazuje pidove.

`pgrep -f <ime>` vraća pidove s danim imenom (ili dijelom imena). Za ispis detalja napravi `ps wup $(pgrep -f <ime>)`.

## top

1. red: uptime, useri, load average
2. red: ukupni broj procesa
3. red: postotak vremena na što CPU troši (us: user ne-_niced_ procesi, sy: system, ni: user _niced_ procesi, id: idle, wa: I/O wait, hi: hardware interrupts, si: software interrupts, st: hypervisor VM-a)
4. red: fizička memorija
5. red: virtualna (swap) memorija

Tablica procesa:
* PR/NI: priority (kernel postavlja) i nice value (user postavlja)
* VIRT/RES/SHR: korištena ukupna virtualna, resident (neswapana) i shared memorija
* S: status. `R`: running, `S`: sleeping, `D`: uninteruptable sleep, `T`: traced or stopped, `Z`: zombie
* %CPU: udio CPU kojeg proces koristi (100% je jedan CPU, `shift+i` mijenja u ukupni udio)
* %MEM: udio fizičke memorije koju proces koristi
* TIME+: ukupno vrijeme procesora koje je proces koristio
* COMMAND: naredba koje je pokrenula proces

`top -p <pid>` prikazuje podatke samo za jedan proces.

# Literatura

* https://peteris.rocks/blog/htop
