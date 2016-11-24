# Bash

## Keyboard
  `tab` autocomplete
  `CTRL + R` započinje search history. Utipkaj što tražiš, `CTRL + R` cycla kroz rezultate, `enter` izvršava naredbu, a desna strelica je stavlja u command line za editiranje.
  `CTRL + U` delete do početka linije, `CTRL + K` delete do kraja linije. `CTRL + W` deleta prethodnu riječ,
  `CTRL + L` clear screen


## Globbing
Patterni koji se zamjene listom filenameova u trenutnom directoriju.
  `?` za bilo koji znak,
  `*` za bilo koji string,
  `[abc]` za neki od navedenih znakova. `[a-c]` za range.
  `file{.txt,.doc}` za sve navedene stringove. `file-{1..10}.txt` za rangeove.


## Varijable
  `VAR=value` assignment, ne smije biti razmaka oko `=`. Varijabla je dostupna samo u shellu.
  `VAR=value <cmd>` čini varijablu dostupnom unutar `<cmd>` procesa.
  `export VAR` čini varijablu dostupnom u svim *child* procesima pokrenutih iz tog shella.

  `$VAR` expanda se u vrijednost varijable.
  `${VAR}s` ako expandaš pored stringa, da označiš koji dio je ime varijable.
  `${VAR:2}` vraća substring počevši od 2. znaka. `${VAR:2:3}` vraća substring od 2. znaka duljine 3.
  `${VAR#str}` briše `str` s početka stringa. `${VAR%str}` briše `STR` s kraja.
  `${VAR/str1/str2}` replace prvi match `str1` sa `str2`. `${VAR//str1/str2}` replacea sve matcheve.


## Subshell
  `(cd /etc/log && ls)` otvara novi shell i u njemu izvede naredbe
  `$(cd /etc/log && ls)` expanda se u rezultat izvedenih naredbi


## Quoting
Vrste quotanja:
  `\` za escapanje jednog znaka bez evaluacije, npr. `\>`
  `'<cmd>'` literal value,
  `"<cmd>"` literal value, ali interpolira varijable (`$`) i escapanje (`\`).

*UVIJEK*, ali baš *UVIJEK* quotaj varijable (`$var`) i command substitution (`$(cmd)`):
  `cmd $var` je zapravo `cmd(glob(split($var)))`
  `cmd "$var"` je zapravo `cmd($var)`


## Arithmetic
`let <expr>` evaluira aritmetičku (integer) operaciju nad varijablama, npr. `let "A = B + 3"`. Pazi na navodnike!
`(( <expr> ))` radi istu stvar, npr. `(( VAR += 3 ))`
`$(( <expr> ))` se expanda u rezultat operacije, npr. `echo $(( VAR * 2 ))`


## Testing
`[ <test> ]` vraća true (`0`) ili false (`1`). `[` je zapravo naredba i prima argumente koji završavaju s `]`. Zato je nužno imati **razmake** prije i nakon zagrada!
  * `[ -e /etc/passwd ]` true ako file postoji. `-f` ako je file, `-d` ako je directory.
  * `[ -n "$VAR" ]` true ako string nije prazan, `-z` ako je prazan.
  * `[ "$VAR" = nikola ]` za usporedbu stringova, `!=` ako su različiti.
  * `[ "$VAR" -eq 1 ]` za usporedbu integera, `-ne` ako su različiti, `-lt` i `-le` za manje i `-gt` i `-ge` za veće.
  * `[ ! -e file ]` za negiranje.
  * `[ <test> ] && [ <test> ]` za kombiniranje.

`[[ <test> ]]` je moderniji oblik testa koji nije naredba, pa dopušta ne-escapane znakove poput `<` i `()`. Korisno za složenije uvjete.
 * `[[ ( <t1> || <t2> ) && <t3> ]]` dopušta kombiniranje unutar testa.
 * `[[ "$VAR" ~= n.* ]]` radi regex matching


## if, for, while
**if** provjerava exit code `<cmd>`. To može biti obična naredba, test (`[...]`) ili arithmetic expansion (`((...))`). Forma:
```
if <cmd>; then
  ...
else
  ...
fi
```

**for** iterira po `<list>` koji može biti lista varijabli (`"$VAR1", "$VAR2", "$VAR3"`), globbing pattern (`*.txt`), ili range (`{1..10}`).
```
for x in <list>; do
  ...
done
```

**while** se pokreće dok je exit code `<cmd>` true (`0`). `<cmd>` može biti sve što i u `if`u.
```
while <cmd>; do
  ...
done
```


## Options
`<cmd> --` označava kraj opcija. Korisno ako treba proslijediti file koji počinje s `-`, npr. `rm -- -file.txt`


## Redirection
Može biti bilo gdje unutar naredbe, ali redoslijed redirectiona je bitan. `0`: stdin, `1`: stdout, `2`: stderr.
  `echo foo >file` zapisuje *stdout* u file (`>` je isto kao i `1>`)
  `echo foo >>file` appenda *stdout* u file
  `cmd <file` čita *stdin*  iz filea (`<` je isto kao i `0<`)
  `cmd 2>&1` šalje *stderr* na gdjegod je *stdout* usmjeren. `&` duplicira file descriptor.
  `cmd >x 2>&1 >y` *stdout* ide na x, *stderr* ide gdjegod je *stdout* (znači x), i onda *stdout* ide na y (*stderr* ostaje x)  
  `cmd file >file` **neće raditi**. `>` otvara file za pisanje i obriše ga prije nego ga naredba počne čitati. Koristi temp file ili inline opciju naredbe ako postoji.


## Pipeline
  `<cmd> | <cmd>` stdout prve naredbe šalje se na stdin iduće. Svaka naredba pokreće se kao zaseban proces. Exit status se uzima od zadnje naredbe.
  `<cmd> && <cmd>` druga naredba pokreće se samo ako je prva uspješno završila (exit status = `0`)
  `<cmd> || <cmd>` druga naredba pokreće se samo ako prva nije uspješno završila (exit status != `0`)


## Background Jobs
Shell uz svaki pokrenuti pipeline (proces ili više povezanih) veže uz abstrakciju *job*. Job može biti foreground (zauzima shell dok ne završi, može biti samo jedan) ili background (može ih više biti pokrenuti odjednom)
  `CTRL + C` šalje *SIGINT* foreground jobu, završava izvođenje.
  `CTRL + Z` šalje *SIGTSTP* foreground jobu, pauzira izvođenje, šalje ga u background i vraća kontrolu terminalu.
  `<cmd> &` pokreće naredbu u backgroundu. Ispisuje `[1] 4287` job number i process id.
  `jobs` ispisuje sve aktivne jobove (pauzirane i background procese) ovog terminala.
  `fg` uzima prvi aktivni job i stavlja ga u foreground. `fg %1` ako znaš job number.
  `bg` uzima prvi aktivni job i stavlja ga u background (kao da je s `&` pozvan). Korisno u kombinaciji s `CTRL+Z`.
  `kill %1` završava izvođenje joba.

Kada se terminal zatvara, svim svojim jobovima šalje *SIGHUP* signal da se prekinu izvoditi. Da bi job ostao živ nakon:
  `nohup <cmd> &` ako tek pokrećeš job. Pokreće background job imun na *SIGHUP*. Output ide u `nohup.out`.
  `disown %1` ako je job već pokrenut. Uklanja job iz job managementa terminala. Output treba ručno redirectati.
(http://unix.stackexchange.com/a/148698)


## Path
`PATH` varijabla sastoji se od liste directorija (odvojenih s `:`) u kojima shell traži naredbu.
`PATH=$PATH:/opt/bin` dodaje novi directory u `PATH`.
Shell cachira lokacije executablea. Ako se nova naredba ne može pronaći, pozovi `hash -r` da reloadaš hash table s executablima. Ovo se automatski dogodi kad se promijeni `PATH`


## Configuration
`.bash_profile` se izvodi svaki put kad je pokrenut *login shell* (kada se ulogiraš u terminal prvi put, ili preko `ssh`). Tu stavi postavke env varijabli i pozdravne poruke.
`.bashrc` se izvodi svaki put kad je pokrenut *non-login shell*. (kada otvoriš novi prozor u terminalu. OSX je iznimka, i on svaki put iznova otvara login shell). Tu stavi aliase, postavke shella i custom funkcije.
Najčešće želiš `.bash_profile` da interno učita `.bashrc` pomoću `[[ -r ~/.bashrc ]] && . ~/.bashrc`.


## Alias i funkcije
Aliase i funkcije definiraj u `.bashrc`. Koristi ih za
  * naredbe koje ćeš pozivati samo iz shella (drugi programi ih ne koriste)
  * naredbe koje mijenjaju environment
  * naredbe koje ćeš koristiti često (držat će se u memoriji)

`alias ll="ls -hal"` koristi za dodavanje opcija postojećim programima.
`ime_funkcije() { ... }` koristi kad trebaš složenije ponašanje


## Scripting
Skripte definiraj u `~/bin` za svog usera, u `/usr/local/bin` za sve lokalne usere. Koristi ih za:
  * naredbe koje koriste i drugi programi osim shella
  * naredbe koje ne koristiš često pa nema smisla da zauzimaju memoriju

Skripta se može pozvati na dva načina:
  * `sh script.sh` (ili `./script.sh`) pokreće u novom shellu i varijable postavljene u skripti se gube na kraju izvođenja.
  * `source script.sh` (ili `. script.sh`) pokreće skriptu u trenutnom shellu i procesu, varijable ostaju.

Da bi se skripta mogla pozvati s `./script.sh` potrebno je:
  * početi *shebang* (hashbang) linijom koja instruira program loader čime interpretirati skriptu.
    `#!/usr/bin/env <program>` je najbolji oblik (npr. `#!/usr/bin/env bash` ili `#!/usr/bin/env ruby`).
  * dati permission za execute s `chmod +x script.sh`

Korisne postavke za pozvati na početku skripte:
  * `set -e` abort skripte ako neka naredba faila (non-zero exit code).
  * `set -o pipefail` ako naredba u pipelinu faila, cijeli pipeline faila.
  * `set -u` detektira nedefinirane varijable.
  * `set -x` za debugiranje.

Posebne varijable u scripti:
  * `$0` ime skripte.
  * `$1`, `$2`, `$3` parametri skripte. `"$@"` array svih parametara. `$#` broj parametara.
  * `$$` PID skripte.


## Device management
Unix omogućuje jednaki način pristupa svim vrstama uređaja (*devices*). Svi uređaji koje OS prepozna imaju svoj file (*device node*) u `/dev` directoriju. Osim imena, device node ima major number (koji driver je zadužen za njega) i minor number (njegov id za taj driver). `ls -l` za više detalja. Device node može predstavljati:
  *block device* (dozvoljava random access, kao disk)
  *character device* (pristupa mu se slijedno kao streamu, npr. `/dev/fd/0` tj. `/dev/stdout`)
  *pseudo device* (npr. `/dev/null` ili `/dev/random`)

Fizički device može se podijeliti na više logičkih cijelina (*partitions*). Svaka particija će imati svoj device node u `/dev` directoriju. Particije olakšavaju backup i povećavaju sigurnost. Čak i ako jedna ode kvragu (npr. ostane bez slobodnog mjesta), druge će nastaviti raditi.

Da bi se moglo pristupiti fileovima devicea ili particije, potrebno je napraviti *mounting*. Mounting povezuje filesystem na block deviceu s nekim directorijem. Kada se eksterni disk ili DVD spoji na računalo, mounting se dogodi automatski u directory `/mnt` (`/Volumes` za OSX). Automatsko mountanje pri bootu može se podesiti u `/etc/fstab` fileu.

`fdisk <device>` kreira novu particiju.
`mkfs -t <type> <device>` stvara filesystem na danom deviceu/particiji. Pritom briše sve podatke s njega.
`mount <device> <directory>` mounta device u dani directory. Ako nije directory nije zadan, koristit će `/etc/fstab` file.
`fsck <device>` provjerava ima li grešaka na deviceu.

`df -h` ispisuje sve mountane uređaje i slobodno mjesto na njima. Dobro za provjeru mjesta na disku.
`findmnt` ispisuje sve mountane filesysteme i njihove opcije.
`lsblk` ispisuje sve block deviceove.
`lsbusb`/`lspci` ispisuje sve USB/PCI busove.


## Swap Memory
Svaki sustav bi trebao imati definiran swap space na disku koji može koristiti kada mu zafali RAMa.
Swap se može držati u swap particiji (`fdisk <device>`) ili u swap fileu (`dd if=/dev/zero of=/extraswap bs=1M count=512`)
`mkswap <device or file>` inicijalizira swap space.
`swapon <device or file>` aktivira swap space i počinje ga koristiti. Za automatsko aktiviranje pri bootu dodaj u `/etc/fstab`.

`cat /proc/swaps` (bez argumenta) ispisuje trenutno aktivne swapove.


## Users
`whoami` - ime mog usera
`id` - uid i group id mog usera

`cat /etc/passwd` - lista svih usera s pripadajućim uid, groupid, home path i shell path
`who` - lista trenutno ulogiranih usera
`last` - lista usera koji su bili ulogirani. `last reboot` - lista system reboota.
`w` - tko je ulogiran, koliko dugo, i koji proces trenutno vrti (kombinira `who`, `uptime` i `ps`)

`useradd <ime>` - stvori novog usera, teži način.
`adduser <ime>` - stvori novog usera, radi sve za tebe.
`passwd <ime>` - mijenja password usera.
`userdel <ime>` - obriši usera. Nakon toga trebaš napraviti `rm -r /home/<ime>`.

`groupadd <ime>` - stvori novu grupu
`groupdel <ime>` - obriši grupu
`gpasswd -a <ime> <grupa>` - dodaj usera u grupu
`gpasswd -d <ime> <grupa>` - obriši usera iz grupe


## File management
`pwd` ispisuje trenutni path.
`cd` mijenja trenutni path. `cd ..` za level up. `cd -` za prethodni directory.
`ls -hal` ispisuje sve fileove u trenutnom direktoriju.
`du -sh *` ispisuje koliko fileovi u trenutnom direktoriju zauzimaju na disku. `du -ch` za total na kraju.

`mkdir <dir>` stvara novi directory. `-p` stvara i sve međudirektorije u danom pathu ako ne postoje.
`touch <file>` ako ne postoji, stvara prazan file. Ako postoji, updatea access i modified timestamp.

`cp <src> <dest>` radi kopiju filea i sprema ga kao `<dest>`.
  * ako je `<dest>` directory (s *trailing slashom*), kopira file u njega.
  * `-a` kopira rekurzivno i sačuva ownership, permissione, linkove itd. Dobro za backup.
`mv <src> <dest>` radi isto kao i `cp`, samo briše `<src>` nakon.
`rm <files>` briše fileove.
  * `-r` briše directory rekurzivno.
  * `-f` briše bez prompta.

`file <file>` koristeći *magic numbers*, ispisuje tip podataka u fileu. `--mime-type` za MIME.


## Permissions
Svaki file ima usera i grupu kojim pripada, te permissione (read, write, execute) za usera, grupu i sve ostale.
`chown <user>:<group> <file>` postavlja usera/grupu kao ownera. `-R` za rekurzivno u directoriju.
`chmod <perm> <file>` postavlja permissione za usera, grupu i sve ostale. `-R` za rekurzivno u directoriju.
`<perm>` može biti:
  * numeric, npr. `664`. `4`: read, `2`: write, `1`: execute
  * symbolic, `u+x`: dodaj useru execute permission. `a-w`: oduzmi svima write permission.


## su i sudo
Za izvršavanje naredbi kao *root*, koriste se `su` ili `sudo`. Osnovna razlika je:
  `su` zahtjeva *root* password.
  `sudo` zahtjeva *tvoj* password, te koristi `/etc/sudoers` da odluči što smiješ raditi. Ovo je bolje jer nitko ne mora znati *root* password.

`sudo <cmd>` izvršava naredbu kao root.
`su` (`sudo -s`) otvara non-login shell. Trenutni directory i env varijable ostaju iste.
`su -` (`sudo -i`) otvara login shell. Premješta se u rootov home directory i pokreće `~/.bash_profile` (kao da se root upravo ulogirao). Bolji način, jer osigurava da će sve raditi kako si je root postavio.

`su <user>` i `sudo -u <user>` da se koristi `<user>` umjesto roota.


## Linkovi
Svaki directory je samo tablica fileova, a svaki file ima samo *filename* i *inode number*. *Inode number* pokazuje na *inode* koji sadrži data dio filea: owner, permissioni, timestampovi, veličina, gdje se podatci nalaze, itd. + `mv` je upravo zato brz ma koliko file bio velik: ne prebacuju se podatci, već samo redak u tablici s filenameom i inode brojem.
Svaki directory pri kreiranju dobija dva retka u tablici: `.` koji pokazuje na inode directorija, te `..` koji pokazuje na inode parenta. Ta dva "retka" su zapravo hard linkovi.

`ln <src> <dest>` stvara *hard link* na inode koji ima različiti filename, ali isti inode number.
  Svi hard linkovi su ravnopravni: ako obrišemo jednog, drugi će i dalje raditi (inode se ne briše dok ima linkova na njega).
  Ako promijenimo permissione jednom, promjenit će se i drugom.
  Ograničenje: mora biti na istom file systemu.

`ln -s <src> <dest>` stvara *soft link*, koji je posebna vrsta filea čiji data dio sadrži path do drugog filea.
  OS se pobrine da se pri otvaranju soft linka otvori file na kojeg pokazuje.
  Ovisi o izvoru: ako se `<src>` obriše, soft link više neće raditi.
  Može biti na različitom file systemu.
  Zauzima malo više mjesta i malo je sporiji od hard linka (treba otvarati dva filea svaki put).

Inode podatci mogu se prikazati s `ls -i` (za directory) ili `df -i` (za cijeli sistem).


## Search
`find <start_dir> <filter>` rekurzivno prolazi kroz sve fileove u `<start_dir>` i ispisuje one koji prolaze `<filter>`
  * `-name '*.txt'` traži fileove danog imena. `-iname` traži case-insensitive.
  * `-type f` traži fileove. `-type d` traži directorije.
  * `-not -name '*.txt'` za sve koji ne ispunjavaju uvjet.
  * `-exec <cmd> {} \;` poziva `<cmd>` za *svaki* pronađeni file, `{}` substituira imenom svakog filea.
  * `-exec <cmd> {} +` poziva `<cmd>` za *sve* pronađene fileove, `{}` substituira imenom svih fileova.

`locate <filename>` je brži od `find`, ali koristi svoju bazu koja se updatea svakih 24h, pa ne pronalazi tek dodane fileove.
`which <cmd>` vraća path gdje je naredba definirana.


## System
`uname` - podatci o OS-u. `-r` kernel release. `-a` za sve podatke.
`hostname` - sistemsko ime u mreži
`date` - trenutno vrijeme.
`uptime` - koliko dugo već system radi, te load zadnjih 1min, 5min i 15min


## Stats
`history` - lista svih prethodnih naredbi.

`top` - lista procesa koji najviše troše CPU.
`lsof` - lista otvorenih fileova s procesima koji su ih otvorili
`netstat -lnp` - lista procese koje imaju otvorene sockete.
`free` - slobodna memorija. `-m` u megabytima.

`watch <command>` - poziva naredbu svakih 2 sekunde (npr. `watch df -h`).
  * `-n 5` za svakih 5 sekundi.
  * `-d` za highlight promjena.



## Literatura
* Pregled: https://github.com/jlevy/the-art-of-command-line
* Česte greške: http://mywiki.wooledge.org/BashPitfalls
