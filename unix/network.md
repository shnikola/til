# Network

## SSH

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

## rsync

`rsync <src>/ <user>@<host>:<dest>` sinkronizira directorije između različitih sistema, plus je super brz.
  * `-nv` ne kopira ništa nego ispisuje što bi se sve kopiralo. Odlično prije stvarnog poziva.
  * `-a` radi rekurzivno i čuva sve permissione.
  * `-P` daje progress bar i omogućava resume ako se veza prekine.
  * `--delete` briše fileove u `<dest>` ako ih nema u `<src>`.

## iproute2

Skup alata za konfiguraciju i prikupljanje informacija o mreži.
`ip link` prikazuje stanje svakog linka. `ip link show eth0` za prikaz jednog.
`ip -s link` prikazuje statistiku paketa po linku.
`ip addr` prikazuje adrese svakog linka (kao `ifconfig`)
`ip route` prikazuje route prema vanjskom internetu.

`ip link set eth1 multicast on` za konfiguraciju linka.

## traceroute

`traceroute google.com` ispisuje put kojim paket putuje od tvog računala do servera.

## httpie

Kao `curl`, samo fino formatiran, lijepo obojen i pristojnog apija.
* `http PUT example.org` osnovni poziv
* `X-API-Token:123` za custom headere
* `field=value` za parametre, po defaultu se šalje kao JSON, s `-f` kao forma
* `field:=[1,2,3]` za raw JSON, ako value treba biti number, boolean ili array
* `--session=logged-in` čuva cookije i autorizaciju

## tcpdump

http://jvns.ca/blog/2016/03/16/tcpdump-is-amazing/

Za capture TCP prometa.
`sudo tcpdump -i wlan0` - hvata sav promet s interfacea `wlan0`.
`sudo tcpdump -i wlan0 port 80 -w output.pcap` - sluša odlazni i dolazni port 80 te sprema u PCAP file koji kasnije možeš otvoriti s wiresharkom. Prihvaća i druge opcije:
`dst port 80` - sve što se šalje na port 80
`ip src 66.66.66.66` - sve što se prima od IP adrese
`src port 80 or dst port 80` - prima i boolean operatore
Za dump na strojevima s velikim prometom, s `-c 10000` ograniči broj paketa koji će se capturati.

## ngrep

Slično kao `tcpdump`, ali ima i podršku za regexe (kao grep).
`ngrep -q -W byline "^(GET|POST) .*"` hvata sve pakete koji imaju GET ili POST
`ngrep -q -W byline "search" host www.google.com and port 80` hvata sve pakete na google koji u sebi imaju riječ "search".

## siege

Za load testing.
`siege -c20 www.google.com -t30s` stvara 20 concurrent konekcija idućih 30 sekunda.
`-f urls.txt` uzima urlove iz filea. Dobro za replay prometa iz logova.

## ngrok

Za testiranje lokalnog servera s vanjskog stroja. Samo pokreni `ngrok http 3000` i dobit ćeš url tipa `https://83832de1.ngrok.io`.

