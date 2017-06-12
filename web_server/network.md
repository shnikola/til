# Network

## OSI Model Layers

* Layer 1: struja, žice, wifi, frekvencije
* Layer 2: ethernet protokol
* Layer 3: IP adrese
* Layer 4: TCP + UDP, portovi
* Layer 5 + 6: o njima nitko ne priča
* Layer 7: HTTP i ostali

Ako je neki alat "L3", znači da zna samo za IP adrese, a ignorira sve više protokole. Slično, L7 load balancer koristi HTTP headere (npr. Host).

## Packet Anatomy

U mreži se komunicira pomoću paketa koji su građeni slojevito:
1. U srži paketa nalazi se payload, primjerice HTTP paket.
2. Oko njega je omotan TCP paket radi očuvanja redoslijeda i obrane od korupcije podataka.
3. Oko njega je IP paket s IP adresama izvora i odredišta kako bi paket stigao na pravi server.
4. Oko njega je ethernet/wifi paket s MAC adresom koja se konstantno mijenja kako se paket kreće od računala do računala.

## What really happens when you navigate to a URL

http://igoro.com/archive/what-really-happens-when-you-navigate-to-a-url/

1. Unesemo url wikipedia.org/login u browser
2. Browser DNS-om dohvaća IP adresu za "wikipedia.org".
3. Browser šalje request na IP: URL, tko sam (User-Agent), što želim (Accept, Accept-Encoding), da održi TCP vezu (Connection), i stanje klijenta (Cookies)
4. Server odgovara s `301 Moved Permanently`, `Location: https://www.wikipedia.org/`
5. Browser šalje isti request na novu lokaciju.
6. Server generira HTML i vrati ga nazad.
7. Browser počinje renderirati HTML, radeći dodatne requestove pri nailasku na objekte koje treba dohvatiti (images, css, js). Svaki od tih dodatnih requestova prolazi kroz ovaj isti proces (uključujući i DNS). Ali statični fileovi se često serviraju iz browser cachea, koristeći Expires (datum) i ETag (verzija) headere.

## DNS

*Registrar* onaj kod kojeg si kupio domenu. Drži podatke o lokacijama name servera.

*DNS hosting provider* hosta name servere. Može biti u sklopu registrara, server hostinga ili dedicated kompanija.

*Name server* hosta ili cacheira prijevode domena u IP adrese. Poznat kao i DNS server.

*DNS zone* jedna ili više domena kojim upravlja jedan manager. Konfiguracija se drži u Zone Fileu.

*Zone File* sadrži mappinge domena u IP adrese ili druge resource. Svaki mapping ima `name` (što se mapira), `record class` (IN za Internet), `record type`, `record data` i `ttl` (vrijeme trajanja cachea)

Vrste recorda:
* `A`: usmjerava (sub)domenu IPv4 adresu. Host može biti `@` (root), `pitalica` (subdomena), ili `*` (fallback)
* `AAAA`: isto kao i `A`, samo za IPv6
* `CNAME`: alias na `A` record (`puzzle` -> `pitalica`) ili drugu domenu (`blog` -> `journal.srijeda.com`)
* `MX`: usmjerava na `A` record ili drugu domenu kojoj će proslijediti primljeni email. `@` host za glavnu domenu.
* `TXT`: drži tesktualne podatke, uglavnom za verifikaciju
* `NS`: nameserveri authoritive za domenu. Host može biti `@` (za trenutnu zonu) ili `pitalica` (delegiranje subodmene u drugu zonu)

## DNS Resolving

https://howdns.works/

1. Browser traži IP adresu za wikipedia.org.
2. Provjerava u browser cacheu; ako nema:
3. Provjerava u OS cacheu; ako nema:
4. Provjerava s resolverom (ISP server); ako on nema u cacheu:
5. Resolver pita root server. Postoji 13 root name IP-jeva (`a.root-servers.net`, `b.root-servers.net`...) na cijelom svijetu, rasprostranjenih po svakom kontinentu. Ako root server ne zna:
6. Resolver se šalje na .ORG Top-level domain server. Resolver cachira adresu TLD servera da ne mora idući put pitati root server. Ako TLD server ne zna:
7. Resolver se šalje na autorativne nameservere (npr. `ns1.wikipedia.org`). Registrar je zadužen da pri registriranju domene pošalje svoje nameservere TLD registru. `WHOIS` query vraća autorativne nameservere za domenu. Oni će uvijek imati IP adresu domene.
8. Resolver vraća OS-u IP adresu koji je cachira i prosljeđuje browseru.

## IPv6

IPv4 adrese imaju 32 bita, što znači da postoji samo 4 milijarde različitih IP adresa. U zadnje vrijeme to postaje premalo.

Ipv6 imaju 128 bitova. OS-ovi ih podržavaju, ali serveri još nisu spremni.

## data-uri _IE8+_

URI koji sadrži inline data. Koriste se kako bi se uštedio request.
Format je `data:[<media type>][;base64],<data>`. (npr. `data:image/png;base64,iVBORw0KGgoAA...`)

## Kako iza domene može stajati više IP-jeva

* Round-robin DNS - vraća jedan od nekoliko IP-jeva (nije preporučen http://serverfault.com/questions/60553/why-is-dns-failover-not-recommended)
* GeoDNS - vraća IP ovisno o korisnikovoj lokaciji
* Load Balancer - stroj koji prosljeđuje request ostalim serverima (npr. HAProxy)
* Anycast je routing metoda u kojoj je IP mapiran na više fizičkih servera, ali se ne koristi s TCP-om.
