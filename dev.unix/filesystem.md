# Filesystem

## File management

`pwd` ispisuje trenutni path.
`cd` mijenja trenutni path. `cd ..` za level up. `cd -` za prethodni directory.
`ls -hal` ispisuje sve fileove u trenutnom direktoriju.
`du -sh *` ispisuje koliko fileovi u trenutnom direktoriju zauzimaju na disku. `du -ch` za total na kraju.

`mkdir <dir>` stvara novi directory. `-p` stvara i sve međudirektorije u danom pathu ako ne postoje.
`touch <file>` ako ne postoji, stvara prazan file. Ako postoji, updatea access i modified timestamp.
`mktemp` stvara privremeni file (`-d` za direktorij) s unikatnim imenom.

`cp <src> <dest>` radi kopiju filea i sprema ga kao `<dest>`.
  * ako je `<dest>` directory (s *trailing slashom*), kopira file u njega.
  * `-a` kopira rekurzivno i sačuva ownership, permissione, linkove itd. Dobro za backup.
`mv <src> <dest>` radi isto kao i `cp`, samo briše `<src>` nakon.
`rm <files>` briše fileove.
  * `-r` briše directory rekurzivno.
  * `-f` briše bez prompta.

`file <file>` koristeći *magic numbers*, ispisuje tip podataka u fileu. `--mime-type` za MIME.

`tar -zcvf dest.tar.gz src` za zipanje fileova.

## Search

`find <start_dir> <filter>` rekurzivno prolazi kroz sve fileove u `<start_dir>` i ispisuje one koji prolaze `<filter>`
* `-name '*.txt'` traži fileove danog imena. `-iname` traži case-insensitive.
* `-type f` traži fileove. `-type d` traži directorije.
* `-not -name '*.txt'` za sve koji ne ispunjavaju uvjet.
* `-exec <cmd> {} \;` poziva `<cmd>` za *svaki* pronađeni file, `{}` substituira imenom svakog filea.
* `-exec <cmd> {} +` poziva `<cmd>` za *batch* pronađenih fileova, `{}` substituira imenom svih fileova.

`locate <filename>` je brži od `find`, ali koristi svoju bazu koja se updatea svakih 24h, pa ne pronalazi tek dodane fileove.
`which <cmd>` vraća path gdje je naredba definirana.

## Permissions

Svaki file ima usera i grupu kojim pripada, te permissione (read, write, execute) za usera, grupu i sve ostale.

`chown <user>:<group> <file>` postavlja usera/grupu kao ownera. `-R` za rekurzivno u directoriju.

`chmod <permission> <file>` postavlja permissione za usera, grupu i sve ostale. `-R` za rekurzivno u directoriju. `<permission>` može biti:
* numeric, npr. `664`. `4`: read, `2`: write, `1`: execute
* symbolic, `u+x`: dodaj useru execute permission. `a-w`: oduzmi svima write permission.

## Inodes

Svaki directory je zapravo tablica fileova, a svaki file ima samo *filename* i *inode number*. *Inode number* pokazuje na *inode* koji sadrži data dio filea: owner, permissioni, timestampovi, veličina, gdje se podatci nalaze, itd.

`mv` je upravo zato brz ma koliko file bio velik: ne prebacuju se podatci, već samo redak u tablici s filenameom i inode brojem.

Svaki directory pri kreiranju dobija dva retka u tablici: `.` koji pokazuje na inode directorija, te `..` koji pokazuje na inode parenta. Ta dva "retka" su zapravo hard linkovi.

Sustav može ostati bez fizičkog mjesta, ali i bez inode mjesta (ako imaš jako puno malih ili praznih fileova). Inode podatci mogu se prikazati s `ls -i` za directory ili `df -i` za cijeli sistem.

## Linkovi

`ln <src> <dest>` stvara *hard link* na inode koji ima različiti filename, ali isti inode number. Mora biti na istom file systemu.

Svi hard linkovi su ravnopravni. Ako promijenimo permissione jednom, promjenit će se i drugom. Ako obrišemo jednog, drugi će i dalje raditi. Inode će se obrisati tek kad nitko više ne linka na njega.

`ln -s <src> <dest>` stvara *soft link*, koji je posebna vrsta filea čiji data dio sadrži path do drugog filea. OS se pobrine da se pri otvaranju soft linka otvori file na kojeg pokazuje. Može biti na različitom file systemu.

Soft linkovi ovise o izvoru: ako se `<src>` obriše, soft link više neće raditi.
Zauzima malo više mjesta i malo je sporiji od hard linka (treba otvarati dva filea svaki put).

## Page Cache

Kada OS prvi put čita s diska ili piše na disk, koristi slobodni RAM da cachira taj sadržaj. Ako se file opet pokuša čitati, iskoristit će cache umjesto da čita sa sporijeg diska.

`free -m` u "cache" stupcu ispisuje koliko se bytova memorije koristi za cache.

