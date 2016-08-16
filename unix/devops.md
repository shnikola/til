# Devops

provjera memorije: `free -h`
ako nemaš rama a imaš diska, dodaj swap.

remote_syslog - šalje logove na papertrail, konfiguracija: `/etc/log_files.yml`

Unicorn se kršio s:
`Operation not permitted @ rb_file_chown - /home/jugosluh/shared/log/unicorn.log`
U `unicorn.rb` user nije bio postavljen na "app" s kojim sam deployao iz mine (on je stvarao foldere i sve)

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
`http example.org` - osnovni poziv
`http X-API-Token:123` - custom header
`http field=value` - data, po defaultu se šalje kao JSON, s `-f` kao forma
`http field:=[1,2,3]` - raw JSON, ako value treba biti number, boolean ili array
`http --session=logged-in` - čuva cookije i autorizaciju

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
