# High Performance Browser Networking

https://hpbn.co

## 1. Primer on Latency and Bandwidth

**Latency:** vrijeme od slanja paketa s izvora do primitka u odredištu (sekunde)
**Bandwith:** maksimalna propusnost logičnog ili fizičkog komunikacijskog kanala (bytovi po sekundi)

Na latenciju utječu:
* *propagation delay* (vrijeme putovanja paketa po žici, ovisi o udaljenosti),
* *transmission delay* (vrijeme slanja paketa s routera na žicu, ovisi o routeru i veličini paketa),
* *processing delay* (vrijeme čitanja headera paketa, provjera bit errora, i računanje odredišta paketa)
* *queueing delay* (vrijeme čekanja da router obradi paket - ovisi o routeru i prometu)

Brzina signala u fiber optičkom kablu je 1.5x sporija od brzine svjetlosti - oko 200.000.000 m/s. To znači da je Round Trip Time od New Yorka do Sydneya u najboljem slučaju 160ms. U stvarnosti, s delayevima, radi se o 200-300ms. Što je i dalje brzo, uzevši sve u obzir!

Ali najsporiji dio je "last mile" - spajanje klijenta na lokalni ISP node. Prije nego se paket uopće počne slati na odredište, proći će desetke milisekunda.

Optical fiber je obična svjetlosna cijev (signal uđe, signal izađe), ali različite valne duljine omogućuju istovremeno slanje preko 400 signala u jednom kanalu. Stoga jedan fiber link dostiže bandwith od preko 70 Tib/s!

Ali u odnosu na taj superbrzi "backbone" interneta, kapacitet na rubovima mreže mnogo je manji. Bandwith dostupan korisniku jednak je najmanjem kapacitetu između njega i servera. Prosječan bandwith cijelog svijeta je 5 Mbps, što nije loše, ali na knap ako želiš streamati HD video (50% svog prometa na netu je streamanje videa.)

Zahtjevi za bandwithom rastu, a tehnologija ne može zauvijek napredovati. Zato je potrebno raditi aplikacije koje će na pametan način sakriti latenciju.

## 2. Building Blocks of TCP

Internet koristi dva protokola: IP (host-to-host routing and addressing) i TCP (pruža apstrakciju pouzdane mreže na nepouzdanom kanalu). TCP od aplikacije skriva složenosti mreže: ponovno slanje izgubljenih paketa, očuvanje redoslijeda, izbjegavanje zakrčenja prometa, itd. TCP je optimiziran ne za brzu, nego za preciznu dostavu.

Svaka TCP konekcija počinje s 3-way handshakeom.
1) `SYN` Klijent odabire random broj (`x`) i šalje primaocu.
2) `SYN ACK` Primaoc povećava (`x+1`) i šalje ga sa svojim random (`y`)
3) `ACK` Klijent šalje (`x+1`) i (`y+1`), te može odmah početi slati aplikacijske podatke.

To znači da svaka nova TCP konekcija ima latenciju punog roundtripa prije nego se ikakvi podatci počnu slati. Postoji i TCP Fast Open način rada (još je u draftu) koji omogućuje slanje s prvim SYN paketom, ali s ograničenjima.

Ukoliko je vrijeme roundtripa duže od vremena ponovnog slanja, klijent će početi slati još paketa dodatno zakrčujući promet. Da bi se kontrolirala količina prometa koja se stvara u mreži, TCP ima ugrađene mehanizme: flow control, congestion control i congestion avoidance.

*Flow Control* je mehanizam koji sprječava da jedna strana preplavi drugu s podatcima koje ova ne stigne obraditi. Zato obje strane TCP konekcije šalju svoj *recieve window* (`rwnd`) koji je veličina slobodnog prostora u bufferu za obradu podataka. Obje strane počnu sa veličinama iz sistemskih postavki, a ako jedna strana ne može pratiti tempo podataka, smanjuje svoj `rwnd`. Ako `rwnd` postane 0, toj se strani nikakvi podatci ne smiju slati dok ne pošalje veću vrijednost.

Po originalnoj specifikaciji maksimalan `rwnd` je 16KB, što često nije dovoljno za optimalne performanse. Zato je uveden *Window Scaling* koji dopušta `rwnd` do 1GB. Na većini platformi je eneblan po defaultu, ali moguće je da ga routeri i firewallovi po putu ne podržavaju i onda te sjebu.

*Slow Start* je mehanizam koji sprečava da obje strane zaguše mrežu između sebe. Pošiljatelj kod sebe čuva *congestion window size* (`cwnd`) koji je veličina podataka koju smije slati bez da dođe do packet lossa. Ukupna veličina paketa koji su na žici prema primaocu ni u jednom trenutku ne smije biti veća ni od `rwnd` ni od `cwnd`. Cwnd počinje sporo - od 10 segmenata (14KB) - i za svaki uspješni ACK se povećava za 1 (tj. za svaki uspješan primitak, dva paketa se šalju) i tako raste eksponencijalno. Vrijeme da dođemo do punog kapaciteta tako ovisi o početnoj vrijednosti cwnda i vremenu roundtripa. Međutim, čim se detektira packet loss, `cwnd` se resetira (TCP throughput često ima "sawtooth" uzorak), i congestion avoidance algoritmi preuzimaju. TCP je dizajniran da koristi packet loss kao feedback. Ukoliko je konekcija idle duže vrijeme, Slow-Start Restart će resetirati `cwnd` (jer se stanje u mreži moglo promijeniti).

Posljedica slow starta je da, koliki god bandwith imali, TCP konekcija neće odmah imati puni kapacitet. To nije problem za duge streaming konekcije, ali HTTP konekcije koje su "short and bursty" često završe prije nego se dosegao maksimalni window size.

*Bandwidth-Delay Product* (umnožak kapaciteta žice i roundtrip timea) jednak je maksimalnoj količini neprimljenih podataka koja može biti "na žici" u nekom trenutku. S druge strane, ta maksimalna količina jednaka je `min(rwnd, cwnd)`. Neovisno o potencijalnom bandwithu između dvije strane, s premalim window sizeom stvarni promet će biti mnogo manji. A razlog premalom window size može biti: šugavi server koji ima premali `rwnd`, veliki paket loss uzrokuje resetiranje `cwnd`a, i sl.

*Head-of-Line blocking* pojava je uzrokovana TCPovim očuvanjem redoslijeda paketa. Kad se jedan paket izgubi, svi primljeni nakon njega moraju čekati dok on ne bude primljen. Aplikacija to ne zna, i jednostavno vidi to kao delivery delay. Aplikacije kojima nije bitan redoslijed trebale bi umjesto TCP-a koristi UDP.

Sažetak:
* TCP 3-way handshake ima round trip latency.
* svaka konekcija ima slow start.
* flow (`rwnd`) i congestion (`cwnd`) control upravljaju throughputom svih konekcija.

Zaključak: brzina kojom TCP konekcija može slati podatke u modernim mrežama visokog bandwitha ograničena je prvenstveno roundtrip timeom između klijenta i servera. Bottleneck je latencija, a ne bandwith.

*Tuning servera:* Drži kernel up to date. Ozbiljno. Povećaj initial `cwnd`. Disablaj Slow-Start Restart. Provjeri imaš li Window Scaling. Eneblaj TCP Fast Open (ako klijent i server podržavaju).

*Tuning klijenta:* Šalji manje podataka. Ozbiljno. Koristi CDN za manju latenciju. Reusaj TCP konekcije.

## 3. Building Blocks of UDP

UDP je super lightweight (zovu ga i _null protocol_). Šalje se IP-om (koji je nepouzdan) i sve što dodaje userovom payloadu su 4 polja: source port (optional), destination port, length i checksum (optional, IP ima svoj).
* Ne garantira da će isporuku paketa.
* Ne garantira redoslijed isporuke.
* Ne postoji connection state.
* Nema congestion controle.

Za razliku od TCP-a čija se poruka može sastojati od više paketa, u UDP-u je 1 datagram = 1 poruka. (Inače, *datagram* je paket poslan kroz nepouzdan servis, bez notifikacije o uspjehu ili neuspjehu isporuke)

Zbog ograničenog prostora IPv4, sredinom 90ih privremeno su uvedeni *NAT* uređaji. Postavljeni na rub mreže, oni drže tablice koje mapiraju (local IP, port) -> (public IP, port), tako da klijenti iza različitih NATova mogu reusati iste privatne IP-je. Nažalost, privremeno rješenje postalo je stalan dio internetske infrastrukture.

TCP ima točno definirano otvaranje i zatvaranje konekcije, pa NAT zna kada dodati ili obrisati routing zapis u tablicu. Ali s UDPom, ne postoji konekcija. Nakon poslanog datagrama, odgovor možda dođe, a možda i ne. NAT ne zna koliko dugo da čuva routing zapis u svojoj tablici. Zato UDP routing zapisi imaju timeout, koji ovisi od NATa do NATa. Best practice za duge sessione preko UDPa je periodički slati keepalive pakete u oba smjera kako NATovi po putu ne bi obrisali taj mapping. (Ista timeout situacija se zna dogoditi i s TCP konekcijama, iako ne bi trebalo. Glupi NATovi.)

Još jedan problem s NATom je što pošiljatelj iz interne mreže ne zna svoj public IP. Ako ga šalje kao application data, poslat će svoj lokalni što primatelj neće moći koristiti. Još jedan problem je što paket koji dolazi iz javne mreže (npr. VOIP, file sharing i sl.) neće proći NAT jer još ne postoji routing zapis u tablici. On se stvara tek na slanje paketa iz interne mreže.

Te probleme rješava *STUN* protokol. STUN server je postavljen na javnoj mreži. Aplikacija prvo šalje request STUN serveru i on joj vraća njen (public IP, port) koji se vide iz javne mreže. Taj request u isto vrijeme i dodaje i routing zapis u NAT pa se mogu primati inbound requesti, a STUN definira i mehanizam za keepalive pingove kako se NAT tablica ne bi resetirala.

Kada 2 peera žele komunicirati komunicirati preko UDP-a, svaki pošalje STUN da sazna svoj javni IP i port, pošalju ih jedan drugome i preko njih razmjenjuju podatke.

Ali, avaj, ni to nije dovoljno za neke komplicirane NAT topologije. A i UDP zna biti blokiran na firewallovima. U slučaju kad STUN faila, koristi se *TURN*. Oba peera se spajaju na relay server i preko njega razmjenjuju podatke. To nije više peer-to-peer, ali je ok fallback (u stvarnom svijetu, 8% konekcija zahtjeva TURN).

*ICE* protokol je skup metoda za pronalaženje najefikasnijeg povezivanja dviju strana: bilo direktno, sa STUNom ili TURNom.

*TLDR:* Glavni feature UDP-a je da nema featurea, i zato je prikladan za izgradnju novog protokola nad njim. Ali nemoj to raditi pobogu, koristi nešto postojeće i zrelo (npr. WebRTC).

## 4. Transport Layer Security (TLS)

(TODO)

## 5. Introduction to Wireless Networks

Tipovi wireless mreža:
* Personal Area Network (domet osobe, zamjena za kablove periferije): Bluetooth, ZigBee, NFC
* Local Area Network (domet zgrade, produženje žičane mreže): 802.11 (WiFi)
* Metropolitan Area Network (unutar grada, bežična povezanost između mreža): 802.15 (WiMAX)
* Wide Area Network (worldwide, bežični pristup mreži): Cellular (UMTS, LTE, itd.)

Kapacitet wireless kanala ovisi o *širini pojasa* (bandwith, u Hz) i *omjeru signala i šuma* (S/N ratio, u Wattima).

Što je veća širina, veći je i kapacitet. Signali niske frekvencije putuju dalje i pokrivaju veća područja, ali zahtjevaju veće antene. Signali visoke frekvencije mogu prenijeti više podataka ali ne putuju daleko.

Što je šum jači, to signal mora biti jači da bi se prenijela informacija. Šum dolazi iz svih drugih wireless uređaja i nemoguće ga je izbjeći. Rješenje je ili povećati snagu odašiljanja, ili smanjiti udaljenost do primatelja.

Modulacija je prijevod digitalnih podataka u analogni signal. Odabir modulacijske abecede također utječe na kapacitet wireless kanala.

Brzina koju tehnologije reklamiraju (802.11g = 54Mbit/s, 802.11n = 600Mbit/s) je ona dobivena u idealnim uvjetima. U stvarnom svijetu, na throughput utječu:
* udaljenost,
* pozadinski šum,
* interferencija korisnika u istoj mreži (intra-cell),
* interferencija korisnika u susjednim mrežama (inter-cell),
* količina dostupne snage odašiljanja, te
* količina procesorske snage i odabrana modulacijska shema

## 6.
