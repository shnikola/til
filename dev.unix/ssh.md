# SSH

`ssh <user>@<host>` otvara shell na remote stroju. Ali može i još mnogo drugih stvari!

*Local port forwarding* (`-L <local>:<remote>`) šalje *lokalni* promet SSH tunelom do *remote* hosta. Korisno za spajanje na serverov DB.
Primjer: `-L 9000:localhost:5432` promet lokalnog porta `9000` šalje na SSH server koji ga prosljeđuje svom localhostu na port `5432` (gdje se nalazi DB).

*Remote port forwarding* (`-R <remote>:<local>`) šalje promet s *remote* hosta SSH tunelom na *lokalni* stroj. Korisno za spajanje izvana na tvoj lokalni stroj dok developaš.
Primjer: `-R 9000:localhost:3000` promet `<host>:9000` prosljeđuje se tvom lokalnom računalu na port `3000`.

*Dynamic port forwarding* (`-D <port>`) koristi lokalni port kao SOCKS proxy, pa *sav* promet možeš tunelirati preko SSH.
Primjer: `ssh -D 5555 server`, i u browseru ili curlu možeš podesiti proxy na `localhost:5555`.

*Agent Forwarding* (`-A`) prosljeđuje tvoje lokalne private keyeve SSH serveru, pa možeš pullati s Githuba bez da dodaješ deployment keyeve. Moraš turbo vjerovati serveru da bi ovo koristio.

*Command execution* (`ssh <user>@<host> <cmd>`) izvodi naredbu na remote stroju, a rezultat možeš normalni pipeati lokalno. U `.ssh/authorized_keys` mogu se dodati naredbe (`command=...`) koje se obavezno izvode prije otvaranja shella. Tako rade Github i Heroku protokoli, koji interno koriste SSH, ali whitelistaju samo dio naredbi, dok otvaranje shella ne dopuštaju.

Neke kul opcije:
* `~C` dok si u shellu otvara SSH konzolu, gdje možeš dodati opcije kao `-L` i `-R`.
* `~?` za popis svih escape sekvenci.
* `-fNn` za tuneliranje bez da se otvori remote terminal.

Obavezno koristi passwordless authentifikaciju.

`ssh-keygen` generira private/public key pair. Koristi *passphrase* da se zaštitiš od sudoera.
`ssh-copy-id <user>@<host>` za dodavanje svog public keya na remote machine (trebaš znati password).

`ssh-agent` dozvoljava da samo jednom ukucaš passphrase (drži ga u memoriji tijekom login sessiona).
`ssh-add` dodaje novi private key u `ssh-agent`.

Lokalni private key file mora biti `600`, `~/.ssh/authorized_keys` mora biti `644`.

Sistemska konfiguracija stoji u `/etc/ssh/ssh_config`, a lokalna u `~/.ssh/config`. Neke postavke:
* `Port` definira custom port za spajanje
* `LocalForward` za definiranje tunela.
* `Compression yes` korisno za spore servere.
* `ServerAliveInterval 15`, `ServerAliveCountMax 6` šalji keep alive svakih 15 sekundi, do 6 puta. Ako server krepne, veza će se disconnectati nakon 15*6=90 sekundi.

## File transfer

`scp myfile.txt app@shitake:/remote/directory` kopira lokalni file na stroj `shitake` koristeći usera `app`. Za direktorije koristi `-r`.

`rsync -aP --delete /local/directory/ app@shitake:/remote/directory` kopira direktorij na remote stroj, ali je brži jer ne kopira postojeće fileove. Odlično za sinkroniziranje direktorija. `-a` radi rekurzivno i čuva sve permissione, `-P` daje progress bar i omogućava resume ako se veza prekine, `--delete` briše fileove u `<dest>` ako ih nema u `<src>`.

`rsync -nv` koristi za probu, ne kopira ništa nego ispisuje što bi se sve kopiralo.

