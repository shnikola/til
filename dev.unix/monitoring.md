# Monitoring

## Stats

`uname -a` ispisuje podatke o OS-u.
`hostname` ispisuje sistemsko ime u mreži.
`date` ispisuje trenutno vrijeme.

`history` ispisuje sve prethodnih naredbi.
`last reboot` ispisuje system rebootove.

`netstat -lnp` ispisuje procese koje imaju otvorene sockete.
Pomoću `lsof -i TCP:3000` možeš vidjeti tko je sve pokrenut na portu `3000`.

## Continuous Monitoring

`watch -n 5 <command>` poziva naredbu svakih 5 sekundi (npr. `watch -n 5 df -h`).

S instalacijom `sysstat` paketa, automatski se sakupljaju dnevni podatci kojima poslije možeš pristupiti.
`sudo sar -q` ispisuje podatke od početka današnjeg dana.
`sudo sar -q /var/log/sysstat/sa<index>` za neki drugi dan.

Za automatsko pozivanje skripti, koristi `cron`. `crontab -e` otvara popis naredbi formata `30 08 * * * /home/some_script`. Format definira kada će se skripta izvršavati - zvjezdice matchaju sve, pa će se ova skripta pozivati svaki dan u 8:30.

Pripazi, `cronjob` nije interaktivni shell pa ne učitava `.bash_profile` ni `.bashrc`. Možeš ih ručno pozvati u skripti s `source /home/user/.bash_profile`.

## Files

`lsof` (list open files) ispusuje sve fileove koje su trenutno otvoreni i procese koji ih koriste. Korisno ako se npr. USB ne želi ejectati, a ne znaš tko ga koristi.
`lsof <file>` ispuje tko sve koristi zadani file.

## CPU

`uptime` ispisuje koliko dugo već system radi, te load zadnjih 1, 5 i 15 min.
Load se računa kao prosječni broj procesa koji čekaju na CPU. Ako ti je load `1.0`, a imaš 4 jezgre, znači da se koristi samo 1 jezgra.

`cat /proc/cpuinfo` ispisuje podatke o procesoru, broj jezgri.

`top` ispisuje procese koji najviše troše CPU.

## IO

`iostat` osim potrošnje CPU-a po kategorijama (user, nice, system...), ispisuje korištenje pojedinih diskova (broj zahtjeva po sekundi, količina čitanih i pisanih podataka)

## RAM

`free -m` ispisuje slobodnu radnu memorija u MB.

## Disk

`du -h /home` za ispis koliko zauzima koji file u direktoriju. Nepregledno kad je puno fileova. Umjesto toga, koristi `ncdu` kao mini file browser.

## Logging

`/var/log/dmesg` sadrži podatke o driverima, kernelu i bootupu.
`/var/log/syslog` sadrži sve sistemske stvari koje stižu u `syslogd` daemon.
`/var/log/auth.log` sadrži sve vezano uz autentifikaciju.

Kako bi ograničio veličinu logova, koristi se `logrotate`. Defaultno je postavljen da tjedno rotira logove kojima je vlasnik root i syslog grupa.

U `/etc/logrotate.conf` nalazi se glavna konfiguracija, a u `/etc/logrotate.d/` konfiguracije za ne-sistemske aplikacije (nginx, mysql i sl.).

