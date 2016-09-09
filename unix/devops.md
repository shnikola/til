# Devops

TODO
supervise

remote_syslog - šalje logove na papertrail, konfiguracija: `/etc/log_files.yml`

Unicorn se kršio s:
`Operation not permitted @ rb_file_chown - /home/jugosluh/shared/log/unicorn.log`
U `unicorn.rb` user nije bio postavljen na "app" s kojim sam deployao iz mine (on je stvarao foldere i sve)


## Security
Prebaci `ssh` na neki visoki port - eliminira većinu port sniffera.
Disablaj password login za `ssh`.
`fail2ban`


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


## SSH
`ssh <user>@<host>` otvara shell na remote stroju. Ali može i još mnogo drugih stvari!
  * *Local port forwarding* (`-L <local>:<remote>`) šalje *lokalni* promet SSH tunelom do *remote* hosta. Korisno za spajanje na serverov DB.
  Primjer: `-L 9000:localhost:5432` promet lokalnog porta `9000` šalje na SSH server koji ga prosljeđuje svom localhostu na port `5432` (gdje se nalazi DB).

  * *Remote port forwarding* (`-R <remote>:<local>`) šalje promet s *remote* hosta SSH tunelom na *lokalni* stroj. Korisno za spajanje izvana na tvoj lokalni stroj dok developaš.
  Primjer: `-R 9000:localhost:3000` promet `<host>:9000` prosljeđuje se tvom lokalnom računalu na port `3000`.

  * *Dynamic port forwarding* (`-D <port>`) koristi lokalni port kao SOCKS proxy, pa *sav* promet možeš tunelirati preko SSH.
  Primjer: `ssh -D 5555 server`, i u browseru ili curlu možeš podesiti proxy na `localhost:5555`.

  * *Agent Forwarding* (`-A`) prosljeđuje tvoje lokalne private keyeve SSH serveru, pa možeš pullati s Githuba bez da dodaješ deployment keyeve. Moraš turbo vjerovati serveru da bi ovo koristio.

  * *Command execution* (`ssh <user>@<host> <cmd>`) izvodi naredbu na remote stroju, a rezultat možeš normalni pipeati lokalno. U `.ssh/authorized_keys` mogu se dodati naredbe (`command=...`) koje se obavezno izvode prije otvaranja shella. Tako rade Github i Heroku protokoli, koji interno koriste SSH, ali whitelistaju samo dio naredbi, dok otvaranje shella ne dopuštaju.

Neke kul opcije:
  * `~C` dok si u shellu otvara SSH konzolu, gdje možeš dodati opcije kao `-L` i `-R`.
  * `~?` za popis svih escape sekvenci.
  * `-fNn` za tuneliranje bez da se otvori remote terminal.

Passwordless authentifikaciju obavezno koristi:
  * `ssh-keygen` generira private/public key pair. Koristi *passphrase* da se zaštitiš od sudoera.
  * `ssh-copy-id <user>@<host>` za dodavanje svog public keya na remote machine (trebaš znati password).
  * lokalni private key file mora biti `600`, `~/.ssh/authorized_keys` mora biti `644`.
  * `ssh-agent` dozvoljava da samo jednom ukucaš passphrase (drži ga u memoriji tijekom login sessiona).
  * `ssh-add` dodaje novi private key u `ssh-agent`.

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


## cronjob
cronjob nije interaktivni shell pa ne učitava `.bash_profile` ni `.bashrc`. Možeš ih ručno pozvati s:
`source /home/user/.bash_profile`


## nginx
config: `/etc/nginx/sites-available`
`listen 80 default_server` opcija hvata sve nematchane hostove (nemoj koristiti `server_name _;`)


## Automatska provjera https certifikata
http://prefetch.net/code/ssl-cert-check
`cd ssl/certs; for pem in *.pem; do ssl-cert-check -a -x 15 -e admin@yourdomain.com -q -c $pem; done`


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

## httpie
Kao `curl`, samo fino formatiran, lijepo obojen i pristojnog apija.
  * `http PUT example.org` - osnovni poziv
  * `X-API-Token:123` - custom header
  * `field=value` - data, po defaultu se šalje kao JSON, s `-f` kao forma
  * `field:=[1,2,3]` - raw JSON, ako value treba biti number, boolean ili array
  * `--session=logged-in` - čuva cookije i autorizaciju

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
`ngrep -q -W byline "^(GET|POST) .*"` - hvata sve pakete koji imaju GET ili POST
`ngrep -q -W byline "search" host www.google.com and port 80` - hvata sve pakete na google koji u sebi imaju riječ "search".


## siege
Za load testing.
`siege -c20 www.google.com -t30s` - po 20 concurrent konekcija idućih 30 sekunda
`-f urls.txt` - uzima urlove iz filea. Dobro za replay prometa iz logova.
