# Bash

## Keyboard controls

`tab` autocomplete.
`CTRL + R` započinje search history. Utipkaj što tražiš, `CTRL + R` cycla kroz rezultate, `enter` izvršava naredbu, a desna strelica je stavlja u command line za editiranje.
`CTRL + U` delete do početka linije, `CTRL + K` delete do kraja linije. `CTRL + W` deleta prethodnu riječ.
`CTRL + L` clear screen.

## Globbing

Bash određene patterne zamijeni listom filenameova u trenutnom directoriju.
* `?` za bilo koji znak,
* `*` za bilo koji string,
* `[abc]` za neki od navedenih znakova. `[a-c]` za range.
* `file{.txt,.doc}` za sve navedene stringove. `file-{1..10}.txt` za rangeove.

## Variables

`VAR=value` assignment, ne smije biti razmaka oko `=`. Varijabla je dostupna samo u shellu.
`VAR=value <cmd>` čini varijablu dostupnom unutar `<cmd>` procesa.
`export VAR` čini varijablu dostupnom u svim *child* procesima pokrenutih iz tog shella.
`printenv` ispisuje sve environment varijable.

`$VAR` expanda se u vrijednost varijable.
`${VAR}s` ako expandaš pored stringa, da označiš koji dio je ime varijable.
`${VAR:2}` vraća substring počevši od 2. znaka. `${VAR:2:3}` vraća substring od 2. znaka duljine 3.
`${VAR#str}` briše `str` s početka stringa.
`${VAR%str}` briše `STR` s kraja, npr. `${filename%.*}` briše ekstenziju filea.
`${VAR/str1/str2}` replace prvi match `str1` sa `str2`. `${VAR//str1/str2}` replacea sve matcheve.

## Subshell

`(cd /etc/log && ls)` otvara novi shell i u njemu izvede naredbe
`$(cd /etc/log && ls)` expanda se u rezultat izvedenih naredbi

## Quoting

Vrste quotanja:
* `\` za escapanje jednog znaka bez evaluacije, npr. `\>`
* `'<cmd>'` literal value,
* `"<cmd>"` literal value, ali interpolira varijable (`$`) i escapanje (`\`).

**UVIJEK** quotaj varijable (`$var`) i command substitution (`$(cmd)`):
* `cmd $var` je zapravo `cmd(glob(split($var)))`
* `cmd "$var"` je zapravo `cmd($var)`

## Options

`<cmd> --` označava kraj opcija. Korisno ako treba proslijediti file koji počinje s `-`, npr. `rm -- -file.txt`

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

## if

`if` provjerava exit code `<cmd>`. To može biti obična naredba, test (`[...]`) ili arithmetic expansion (`((...))`).
```
if <cmd>; then
  ...
else
  ...
fi
```

## for

`for` iterira po `<list>` koji može biti lista varijabli (`"$VAR1", "$VAR2", "$VAR3"`), globbing pattern (`*.txt`), ili range (`{1..10}`).
```
for x in <list>; do
  echo "$x"
done
```

## while

`while` se pokreće dok je exit code `<cmd>` true (`0`). `<cmd>` može biti sve što i u `if`u.
```
while <cmd>; do
  ...
done
```

## Redirection

Naredbe po defaultu primaju podatke sa `stdin`, ispisuju rezultat na `stdout`, a greške na `stderr`. Redirection omogućuje da se bilo koji od tih ulaza i izlaza preusmjeri. Može biti bilo gdje unutar naredbe, ali redoslijed redirectiona je bitan.

`0` je stdin, `1` je stdout, `2` je stderr.

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

Da bi se skripta mogla pozvati s `./script.sh` potrebno je početi *shebang* (hashbang) linijom koja instruira program loader čime interpretirati skriptu.
`#!/usr/bin/env <program>` je najbolji oblik (npr. `#!/usr/bin/env bash` ili `#!/usr/bin/env ruby`).
Također treba fileu dati permission za execute s `chmod +x script.sh`.

Korisne postavke za pozvati na početku skripte:
* `set -e` abort skripte ako neka naredba faila (non-zero exit code).
* `set -o pipefail` ako naredba u pipelinu faila, cijeli pipeline faila.
* `set -u` detektira nedefinirane varijable.
* `set -x` za debugiranje.

Posebne varijable u scripti:
* `$0` ime skripte.
* `$1`, `$2`, `$3` parametri skripte. `"$@"` array svih parametara. `$#` broj parametara.
* `$$` PID skripte.

## User management

`whoami` vraća ime trenutnog usera.
`id` vraća uid i group id trenutnog usera.

`cat /etc/passwd` ispisuje sve usere s pripadajućim uid, groupid, home path i shell path.
`who` ispisuje trenutno ulogirane usere.
`last` ispisuje usere koji su bili ulogirani.

`w` kombinira `who`, `uptime` i `ps`: tko je ulogiran, koliko dugo, i koji proces trenutno vrti.

`adduser <ime>` stvara novog usera, radi sve za tebe. Postoji i `useradd`, ali s njim je teško.
`passwd <ime>` mijenja password usera.
`userdel <ime>` briše usera, ali nakon trebaš napraviti `rm -r /home/<ime>`.

`groupadd <ime>` stvara novu grupu
`groupdel <ime>` briše grupu
`gpasswd -a <ime> <grupa>` dodaje usera u grupu
`gpasswd -d <ime> <grupa>` briše usera iz grupe

## su i sudo

Za izvršavanje naredbi kao *root*, koriste se `su` ili `sudo`. Osnovna razlika je:
* `su` zahtjeva *root* password.
* `sudo` zahtjeva *tvoj* password, te koristi `/etc/sudoers` da odluči što smiješ raditi. Ovo je bolje jer nitko ne mora znati *root* password.

`sudo <cmd>` izvršava naredbu kao root.

`su` (`sudo -s`) otvara non-login shell. Trenutni directory i env varijable ostaju iste.

`su -` (`sudo -i`) otvara login shell. Premješta se u rootov home directory i pokreće `~/.bash_profile` (kao da se root upravo ulogirao). Bolji način, jer osigurava da će sve raditi kako si je root postavio.

`su <user>` i `sudo -u <user>` koristi ako želiš izvršavati kao `<user>`, a ne kao root.

## System

`uname` ispisuje podatke o OS-u. `-r` kernel release. `-a` za sve podatke.
`hostname` ispisuje sistemsko ime u mreži.
`date` ispisuje trenutno vrijeme.
`uptime` ispisuje koliko dugo već system radi, te load zadnjih 1, 5 i 15 min.

## Stats

`history` ispisuje sve prethodnih naredbi.
`last reboot` ispisuje system rebootove.

`top` ispisuje procese koji najviše troše CPU.
`lsof` ispisuje sve otvorene fileova i procese koji su ih otvorili. Pomoću `-i TCP:3000` možeš vidjeti tko je sve pokrenut na portu `3000`.
`netstat -lnp` ispisuje procese koje imaju otvorene sockete.

`free -m` ispisuje slobodnu radnu memorija u Mb.

`watch <command>` poziva naredbu svakih 2 sekunde (npr. `watch df -h`).
`-n 5` za svakih 5 sekundi. `-d` za highlight promjena.

## Literatura

* Pregled: https://github.com/jlevy/the-art-of-command-line
* Česte greške: http://mywiki.wooledge.org/BashPitfalls
