# System Design

## Scalability

Servis je skalabilan ako dodavanje novih resursa povećava njegove performanse.

Vertikalno skaliranje: pojačavanje snage servera. Ograničeno maksimalnom konfiguracijom.

Horizontalno skaliranje: dodavanje još servera. Zahtjeva load balancer. Svaki server mora imati isti codebase i ne spremati user-related podatke (sessione ili uploadane fileove) lokalno na disku ili u memoriji.

### Load Balancing

*Load balancing* se može napraviti samo koristeći DNS kao round robin. Problem je što se DNS cachira i ne možemo uniformno podijeliti requeste. Bolje rješenje je imati poseban load balancer, softverski (ELB, HAProxy, LVS) ili hardverski (Barracuda, Cisco, Citrix...) koji su ludo skupi.

Load balanceri mogu podržavati *SSL Termination* gdje on dekriptira requeste i enkriptira response. Tako se serveri ne moraju trošiti na SSL enkripciju niti imati instalirane SSL certifikate.

Load balancing mogu preusmjeravati promet prema različitim metodama: random, round-robin (po redu), least loaded (prema najmanje opterećenom serveru), layer 4 (koristeći IP adrese i portove, kao NAT), layer 7 (koristeći headere, cookije ili vrstu podatka, npr. request za video šalje na video server).

Load balancer je single point of failure, pa je korisno imati ih više. U Active/Active modu svi primaju konekcije, a u Active/Passive modu jedan prima konekcije, a drugi postaje aktivan ako prvi umre.

### DB Scalabiliy

Nakon skaliranja servera, single point of failure postaje baza podataka. Baza se može skalirati na više načina.

*DB replication* je stvaranje više baza u kojima se drže isti podatci, pa se upiti na bazi mogu distribuirati.

Primary/Replica (master/slave) replikacija ima jednog Primarija u kojeg se prosljeđuju svi write queriji, a promjene se automatski prosljeđuju na replike koje primaju read querije. Korisno ako imaš puno read querija u odnosu na writeove. Problem je što ako primary krepa, ne možeš obavljati write dok ne propagiraš jednog slavea u master.

Primary/Primary replikacija ima više primarija sa svojim replikama što omogućuje da primary krepa, a da se ne izgubi mogućnost pisanja.

*High Availabilty* znači imati load balancer ispred repliciranih bazi. Ako jedna baza umre, druga preuzima.

*DB Partitioning* je dijeljenje podataka u različite baze, kako bi se podijelio promet koji dolazi na svaku.

*Clustering* podrazumjeva da se podatci automatski distribuiraju, a clusteri međusobno komuniciraju i balansiraju. Nedostatak što je takva komunikacija kompleksna i korupcija podataka se lako proširi kad dođe do particije.

*Sharding* podrazumjeva da podatke distribuiraš ručno po nekom pravilu (npr. parnosti id-ja). Podatci uvijek ostaju u istoj bazi.

*Federation* podrazumjeva dijeljenje podataka po funkciji, npr. u Forums, Users i Products bazu.

Nedostatak partitioninga je što ne možeš koristiti joinove ni transakcije između particija. Zato se često federirane i particionirane baze denormaliziraju. Također, moraš ručno provjeravati unique constrainte i raditi agregacije.

## Raid

Raid 0 koristi *striping*, odnosno spajanje više diskova u jedan zajednički storage. Zapisivanje filea na različite diskove omogućava paralelno čitanje i pisanje. Nema redundancije, pa ako jedan disk faila, faila i cijeli Raid.

Raid 1 koristi *mirroring*, odnosno zapisivanje istog filea na više diskova. To omogućuje čitanje s bilo kojeg diska što ga čini bržim, ali pisanje je sporije jer zahtjeva da se zapiše na svakom mjestu. Redundancija omogućava da Raid radi dok god bar jedan disk funkcionira.

Raid 5 koristi *parity*. Podržava više diskova od kojih se samo jedan koristi za redundaciju. Ako jedan disk umre, podatci se mogu rekonstruirati pomoću ostalih.

Raid 6 je sličan kao Rails 5, ali dopušta da dva diska umru.

Raid 10 kombinira Raid 0 i Raid 1, tako da imaš i striping i redundanciju. Zahtjeva barem 4 diska.

# Literatura

* https://github.com/donnemartin/system-design-primer
* https://www.youtube.com/watch?v=-W9F__D3oY4
