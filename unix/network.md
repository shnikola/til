# Network

## Debugging

`ping -c 3 www.google.com` šalje 3 ICMP echo requesta na odredište i čeka na ICMP echo reply. Ispisani podatci sadrže `icmp_seq` (redni broj paketa), `ttl` (koliko hopova dopuštamo paketu), i `time` (roundtrip vrijeme od slanja paketa do primitka odgovora).

`traceroute google.com` ispisuje put kojim paket putuje od tvog računala do servera. Interno radi tako da šalje ICMP echo inkrementirajući TTL dok ne dobije odgovor

## portovi

`cat /etc/services` popis servisa i portova koje koriste.

## ifconfig

`ifconfig` ispisuje listu network interfacea koji omogućuju da kernel radi s network hardwareom. Neki od uobičajenih su `eth0` (ethernet), `wlan0` (wireless), i `lo` (loopback na samog sebe, za `127.0.0.1`).

Uz svaki interface stoje dodatni detalji: `HWaddr` (Mac adresa), `inet` (IP adresa), `inet6` (IPv6 adresa).

## iproute2

Skup alata za konfiguraciju i prikupljanje informacija o mreži.
`ip link` prikazuje stanje svakog linka. `ip link show eth0` za prikaz jednog.
`ip -s link` prikazuje statistiku paketa po linku.
`ip addr` prikazuje adrese svakog linka (kao `ifconfig`)
`ip route` prikazuje route prema vanjskom internetu.

`ip link set eth1 multicast on` za konfiguraciju linka.

## DNS

`nslookup utorkom.com` name server lookup, najstariji. Vraća IP i DNS server kojeg je pitao.

`host utorkom.com` dobar za jednostavni query. Ima i reverse lookup: `host 213.11.172.228`. Vraća više informacija s `-a`.

`dig utorkom.com` najviše opcija. `dig NS utorkom.com` vraća nameservere. `dig utokom.com any` za cijeli zone file. `dig @ns1.example.com utorkom.com` traži podatke od zadanog nameservera.

`whois utorkom.com` dohvaća podatke o registriranim korisnicima Internet resourca (npr. domene ili IP bloka)

## httpie

Kao `curl`, samo fino formatiran, lijepo obojen i pristojnog apija.
* `http PUT example.org` osnovni poziv
* `X-API-Token:123` za headere
* `field=value` za parametre, po defaultu se šalje kao JSON, s `-f` kao forma
* `field:='{"name": "nikola"}'` za raw JSON.
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

