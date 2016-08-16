# Unix

## Bash
*Quoting:*
  `\` za escapanje jednog znaka bez evaluacije, npr. `\>`
  `'<cmd>'` literal value,
  `"<cmd>"` literal value, ali interpolira varijable (`$`) i escapanje (`\`).
  ``<cmd>`` (ili `$(<cmd>)`) command substitution, eval rezultata `<cmd>`.

*Globbing:*
Patterni koji se zamjene listom filenameova u trenutnom directoriju.
  `?` za bilo koji znak,
  `*` za bilo koji string,
  `[abc]` za neki od navedenih znakova. `[a-c]` za range.

*Redirection:*
Može biti bilo gdje unutar naredbe, ali redoslijed redirectiona je bitan. `0`: stdin, `1`: stdout, `2`: stderr.
  `echo foo >file` zapisuje *stdout* u file (`>` je isto kao i `1>`)
  `echo foo >>file` appenda *stdout* u file
  `cmd <file` čita *stdin*  iz filea (`<` je isto kao i `0<`)
  `cmd 2>&1` šalje *stderr* na gdjegod je *stdout* usmjeren. `&` duplicira file descriptor.
  `cmd >x 2>&1 >y` *stdout* ide na x, *stderr* ide gdjegod je *stdout* (znači x), i onda *stdout* ide na y (*stderr* ostaje x)  
  `s/foo/bar/' file >file` **neće raditi**. `>` otvara file za pisanje i truncatea ga prije nego ga `s` počne čitati.

*Pipeline:*
  `<cmd> | <cmd>` stdout prve naredbe šalje se na stdin iduće. Svaka naredba pokreće se kao zaseban proces. Exit status se uzima od zadnje naredbe.
  `<cmd> && <cmd>` druga naredba pokreće se samo ako je prva uspješno završila (exit status = 0)
  `<cmd> || <cmd>` druga naredba pokreće se samo ako prva nije uspješno završila (exit status != 0)

*Background Jobs:*
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


## Programs

*System:*
`uname` - podatci o OS-u. `-r` kernel release. `-a` za sve podatke.
`hostname` - sistemski host name (ime u mreži)
`date` - trenutno vrijeme.
`uptime` - koliko dugo već system radi, te load zadnjih 1min, 5min i 15min

*Stats:*
`top` - lista procesa koji najviše troše CPU.
`lsof` - lista otvorenih fileova s procesima koji su ih otvorili
`free` - slobodna memorija. `-m` u megabytima.
`watch <command>` - poziva naredbu svakih 2 sekunde (npr. `watch df -h`). `-n` za interval. `-d` za highlight promjena.

*Users:*
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

*Files:*
`pwd` - ispiši trenutni path.
`cd` - promijeni trenutni path. `cd ..` za level up. `cd -` za prošli path.
`ls -hal` - ispiši sve fileove u trenutnom direktoriju.

`mkdir <dir>` - stvori novi directory. `-p` stvara i sve međudirektorije u danom pathu ako ne postoje.
`touch <file>` - updatea access i modified timestamp na fileu. Ako file ne postoji, stvara prazni.
`cp <src> <dest>` - ako je `<dest>` directory (s trailing slashom), kopira file u njega. Ako je samo ime, kopira u trenutni.
`mv <src> <dest>` - ako je `<dest>` directory (s trailing slashom), premjesti file u njega. Ako je samo ime, radi rename.
`rm <files>` - briše fileove. `-r` briše directory rekurzivno. `-f` briše bez prompta.
`ln -s <src> <dest>` - stvara symlink.

`cat` - ispisuje sadržaj filea.
`head`, `tail` - ispisuje prvih/zadnjih 10 linija. `-n` broj linija. `-F` ispisuje linije kako se dodaju.
`less` - scrollanje kroz file. `+F` ispisuje linije kako se dodaju.

`grep`
`locate`
`find`

`chmod`
`chown`



**Processes:**
`ps`
`pgrep`
`pmap`
`kill`
`killall`
`pkill`

**Disk:**
`df`
`fdisk`
`mkfs`
`du`
`mount`
`findmnt`
`lsblk`


`hash -r` Očisti hash table s executablima (automatski se napravi kad se promijeni $PATH)
`su` i `sudo` TODO
`ssh`, ssh-agent, ssh-add
`scp`
`rsync`
ifconfig
