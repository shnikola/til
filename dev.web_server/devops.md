# Server Management

## Setup

Dovoljna su 2 accounta: `root` i `deploy`. Svi developeri se logiraju u `deploy` pomoću public keyeva. S `passwd` promijeni root password u nešto dugo i složeno. Ne trebaš pamtiti, samo ga spremi na sigurno, ako izgubiš `sudo` password `deploy` usera.

`adduser deploy` stvara deploy usera.
`usermod -aG sudo deploy` dodaje ga u sudoere.

Ulogiraj se kao `deploy`, i dodaj public key u `~/.ssh/authorized_keys`.

`nano /etc/ssh/sshd_config` za konfiguraciju SSH-a:
* `PermitRootLogin no` zabranjuje remote root login.
* `PasswordAuthentication no` zabranjuje password auth.
* `Port 20022` da izbjegneš port sniffere.
* ako želiš ograničiti na IP, `AllowUsers deploy@<ip>`.

`sudo service ssh restart` da se primjeni nova konfiguracija.

`apt-get install fail2ban`, dobro je iskonfiguriran out of the box.

## Monitoring

`uname -a` ispisuje podatke o OS-u.
`hostname` ispisuje sistemsko ime u mreži.
`cat /proc/cpuinfo` ispisuje podatke o procesoru, broj jezgri.
`date` ispisuje trenutno vrijeme.

`history` ispisuje sve prethodnih naredbi.
`last reboot` ispisuje system rebootove.

`netstat -lnp` ispisuje procese koje imaju otvorene sockete.
`lsof -i tcp:3000` ispisuje procese na portu `3000`.

`lsof` (list open files) ispusuje sve fileove koje su trenutno otvoreni i procese koji ih koriste. Korisno ako se npr. USB ne želi ejectati, a ne znaš tko ga koristi.
`lsof <file>` ispuje tko sve koristi zadani file.

`uptime` ispisuje koliko dugo već system radi, te load zadnjih 1, 5 i 15 min.
Load se računa kao prosječni broj procesa koji čekaju na CPU. Ako ti je load `1.0`, a imaš 4 jezgre, znači da se koristi samo 1 jezgra.

`top` ispisuje procese koji najviše troše CPU.

`iostat` osim potrošnje CPU-a po kategorijama (user, nice, system...), ispisuje korištenje pojedinih diskova (broj zahtjeva po sekundi, količina čitanih i pisanih podataka)

`free -m` ispisuje slobodnu radnu memorija u MB.

`du -h /home` za ispis koliko zauzima koji file u direktoriju. Nepregledno kad je puno fileova. Umjesto toga, koristi `ncdu` kao mini file browser.

## cronjob

Za automatsko pozivanje skripti, koristi `cron`. `crontab -e` otvara popis naredbi formata `30 08 * * * /home/some_script`. Format definira kada će se skripta izvršavati - zvjezdice matchaju sve, pa će se ova skripta pozivati svaki dan u 8:30.

Pripazi, `cronjob` nije interaktivni shell pa ne učitava `.bash_profile` ni `.bashrc`. Možeš ih ručno pozvati u skripti s `source /home/user/.bash_profile`.

## logging

`/var/log/dmesg` sadrži podatke o driverima, kernelu i bootupu.
`/var/log/syslog` sadrži sve sistemske stvari koje stižu u `syslogd` daemon.
`/var/log/auth.log` sadrži sve vezano uz autentifikaciju.

Kako bi ograničio veličinu logova, koristi se `logrotate`. Defaultno je postavljen da tjedno rotira logove kojima je vlasnik root i syslog grupa.

U `/etc/logrotate.conf` nalazi se glavna konfiguracija, a u `/etc/logrotate.d/` konfiguracije za ne-sistemske aplikacije (nginx, mysql i sl.).

## ufw

`ufw` za jednostavni firewall. `ufw allow 22` (dodaj `from <ip>` ako treba), `ufw allow 80`, `ufw allow 443`. Na kraju, `ufw enable`

## monit

`monit` prati pokrenute servise (npr. `nginx`, `mysql`) i restarta ih ako se crashaju. Konfiguracija je u `/etc/monit/monitrc`. Učitaj s `monit reload`.

Čak je dostupan i web interface na portu po odabiru (npr. `example.com:2812`).
