# Processes

*Proces* sadrži sve resurse koji su potrebni da se izvrši program: memorijski prostor, kod za izvršenje, handlove na sistemske objekte, environment varijable, i *glavni thread* izvođenja. Proces može stvoriti nove threadove iz bilo kojeh svog threada, a sistemski thread scheduler će se pobrinuti za raspored njihovog izvođenja. Npr, ako je aktivan thread blokiran čekajući IO, scheduler će postaviti idući thread kao aktivan, umjesto da cijeli sistem čeka.

Svaki proces ima odvojen memorijski prostor, dok threadovi unutar istog procesa dijele istu memoriju. Zbog toga i nastaju race conditioni.

## /proc

Svi procesi nalaze se u `/proc` directoriju (zapravo virtualni filesystem čiji deskriptori pokazaju na memoriju, a ne na disk).

Svaki proces živi u subdirecotoriju sa svojim PID-om, npr. `/proc/42`:
* `/proc/42/environ` sadrži env varijable procesa.
* `/proc/42/fd` sadrži linkove na otvorene fileove.
* `/proc/42/cmdline` sadrži argumente s kojima je pokrenut.

## ps

`ps` ispisuje procese tvog usera: pid, controlling terminal (tty), CPU time, i naredbu koja ih je pokrenula.

`ps aux` ispisuje svačije procese (`a`), s userom (`u`), uključuje i procese koji nisu pokrenuti iz terminala (`x`).

`ps -eo pid,user,rss` ispisuje samo dane propertije.

`pstree <pid ili user>` prikazuje stablo procesa. `-p` prikazuje pidove.

`pgrep -f <ime>` vraća pidove s danim imenom (ili dijelom imena). Za ispis detalja napravi `ps wup $(pgrep -f <ime>)`.

## kill

`kill -<sig> <pid>` - šalje signal procesu s danim pidom.
`pkill -<sig> <ime>` - šalje signal procesu s danim imenom (ili dijelom imena)

Za prekidanje procesa:
`kill <pid>` šalje `SIGTERM` (`15`) koji daje priliku procesu da počisti za sobom. Čekaj par sekundi. Ako ne uspije:
`kill -2 <pid>` šalje `SIGINT` (`CRTL+C`). Čekaj par sekundi. Ako ne uspije:
`kill -9 <pid>` šalje `SIGKILL` kojeg proces ne može uhvatiti ni ignorirati. Može izazvati corrupted data, pa ga koristi samo kao last resort.

Za pauziranje procesa:
`kill -TSTP <pid>` pauzira proces (`CTRL+Z`). Za razliku od `-STOP` daje priliku procesu da počisti za sobom.
`kill -CONT <pid>` nastavlja izvršavanje.

## Background Jobs

Shell svaki pokrenuti pipeline (proces ili više povezanih) veže za *job*. Job može biti foreground (zauzima shell dok ne završi, može biti samo jedan) ili background (može ih više biti pokrenuti odjednom).

Svaki job nasljeđuje stdin, stdout i stderr od terminala koji ih je stvorio. Background jobovi se ispisuju na stdout, a pri pokušaju da čitaju sa stdin se pauziraju.

`CTRL + C` šalje `SIGINT` foreground jobu, završava izvođenje.
`CTRL + Z` šalje `SIGTSTP` foreground jobu, pauzira izvođenje, šalje ga u background i vraća kontrolu terminalu.
`<cmd> &` pokreće naredbu u backgroundu. Ispisuje `[1] 4287` job number i process id.

`jobs` ispisuje sve aktivne jobove (pauzirane i background procese) ovog terminala.
`fg` uzima prvi aktivni job i stavlja ga u foreground. `fg %42` ako znaš job number.
`bg` uzima prvi aktivni job i stavlja ga u background (kao da je s `&` pozvan). Korisno u kombinaciji s `CTRL+Z`.
`kill %42` završava izvođenje joba po job numberu.

Kada se terminal zatvara, svim svojim jobovima šalje `SIGHUP` signal da se prekinu izvoditi. Postoje dva načina da job preživi zatvaranje terminala.

`disown %42` uklanja pokrenuti job iz `jobs` liste terminala, pa mu se neće poslati `SIGHUP`. Job je i dalje spojen na stdin i stdout terminala, pa mu ih treba redirectati jer terminal više neće postojati.

`nohup <cmd> &` pokreće novi background job imun na `SIGHUP`. Zatvara stdin, a stdout i stderr redirecta u `nohup.out` file. Korisno ako tek pokrećeš job.

## Services

Dugoživeći jobovi se mogu kontrolirati kroz naredbu `service`, npr. `service nginx restart`.

`service --status-all` ispisuje status svih servisa.

## taskset

Za kritične procese kojima je preformance jako bitan, može pomoći ako ih se *pina* na određeni CPU.

`taskset -c 1 <pid>` postavlja CPU affinity procesa na 1. core.
`taskset -c 1 -- <cmd>` pokreće naredbu na 1. coru.

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

Opcije unutar programa:
`c` - prikaži puni path commanda
`k` - kill process
`d` - promijeni interval updatea
`o` - promijeni ordering
`u` - filtriraj za usera
`w` - saveaj trenutnu konfiguraciju topa

## cronjob

`cronjob` nije interaktivni shell pa ne učitava `.bash_profile` ni `.bashrc`. Možeš ih ručno pozvati pomoću `source /home/user/.bash_profile`.

# Literatura

* http://unix.stackexchange.com/a/148698
