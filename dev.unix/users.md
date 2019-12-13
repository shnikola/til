# Users

Sistem koristi user id-ove (`uid`) da upravlja korisnicima, a username je tu da olakša korištenje. Grupe se koriste za upravljanje permissionima, i svaka ima svoj `gid`.

`whoami` vraća ime trenutnog usera.
`id` vraća uid i group id trenutnog usera.

Svaki korisnik ima svoj folder koji se inače nalazi u `/home/<username>`.

## User management

`adduser <ime>` stvara novog usera, radi sve za tebe. Postoji i `useradd`, ali s njim je teško.
`passwd <ime>` mijenja password usera.
`userdel <ime>` briše usera, ali nakon trebaš napraviti `rm -r /home/<ime>`.

`groupadd <ime>` stvara novu grupu
`groupdel <ime>` briše grupu
`gpasswd -a <ime> <grupa>` dodaje usera u grupu
`gpasswd -d <ime> <grupa>` briše usera iz grupe

`usermod -aG sudo <ime>` dodaje usera u sudoers grupu.

## Logins

`who` ispisuje trenutno ulogirane usere.
`last` ispisuje usere koji su bili ulogirani.

`w` kombinira `who`, `uptime` i `ps`: tko je ulogiran, koliko dugo, i koji proces trenutno vrti.

## su i sudo

Root je najmoćniji user u sistemu, pa ga želiš koristiti što rjeđe kako bi izbjegao slučajno brisanje kritičnih sistemskih fileova.

Za izvršavanje naredbi kao *root*, koriste se `su` ili `sudo`. Osnovna razlika je:
* `su` zahtjeva *root* password.
* `sudo` zahtjeva *tvoj* password, te koristi `/etc/sudoers` da odluči što smiješ raditi. Ovo je bolje jer nitko ne mora znati *root* password.

`sudo <cmd>` izvršava naredbu kao root.

`su` (`sudo -s`) otvara non-login shell. Trenutni directory i env varijable ostaju iste.

`su -` (`sudo -i`) otvara login shell. Premješta se u rootov home directory i pokreće `~/.bash_profile` (kao da se root upravo ulogirao). Bolji način, jer osigurava da će sve raditi kako si je root postavio.

`su <user>` i `sudo -u <user>` koristi ako želiš izvršavati kao `<user>`, a ne kao root.

## Internals

`/etc/passwd` sadrži listu svih usera u sljedećem formatu: `username:password_flag:uid:gid:bilješke:home_path:shell_path`.
Password flag je prazan ako user nema password, `x` ako ima, `*` ako user nema login access.

`/etc/shadow` sadrži enkriptirane korisničke passworde i podatke o tome kada i ako ističu.

`/etc/group` sadrži listu svih grupa i usera koji im pripadaju.
