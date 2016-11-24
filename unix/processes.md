# Processes

*Proces* sadrži sve resurse koji su potrebni da se izvrši program: memorijski prostor, kod za izvršenje, handlove na sistemske objekte, environment varijable, i *glavni thread* izvođenja. Proces može stvoriti nove threadove iz bilo kojeh svog threada, a sistemski thread scheduler će se pobrinuti za raspored njihovog izvođenja. Npr, ako je aktivan thread blokiran čekajući IO, scheduler će postaviti idući thread kao aktivan, umjesto da cijeli sistem čeka.

Svaki proces ima odvojen memorijski prostor, dok threadovi unutar istog procesa dijele istu memoriju. Zbog toga i nastaju race conditioni.


## /proc
Svi procesi nalaze se u `/proc` directoriju (zapravo virtualni filesystem čiji deskroptiri pokazaju na memoriju, a ne na disk).
* Svaki proces živi u subdirecotoriju sa svojim PID-om, npr. `/proc/42`.
* u `/proc/42/environ` nalaze se env varijable procesa.
* u `/proc/42/fd` nalaze se linkovi na otvorene fileove.
* u `/proc/42/cmdline` nalaze se argumenti s kojima je pokrenut.


## ps i kill
`ps` ispisuje procese tvog usera: pid, controlling terminal (tty), CPU time, i naredbu koja ih je pokrenula.
  * `ps aux` ispisuje svačije procese (`a`), s userom (`u`), uključuje i procese koji nisu pokrenuti iz terminala (`x`).
  * `ps -eo <fields>` ispisuje samo `<fieldovs>`, npr. `-eo pid,user,rss`.

`pstree <pid ili user>` prikazuje stablo procesa. `-p` prikazuje pidove.

`pgrep -f <ime>` vraća pidove s danim imenom (ili dijelom imena). Za ispis detalja napravi `ps wup $(pgrep -f <ime>)`.  

`kill -<sig> <pid>` - šalje signal procesu s danim pidom.
`pkill -<sig> <ime>` - šalje signal procesu s danim imenom (ili dijelom imena)

Za prekidanje procesa:
`kill <pid>` šalje `SIGTERM` (`15`) koji daje priliku procesu da počisti za sobom. Čekaj par sekundi. Ako ne uspije:
`kill -2 <pid>` šalje `SIGINT` (`CRTL+C`). Čekaj par sekundi. Ako ne uspije:
`kill -9 <pid>` šalje `SIGKILL` kojeg proces ne može uhvatiti ni ignorirati. Može izazvati corrupted data, pa ga koristi samo kao last resort.

Za pauziranje procesa:
`kill -TSTP <pid>` pauzira proces (`CTRL+Z`). Za razliku od `-STOP` daje priliku procesu da počisti za sobom.
`kill -CONT <pid>` nastavlja izvršavanje.


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
cronjob nije interaktivni shell pa ne učitava `.bash_profile` ni `.bashrc`. Možeš ih ručno pozvati s:
`source /home/user/.bash_profile`
