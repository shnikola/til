# Performance

## Hijerarhija Latencija

* CPU cycle: `0.25 ns`
* L1 cache: `1 ns`
* L2 cache: `7 ns`
* RAM read: `60 ns`
* SSD read `20 μs` (`20,000 ns`)
* Round Trip unutar datacentra `500 μs` (`500,000 ns`)
* Hard Disk read `20 ms` (`20,000,000 ns`)
* Round Trip LA - Amsterdam `150ms` (`150,000,000 ns`)

## Primer on Latency and Bandwidth

**Latency:** vrijeme od slanja paketa s izvora do primitka u odredištu (sekunde)
**Bandwidth:** maksimalna propusnost logičnog ili fizičkog komunikacijskog kanala (bytovi po sekundi)

Na latenciju utječu:
* *propagation delay* (vrijeme putovanja paketa po žici, ovisi o udaljenosti),
* *transmission delay* (vrijeme slanja paketa s routera na žicu, ovisi o routeru i veličini paketa),
* *processing delay* (vrijeme čitanja headera paketa, provjera bit errora, i računanje odredišta paketa)
* *queueing delay* (vrijeme čekanja da router obradi paket - ovisi o routeru i prometu)

Brzina signala u fiber optičkom kablu je 1.5x sporija od brzine svjetlosti - oko 200.000.000 m/s. To znači da je *Round Trip Time* od New Yorka do Sydneya u idealnom slučaju `160ms`. Zbog raznih zastoja, stvarno vrijeme putovanja je 200-300ms. Što je i dalje brzo, uzevši u obzir da se prolazi pola planeta.

Najsporiji dio tog puta je "last mile" - spajanje klijenta na lokalni ISP node. Prije nego se paket uopće počne slati na odredište, proći će desetke milisekunda.

Optical fiber je obična svjetlosna cijev (signal uđe, signal izađe), ali različite valne duljine omogućuju istovremeno slanje preko 400 signala u jednom kanalu. Stoga jedan fiber link dostiže bandwidth od preko 70 Tib/s!

Ali u odnosu na taj superbrzi "backbone" interneta, kapacitet na rubovima mreže mnogo je manji. Bandwidth dostupan korisniku jednak je najmanjem kapacitetu između njega i servera. Prosječan bandwidth cijelog svijeta je 5 Mbps, što nije loše, ali na knap ako želiš streamati HD video (50% svog prometa na netu je streamanje videa.)

Zahtjevi za bandwidthom i brzinom rastu, a tehnologija ne može zauvijek napredovati. Zato je potrebno raditi aplikacije koje će na pametan način sakriti nedostatke tehnologije.

## TCP

Internet koristi dva protokola: IP (host-to-host routing and addressing) i TCP (apstrakcija pouzdane mreže na nepouzdanom kanalu). TCP od aplikacije skriva složenosti mreže: ponovno slanje izgubljenih paketa, očuvanje redoslijeda, izbjegavanje zakrčenja prometa, itd. TCP je optimiziran ne za brzu, nego za pouzdanu dostavu.

Svaka TCP konekcija počinje s 3-way handshakeom.
1) `SYN` Klijent odabire random broj (`x`) i šalje primaocu.
2) `SYN ACK` Primaoc povećava (`x+1`) i šalje ga sa svojim random (`y`)
3) `ACK` Klijent šalje (`x+1`) i (`y+1`), te može odmah početi slati aplikacijske podatke.

To znači da svaka nova TCP konekcija ima latenciju punog roundtripa prije nego se ikakvi podatci počnu slati. Postoji i TCP Fast Open način rada (još je u draftu) koji omogućuje slanje s prvim SYN paketom, ali s ograničenjima.

TCP osigurava pouzdanost tako da se odgovor na upit čeka određeno vrijeme (timeout), i ako odgovor dotad ne stigne upit se šalje ponovno. Ukoliko je vrijeme roundtripa duže od vremena timeouta, klijent će konstatno slati još paketa dodatno zakrčujući promet. Da bi se kontrolirala količina prometa koja se stvara u mreži, TCP ima ugrađene mehanizme: *flow control*, *congestion control* i *congestion avoidance*.

*Flow Control* je mehanizam koji sprječava da jedna strana preplavi drugu s podatcima koje ova ne stigne obraditi. Zato obje strane TCP konekcije šalju svoj *recieve window* (`rwnd`) koji je veličina slobodnog prostora u bufferu za obradu podataka. Obje strane počnu s veličinama iz sistemskih postavki, a ako jedna strana ne može pratiti tempo podataka, smanjuje svoj `rwnd`. Ako `rwnd` postane 0, toj se strani nikakvi podatci ne smiju slati dok ne pošalje veću vrijednost.

Po originalnoj specifikaciji maksimalan `rwnd` je 16KB, što često nije dovoljno za optimalne performanse. Zato je uveden *Window Scaling* koji dopušta `rwnd` do 1GB. Na većini platformi je eneblan po defaultu, ali moguće je da ga routeri i firewallovi po putu ne podržavaju i kvare performanse.

*Slow Start* je mehanizam koji sprječava da obje strane zaguše mrežu između sebe. Pošiljatelj kod sebe čuva *congestion window size* (`cwnd`) koji je količina podataka koju može slati bez da dođe do packet lossa. Cwnd počinje sporo - od 10 segmenata (14KB) - i za svaki uspješni ACK se povećava za 1 (tj. za svaki uspješan primitak, dva paketa se šalju) i tako raste eksponencijalno. Vrijeme da dođemo do punog kapaciteta tako ovisi o početnoj vrijednosti cwnda i vremenu roundtripa. Međutim, čim se detektira packet loss, `cwnd` se resetira (TCP throughput često ima "sawtooth" uzorak), i congestion avoidance algoritmi preuzimaju. TCP je dizajniran da koristi packet loss kao feedback. Osim toga, ako je konekcija idle duže vrijeme, Slow-Start Restart će resetirati `cwnd` (jer se stanje u mreži moglo promijeniti).

Posljedica slow starta je da, koliki god bandwidth imao, TCP konekcija neće odmah ostvariti puni kapacitet. To nije problem za duge streaming konekcije, ali HTTP konekcije koje su "short and bursty" često završe prije nego se dosegne maksimalni window size.

Zbog prethodna dva mehanizma, maksimalna količina neprimljenih podataka koji putuju žicom će biti `min(rwnd, cwnd)`. Neovisno o potencijalnom bandwidthu između dvije strane, s premalim window sizeom stvarni promet će biti mnogo manji. A razlog premalom window size može biti: šugavi server koji ima premali `rwnd`, veliki paket loss uzrokuje resetiranje `cwnd`a, i sl.

*Head-of-Line blocking* pojava je uzrokovana TCP-ovim očuvanjem redoslijeda paketa. Kad se jedan paket izgubi, svi primljeni nakon njega moraju čekati dok on ne bude primljen. Aplikacija to ne zna, i jednostavno vidi to kao delivery delay. Aplikacije kojima nije bitan redoslijed trebale bi umjesto TCP-a koristi UDP.

Sažetak:
* TCP 3-way handshake ima round trip latency.
* svaka konekcija ima slow start.
* flow (`rwnd`) i congestion (`cwnd`) control upravljaju throughputom svih konekcija.

Zaključak: brzina kojom TCP konekcija može slati podatke u modernim mrežama visokog bandwidtha ograničena je prvenstveno roundtrip timeom između klijenta i servera. Bottleneck je latencija, a ne bandwidth.

*Tuning servera:* Drži kernel up to date. Povećaj initial `cwnd`. Disablaj Slow-Start Restart. Provjeri imaš li Window Scaling. Eneblaj TCP Fast Open (ako klijent i server podržavaju).

*Tuning klijenta:* Šalji manje podataka. Koristi CDN za manju latenciju. Reusaj TCP konekcije.

## Transport Layer Security (TLS)

TLS zahtjeva TCP konekciju, što traje **1 roundtrip**. Nakon toga, TLS handshake zahtjeva **2 dodatna roundtripa**:
1) Klijent u plaintextu šalje verziju TLS-a i ciphersuite koji podržava, te ostale opcije.
2) Server odabire verziju TLS-a i chiphersuite, šalje odgovor klijentu zajedno sa svojim certifikatom.
3) Ako odobrava serverov certifikat, klijent započinje razmjenu ključa kojim će se simetrično kriptirati komunikacija.
4) Server provjerava integritet poruke i odgovara s kriptiranom `Finished` porukom.
5) Klijent dekriptira poruku, provjeri njen integritet i može početi slati aplikacijske podatke.

U slučaju da klijent po prvi put komunicira sa serverom, handshake se može skratiti na **1 roundtrip** koristeći *TLS False Start*. Klijent tada počinje slati enkriptirane aplikacijske podatke zajedno sa simetričnim ključem. Provjera integriteta podataka se obavlja kasnije u paraleli.

Ako je TLS session prethodno uspostavljen, handshake se može skratiti na **1 roundtrip** koristeći *TLS Session Resumption*. Klijent s prvim paketom šalje session ID kako bi dao do znanja da još pamti dogovorene ciphersuite postavke i ključeve (*session caching*). Ako server u svom cachu može pronađi postavke tog ID-a, nije potreba razmjena ključeva pa se handshake skraćuje za jedan roundtrip. Da server ne bi morao cachirati tisuće sessiona, klijent može koristiti session token u kojem su enkriptirane sve prethodno dogovorene postavke, pri čemu se na isti način uštedi roundtrip (*stateless resumption*).

*Tuning klijenta i servera*: optimiziraj reuse konekcija s keepalive i dovoljno velikim timeoutom; koristi najnoviju verziju TLS-a; koristi CDN da skratiš put paketima; koristi session resumption i false start.

## Wireless Networks

Tipovi wireless mreža:
* Personal Area Network (domet osobe, zamjena za kablove periferije): Bluetooth, ZigBee, NFC
* Local Area Network (domet zgrade, produženje žičane mreže): 802.11 (WiFi)
* Metropolitan Area Network (unutar grada, bežična povezanost između mreža): 802.15 (WiMAX)
* Wide Area Network (worldwide, bežični pristup mreži): Cellular (UMTS, LTE, itd.)

Kapacitet wireless kanala ovisi o *širini pojasa* (bandwidth, u Hz) i *omjeru signala i šuma* (S/N ratio, u Wattima).

Što je veća širina, veći je i kapacitet. Signali niske frekvencije putuju dalje i pokrivaju veća područja, ali zahtjevaju veće antene. Signali visoke frekvencije mogu prenijeti više podataka ali ne putuju daleko.

Što je šum jači, to signal mora biti jači da bi se prenijela informacija. Šum dolazi iz svih drugih wireless uređaja i nemoguće ga je izbjeći. Rješenje je ili povećati snagu odašiljanja, ili smanjiti udaljenost do primatelja.

Modulacija je prijevod digitalnih podataka u analogni signal. Odabir modulacijske abecede također utječe na kapacitet wireless kanala.

Brzina koju tehnologije reklamiraju (802.11g = 54Mbit/s, 802.11n = 600Mbit/s) je ostvariva u idealnim uvjetima. U stvarnom svijetu, na throughput utječu: udaljenost, pozadinski šum, interferencija korisnika u istoj mreži (intra-cell), interferencija korisnika u susjednim mrežama (inter-cell), količina dostupne snage odašiljanja, te količina procesorske snage i odabrana modulacijska shema.

### WiFi

WiFi je nastao kao adaptacija Ethernet standarda, ali je zbog svoje jednostavnosti, cijene hardwarea i korištenja nelicenciranog 2.4 GHz pojasa postao najčešće korišteni wireless protokol. U WiFi protokolu, kao i u Ethernetu, ne postoji centralni akter koji upravlja prometom, nego je svaki uređaj zadužen da pazi na performanse kanala. Ako jedna strana želi poslati podatke, provjerit će da li netko drugi odašilje u kanalu, i u tom slučaju pričekati. Ali čak i ako je kanal slobodan, moguće je da dvije strane počnu slati istovremeno pri čemu će se dogoditi kolizija. Zato se u slučaju javnih WiFi mreža s većim prometom događa velika degradacija performansi.

Čak i ako ti router omogućuje da ograničiš promet klijentima u vlastitoj mreži, kolizije će dolaziti iz drugih mreža. 2.4 Ghz pojas ima samo 3 kanala koja se ne preklapaju, a najčešće se oko tebe nalaze desetci različitih mreža. Ako ti susjed streama HD video na mreži koja dijeli kanal s tvojom, tvoj bandwidth će se prepoloviti. 5 Ghz pojas srećom omogućuje veći broj kanala koji se ne preklapaju, a i manje ga ljudi koristi.

WiFi je nudi nikakve garancije po pitanju bandwidtha i latencije jer previše ovisi o stanju u mreži. Treba imati na umu da je bolji od 3G i 4G mreža po pitanju throughputa i podatkovnih limita, pa se četo veliki downloadi ostavljaju dok se user ne spoji na WiFi. Zbog iznenadnih promjena u bandwidthu i latenciji treba omogućiti prilagođavanje streaminga, npr. smanjiti kvalitetu videa kad detektiraš da je brzina downloada pala.

### Mobile Networks

Reklamirane performanse mobilnih mreža, baš kao i wifi, vrijede samo za idealne uvjete koje se u stvarnosti rijetko susreće. Očekivane performanse su:
* 2G, GPRS (2.5G), EDGE (2.75G): 100-400 Kb/s, 300-1000ms latencije
* 3G (HSPA, LTE): 0.5-5 Mb/s, 100-500ms latencije
* 4G: 1-50 Mb/s, <300ms latencije

Ne postoji jedna 3G ili 4G tehnologija - oni zapravo prestavljaju skup preduvjeta koje tehnologija mora zadovoljavati. Primjerice, HSPA+ i LTE su vodeće 3G mreže. Imaj na umu da tehnologije iste generacije mogu imati potpuno različite performanse. Usto, prelazak na 4G zahtjeva velika ulaganja u infrastrukturu, što znači da će mreže starijih generacija postojati barem još desetak godina.

Zato je nemoguće razvijati aplikaciju za određenu generaciju mreže, ili očekivati određenu latenciju i bandwidth. Umjesto toga, razvijaj aplikaciju koja se može prilagoditi promijenjivom stanju mreže: latenciji, propusnosti, i uopće dostupnosti.

Različiti uređaji podržavaju različite mreže. Čak i ako uređaj podržava HSPA, postoji 36 različitih kategorija različitih performansi kojima može pripadati. Uređaji rijetko mogu priuštiti da istovremeno podržavaju i HPSA i LTE.

Bežične tehnologije zahtjevaju da radijski sklop bude stalno uključen, što bi trošilo jako puno baterije. Zbog toga se u WiFi protokolu koriste DTIM poruke koje najavljuju dolazak podataka pa se radio može staviti u sleep mode. U 3G i 4G mrežama pak postoji RRC uređaj koji upravlja svim mrežnim resursima te obavještava uređaje prije nego im pošalje podatke. Oba rješenja povećavaju latenciju mreže za 10-100 ms.

Na paket u mobilnoj mreži utječe niz različith latencija. Kada korisnik napravi request s mobilnog uređaja, prvo je potrebno sinkronizirati se s RRC uređajem na radio tornju. Prvi request nakon idle stanja stoga ima latenciju do 100 ms za 4G, pa i do nekoliko sekundi za starije generacije. Kada je komunikacija s RCC uspostavljena, svaki sljedeći paket ima latenciju do 5 ms do tornja, 30-100 ms putovanja u mobilnoj mreži do gatewaya javnog interneta, i varijabilnu latenciju u javnom internetu ovisno od lokacije servera.

U suprotnom smjeru djeluju iste latencije, uz dodatnu cijenu traženja točnog radio tornja korisnika koji je umeđuvremenu mogao promijeniti lokaciju, te autentifikacije korisnika kako bi mu se naplatio promet.

### Optimizing for Mobile Networks

Kako bi smanjio potrošnju baterije, pokušaj obaviti što veći prijenos podataka dok je radio uključen, a zatim ga smanjiti na minimum. Polling, pingovi i realtime analytics su iznimno skupi za bateriju i treba ih izbjegavati. Umjesto toga, koristi push i notification mehanizme, ali i oni ne smiju biti prečesti. Nekritične requeste odgodi dok radio ne postane aktivan. Agregacija requestova i pusha može učiniti veliku razliku.

Istovremeno, ovo znači da progresivno loadanje resursa na mobilnim mrežama može učiniti više štete nego koristi. Ako ti treba veliki audio ili video file, skini ga cijelog umjesto da ga streamaš. Dohvati aplikacijske sadržaje unaprijed, prije nego zatrebaju.

Imaj na umu da se connection keepalive odrađuje na razini mobilne mreže, a ne uređaja, tako da nema potrebe da držiš radio aktivnim kako bi održao TCP konekciju živom.

Očekuj latenciju novih konekcija 200-3500 ms za 3G i 100-600 ms za 4G. Veći dio dolazi od aktiviranja radija i RRC pregovaranja koje se događa svaki put kada je mobilni uređaj idle duže od nekoliko sekundi. To je cijena dužeg trajanja baterije. Zato kada korisnik obavi neku akciju, odmah prikaži rezultat u sučelju, a request obavi u backgroundu. To je jedini nači na koji mobilna aplikacija može pružiti instantni feedback.

Latencija i bandwith mobilnih mreža su stabilni u intervalima od maksimalno sekunde. Drugim riječima, očekuj da se latencija konstantno mijenja, a konekcija često gubi. Mreža određene generacije ne garantira i performanse te generacije - jedonstavno je previše promjenjivih parametara. Očekuj neprestane connection errore i koristi retry s backoff algoritmom. Upotrijebi AppCache i localStorage za offline mode.

U aplikacijama koje rade s velikom količinom podataka, koristi WiFi konekciju kad god je moguće. Promptaj usera da pređe na WiFi kako bi poboljšao performanse i smanjio cijenu prijenosa.

## Optimizing HTTP

Evergreen performance savjeti se zasnivaju na dvije stvari: smanji nepotrebnu latenciju i smanji broj bytova koji se šalju. Konkretnije:
* smanji broj DNS lookupa
* resusaj TCP konekcije
* smanji broj redirectova i requestova
* koristi CDN da smanjiš roundtrip time

Cachiraj resource na klijentu. Koristi `Cache-Control` za lokalni caching te `Last-Modified` i `ETag` za validaciju. Trebaju ti oboje, a ne samo jedan.

Komprimiraj resurse koje šalješ. Za HTML, CSS i JS koristi GZIP. Slike optimiziraj i smanji ih na veličinu u kojoj se prikazuju.

Pripazi na nepotrebne bytove u requestu. Izbaci headere koje ne trebaš, i pogotovo pazi na veličinu Cookija. U HTTP/1.X koristi "cookie-free" origin gdje ćeš hostati resurse za koje ti ne trebaju cookiji (npr. slike).

Paraleliziraj obradu requesta i responsea. Ako HTTP/2 nije opcija, koristi više TCP konekcija da omogućiš paralelne requeste. Pripazi na blokirajuće resource na klijentu (CSS i JS).

### HTTP/1.X

Najveće ograničenje HTTP/1.X je request queueing, odnosno limit od jednog istovremenog requesta po konekciji. Pošto multipleksiranje konekcija nije podržano, browseri otvaraju više paralelnih konekcija kako ne bi morali slijedno dohvaćati podatke. Ovo zauzima dodatne resurse na klijentima i serveru.

*HTTP pipelining* omogućuje da klijent pošalje više requesta odjednom, ne čekajući odgovor jednog da bi poslao drugi. Server isto tako može vraćati response u istom streamu, ali pošto HTTP 1.X ne podržava multipleksiranje u istoj konekciji, odgovori se moraju slati slijedno. Zato je moguće je da spori prvi dio odgovora blokira ostale odgovore (head-of-line blocking). Iz tog razloga, većina browsera ne koristi pipelining, ali i dalje se može pokazati koristan u slučaju da kontroliraš i server i klijente (npr. iTunes).

Većina modernih browsera dopušta 6 konekcija po hostu. Kako bi se povećao broj paralelnih requestova, koristi se *domain sharding*: registriraju se dodatne domene koje pokazuju na isti server. Ovo najčešće nije dobra ideja jer zahtjeva dodatni DNS lookup, a u slučaju HTTPS-a i TLS handshake. Često se zbog prevelikog broja slabo iskorištenih konekcija TCP ne podigne od slow starta, pa u najgorem slučaju rezultat bude sporiji. Budi oprezan ako ga misliš koristiti.

Kako bi se smanjio broj requestova, koristi se *bundling*. Skripte i stylesheetovi se spajaju u jedan file, a slike u spriteove, čime se eliminira overhead dohvaćanja pojedinačnih fileova. Nedostatak je što će se sada učitavati i resursi koji možda i nisu potrebni za prikaz trenutne stranice; update jednog filea će uzrokovati invalidaciju i ponovno skidanje svih; JS i CSS će se duže parsirati i izvršavati jer su veći, pa će se i duže čekati na first paint stranice. Testovi su pokazali da je 30–50 KB dobra kompresirana veličina za JS bundle.

Za smanjenje broja requestova koristi se *resource inlining* tako što se JS i CSS dodaju direktno u dokument, a slike zapišu pomoću data URI sheme. Korisno za male (1-2 KB) i unique assete. Ako se asset koristi na više mjesta, više ga se isplati poslati preko requesta koji se može cachirati.

### HTTP/2

Navedene HTTP/1.X optimizacije su zapravo workaroundi oko ograničenja protokola. HTTP/2 dopušta multipleksiranje u istoj konekciji, zbog čega requesti postaju jeftini. Zahvaljujući tome, većina se spomenutih optimizacija može ukloniti.

Domain sharding nije potreban. HTTP/2 je najoptimalniji kada ima jednu konekciju, odnosno jedan origin.

Bundling nije potreban. Multipleksiranje dopušta skidanje više resursa bez overheada, a nema nedostatke poput skidanja nepotrebnih podataka i invalidacije cijelog bundlea.

Umjesto inlininga koristi server push. Pošto podržava cachiranje, možeš ga koristiti za resurse svih veličina. Najbolji kandidati su blokirajući resursi (CSS i JS).

# Literatura

* https://hpbn.co - fantastična knjiga.
