# Network

## OSI Model Layers

* Layer 1: struja, žice, wifi, frekvencije
* Layer 2: ethernet protokol, MAC adrese
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

## Lifecycle requesta

1. Unesemo url wikipedia.org/login u browser.
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
* `TXT`: drži tekstualne podatke, uglavnom za verifikaciju
* `NS`: autorativni nameserveri za domenu. Host može biti `@` (za trenutnu zonu) ili `pitalica` (delegiranje subodmene u drugu zonu)

## DNS Resolving

https://howdns.works/

1. Browser traži IP adresu za `wikipedia.org`.
2. Provjerava u browser cacheu; ako nema:
3. Provjerava u OS cacheu; ako nema:
4. Provjerava s resolverom (ISP server); ako on nema u cacheu:
5. Resolver pita root server. Postoji 13 root name IP-jeva (`a.root-servers.net`, `b.root-servers.net`...) na cijelom svijetu, rasprostranjenih po svakom kontinentu. Ako root server ne zna:
6. Resolver se šalje na .ORG Top-level domain server. Resolver cachira adresu TLD servera da ne mora idući put pitati root server. Ako TLD server ne zna:
7. Resolver se šalje na autorativne nameservere (npr. `ns1.wikipedia.org`). Registrar je zadužen da pri registriranju domene pošalje svoje nameservere TLD registru. `WHOIS` query vraća autorativne nameservere za domenu. Oni će uvijek imati IP adresu domene.
8. Resolver vraća OS-u IP adresu koji je cachira i prosljeđuje browseru.

## IPv6

IPv4 adrese imaju 32 bita, što znači da postoji samo 4 milijarde različitih IP adresa. U zadnje je vrijeme to postalo premalo.

IPv6 imaju 128 bitova. OS-ovi ih podržavaju, ali serveri još nisu spremni.

## UDP

UDP je super lightweight (zovu ga i _null protocol_). Šalje se IP-om (koji je nepouzdan) i sve što dodaje userovom payloadu su 4 polja: source port (optional), destination port, length i checksum (optional, IP ima svoj).

* Ne garantira isporuku paketa.
* Ne garantira redoslijed isporuke.
* Ne postoji connection state.
* Nema congestion control.

Za razliku od TCP-a čija se poruka može sastojati od više paketa, u UDP-u je 1 datagram = 1 poruka. (Inače, *datagram* je naziv za paket poslan kroz nepouzdan servis, bez notifikacije o uspjehu ili neuspjehu isporuke)

Glavni feature UDP-a je da nema featurea, i zato je prikladan za izgradnju novog protokola nad njim. Ali nemoj to raditi pobogu, koristi nešto postojeće i zrelo (npr. WebRTC).

## NAT, STUN, TURN i ICE

Zbog ograničenog adresnog prostora IPv4, sredinom 90-ih privremeno su uvedeni *NAT* uređaji. Postavljeni na rub mreže, oni drže tablice koje mapiraju `(local IP, port) -> (public IP, port)`, tako da klijenti iza različitih NATova mogu reusati iste privatne IP-je. Nažalost, privremeno rješenje postalo je stalan dio internetske infrastrukture.

TCP ima točno definirano otvaranje i zatvaranje konekcije, pa NAT zna kada dodati ili obrisati routing zapis u tablicu. Ali u UDP-u konekcija ne postoji. Nakon poslanog datagrama, odgovor možda dođe, a možda i ne. NAT ne zna koliko dugo da čuva routing zapis u svojoj tablici. Zato UDP routing zapisi imaju timeout, koji ovisi od NATa do NATa. Best practice za duge sessione preko UDP-a je periodički slati keepalive pakete u oba smjera kako NATovi po putu ne bi obrisali taj mapping. (Ista timeout situacija se zna dogoditi čak i s TCP konekcijama, iako ne bi trebalo. Glupi NATovi.)

Još jedan problem s NATom je što pošiljatelj iz interne mreže ne zna svoj public IP. Ako ga šalje kao application data (npr. zbog autentifikacije ili određivanja geolokacije), poslat će svoj lokalni što primatelj neće moći koristiti. Drugi problem je što paket iniciran iz javne mreže (npr. VOIP, file sharing i sl.) neće proći NAT jer još ne postoji routing zapis u tablici. On se stvara tek na slanje paketa iz interne mreže.

Te probleme rješava *STUN* protokol. STUN server je postavljen na javnoj mreži. Aplikacija prvo šalje request STUN serveru i on joj vraća njen vlastiti `(public IP, port)` koji se vide iz javne mreže. Taj request u istovremeno dodaje i routing zapis u NAT pa se mogu primati inbound requesti. STUN definira i mehanizam za keepalive pingove kako se NAT tablica ne bi resetirala.

Kada 2 peera žele komunicirati komunicirati preko UDP-a, svaki pošalje STUN da sazna svoj javni IP i port, pošalju ih jedan drugome i preko njih razmjenjuju podatke.

Ali za neke komplicirane NAT topologije ni to nije dovoljno (npr. UDP zna biti blokiran firewallom). U slučaju kad STUN faila, koristi se *TURN*. Oba peera se spajaju na relay server i preko njega razmjenjuju podatke. To nije više peer-to-peer, ali je ok fallback (u stvarnom svijetu, 8% konekcija zahtjeva TURN).

*ICE* protokol je skup metoda za pronalaženje najefikasnijeg povezivanja dviju strana: bilo direktno, bilo STUN-om ili TURN-om.
