# init

`init` je zadužen pokretanje i zaustavljanje osnovnih sistemskih procesa. Tri su najčešće implementacije: System V, Upstart, i systemd.

## System V

System V je najstarija i najšugavija `init` implementacija, ali je zbog kompatibilnosti većina sustava još podržava. Ako imaš `/etc/inittab` file, najvjerojatnije koristiš System V.

Stanje sustava definirano je pomoću *runlevela* od 0 do 6: 0 je Shutdown, 1 je Single User Mode, 2 je Multiuser mode without networking, itd. Kada se sustav pokreće, provjerava se njegov runlevel, i pokreću sve skripte definirane za njega. Skripte se nalaze u `/etc/rc.d/rc<runlevel>.d`: one čije ime počinje sa `S` se izvršavaju pri paljenju, a koje počinju s `K` pri gašenju. Na mojim serverima te skripte su jednostavno linkane na skripte u `/etc/init.d` kako bi sve bile na jednom mjestu.

`service --status-all` ispisuje sve postojeće servise.
`sudo service networking start` pokreće servis.
`sudo service networking stop` zaustavlja servis.
`sudo service networking restart` zaustavlja i pokreće servis.

## Upstart

Upstart je razvijen kao poboljšanje na System V kako bi se različiti jobovi mogli konfigurirati da reagiraju na evente. Ako imaš `/usr/share/upstart` vjerojatno koristiš Upstart.

U `/etc/init` nalaze se konfiguracije upstart jobova u kojima je definirano kada se pokreću (`start on runlevel [235]`), kada se gase (`stop on runlevel [0]`), zajedno sa skriptama za pokretanje i gašenje.

Upstart pri pokretanju učita sve konfiguracije iz `/etc/init`. Kada se startup event dogodi, izvršava sve jobove koji su triggerani. Ti jobovi će stvoriti nove evente koji će triggerirati nove jobove. Upstart tako nastavlja dok nisu pokrenuti svi potrebni jobovi.

Upstart radi kompatibilnosti prima i `service` naredbe, ali ima i svoje.

`initctl list` ispisuje sve postojeće jobove sa statusom oblika `start/running` gdje je `start` cilj joba, a `running` trenutni status.
`initctl status networking` ispisuje status određenog joba.
`sudo initctl start networking` ručno pokreće job.
`sudo initctl stop networking` ručno zaustavlja job.
`sudo initctl restart networking` ručno restarta job.

## systemd

Systemd polako postaje standardna `init` implementacija. Koristi ciljeve (*target*) koji svaki ima dependecije kako bi se mogao izvršiti. Ako imaš `/usr/lib/systemd`, vjerojatno koristiš systemd.

Systemd pri pokretanju učitava konfiguracijske fileove iz `/etc/systemd/system`. Zatim određuje boot goal, koji je najčešće `default.target`, izračunava dependecije koji su potrebni i aktivira ih. Slično kao i runleveli, boot targeti mogu biti `poweroff.target` (shutdown), `rescue.target` (single user mode) i sl.

Systed se ne ograničava samo sa procese, on može mountati filesysteme, monitorirati sockete itd. Zbog toga ima različite unite kojima upravlja. `.service` uniti su za upravljanje procesima, `.mount` uniti za mountanje filesystema, a `.target` uniti grupiraju više različitih unita.

`systemctl list-units` ispisuje sve unite.
`systemctl status networking.service` ispisuje statu određenog unita.
`sudo systemctl start networking.service` pokreće servis.
`sudo systemctl stop networking.service` zaustavlja servis.

## Shutdown

`sudo shutdown -h now` gasi sustav odmah.
`sudo shutdown -h +2` gasi sustav za 2 minute.
`sudo reboot` restatira sustav.

