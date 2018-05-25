# IP

## Putovanje paketa

Svaki host pripada subnetu, lokalnoj mreži unutar koje je razmjena paketa jednostavnija. IP adresa se sastoji od network dijela (označava subnet) i host dijela (označava računalo). Subnet se označava s `123.12.24.0/23`, gdje je `23` broj bitova u mrežnom dijelu IP adrese, a preostalih `9` se koristi za označavanje računala, npr. `123.12.25.8`.

Kada računalo treba poslati paket na odredište, prvo projerava nalazi li se odredište u istom subnetu. Ako su u istom subnetu, pomoću ARP protokola saznaje MAC adresu odredišnog računala i šalje mu paket.

Ako odredište nije u istom subnetu, pogledat će u svoj routing table. Tamo će pronaći IP svog gatewaya (routera između subneta i vanjskog interneta). Gatewayi tradicionalno koriste adresu `1` u subnetu, npr. `192.168.1.1`. Pomoću ARP protokola saznaje MAC gatewaya i šalje mu paket. Router prima paket i prema svojoj routing tablici ga prosljeđuje na iduću IP adresu, dok ne dođe do odredišnog subneta. Prilikom proslijeđivanja, zapisani IP odredišta ostaje isti, samo se MAC odredišta mijenja.

## Routing

Routeri su uređaju koji omogućuju komunikaciju između subneta. Svaki router posjeduje routing tablicu koja sadrži putanje za pojedina odredišta. Routeri međusobno razmjenjuju informacije o mreži kako bi postigli *konvergenciju* u kojoj svi posjeduju iste podatke i mogu donositi najbolje odluke o usmjeravanju paketa. Za donošenje takvih odluka koriste se različiti routing protokoli.

Distance vector protocols (npr. *Routing Information Protocol*) određuju putanju prema broju *hopova* (posrednih uređaja) između routera i odredišta. Dobar je za manje mreže, ali se loše skalira jer zahtjeva slanje cijelih routing tablea svim routerima svakih 30 sekundi.

Link state protocols (npr. *Open Shortest Path First*) su bolji za veće mreže. Brže konvergiraju jer ne šalju cijeli routing table, nego samo promjene susjednim routerima.

## ARP

ARP mapira IP adrese u MAC adrese. Svaki mrežni uređaj ima cache tablicu. Kada mu stigne IP adresa s kojom se nije susreo, poslat, poslat će broadcast svim uređajima u mreži. Uređaj s traženim IP-jem će odgovoriti sa svojom MAC adresom. Adresa se cachira, a paket šalje dalje.

## DHCP

IP adrese se računalima mogu dodijeljivati ručno, ali onda nema zaštite od duplikata, a i velika je gnjavaža. Umjesto toga, svaka fizička mreža ima svoj DHCP server zadužen za dodijeljivanje IP adresa.

1. Računalo pri spajanju na mrežu šalje `DHCP DISCOVER` broadcast u potrazi za DHCP serverima.
2. Svaki server šalje `DHCP OFFER` pakete koji sadrže novu IP adresu, rok trajanja i sl.
3. Klijent odabire jednu od ponuda i šalje `DHCP REQUEST` svima s informacijom o svom odabiru.
4. Odabrani server odgovara s `DHCP ACK`.

## NAT, STUN, TURN i ICE

Zbog ograničenog adresnog prostora IPv4, sredinom 90-ih privremeno su uvedeni *NAT* uređaji. Postavljeni na rub mreže, oni drže tablice koje mapiraju `(local IP, port) -> (public IP, port)`, tako da klijenti iza različitih NATova mogu reusati iste privatne IP-je. Nažalost, privremeno rješenje postalo je stalan dio internetske infrastrukture.

TCP ima točno definirano otvaranje i zatvaranje konekcije, pa NAT zna kada dodati ili obrisati routing zapis u tablicu. Ali u UDP-u konekcija ne postoji. Nakon poslanog datagrama, odgovor možda dođe, a možda i ne. NAT ne zna koliko dugo da čuva routing zapis u svojoj tablici. Zato UDP routing zapisi imaju timeout, koji ovisi od NATa do NATa. Best practice za duge sessione preko UDP-a je periodički slati keepalive pakete u oba smjera kako NATovi po putu ne bi obrisali taj mapping. (Ista timeout situacija se zna dogoditi čak i s TCP konekcijama, iako ne bi trebalo. Glupi NATovi.)

Još jedan problem s NATom je što pošiljatelj iz interne mreže ne zna svoj public IP. Ako ga šalje kao application data (npr. zbog autentifikacije ili određivanja geolokacije), poslat će svoj lokalni što primatelj neće moći koristiti. Drugi problem je što paket iniciran iz javne mreže (npr. VOIP, file sharing i sl.) neće proći NAT jer još ne postoji routing zapis u tablici. On se stvara tek na slanje paketa iz interne mreže.

Te probleme rješava *STUN* protokol. STUN server je postavljen na javnoj mreži. Aplikacija prvo šalje request STUN serveru i on joj vraća njen vlastiti `(public IP, port)` koji se vide iz javne mreže. Taj request u istovremeno dodaje i routing zapis u NAT pa se mogu primati inbound requesti. STUN definira i mehanizam za keepalive pingove kako se NAT tablica ne bi resetirala.

Kada 2 peera žele komunicirati komunicirati preko UDP-a, svaki pošalje STUN da sazna svoj javni IP i port, pošalju ih jedan drugome i preko njih razmjenjuju podatke.

Ali za neke komplicirane NAT topologije ni to nije dovoljno (npr. UDP zna biti blokiran firewallom). U slučaju kad STUN faila, koristi se *TURN*. Oba peera se spajaju na relay server i preko njega razmjenjuju podatke. To nije više peer-to-peer, ali je ok fallback (u stvarnom svijetu, 8% konekcija zahtjeva TURN).

*ICE* protokol je skup metoda za pronalaženje najefikasnijeg povezivanja dviju strana: bilo direktno, bilo STUN-om ili TURN-om.

## IPv6

IPv4 adrese imaju 32 bita, što znači da postoji samo 4 milijarde različitih IP adresa. U zadnje je vrijeme to postalo premalo.

IPv6 imaju 128 bitova. OS-ovi ih podržavaju, ali serveri još nisu spremni.


