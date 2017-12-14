# Server Management

## TODO

chef i puppet za automatsku konfiguraciju servera

## Accounts

Dovoljna su 2 accounta: `root` i `deploy`. Svi developeri se logiraju u `deploy` pomoću public keyeva. S `passwd` promijeni root password u nešto dugo i složeno. Ne trebaš pamtiti, samo ga spremi na sigurno, ako izgubiš `sudo` password `deploy` usera.

## apt-get

Koristi `apt-get update` i `apt-get upgrade`.

## ssh

Iskonfiguriraj SSH s `nano /etc/ssh/sshd_config`:
* postavi `PermitRootLogin no` i `PasswordAuthentication no`
* postavi port na neki visoki broj (npr. `Port 20022`) da izbjegneš port sniffere.
* ako želiš ograničiti na IP, `AllowUsers deploy@<ip>`.
Na kraju, `sudo service ssh restart`.

## fail2ban

Napravi `apt-get install fail2ban`, dobro iskonfiguriran out of the box.

## ufw

`ufw` za jednostavni firewall. `ufw allow 22` (dodaj `from <ip>` ako treba), `ufw allow 80`, `ufw allow 443`. Na kraju, `ufw enable`

## monit

`monit` prati pokrenute servise (npr. `nginx`, `mysql`) i restarta ih ako se crashaju. Konfiguracija je u `/etc/monit/monitrc`. Učitaj s `monit reload`.

Čak je dostupan i web interface na portu po odabiru (npr. `example.com:2812`).

## logging

`papertrail` za praćenje logova.
