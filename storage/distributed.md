# Distributed Systems

Distribuirani sustavi sastoje se od više nodeova koji komuniciraju preko mreže. Osnovni problem je propagiranje podataka iz jednog nodea u ostale.

CAP teorem kaže da distribuirani sustav može imati samo 2 od 3 sljedeća svojstva:
* Consistency: ako pišem u jedan node, read na drugom nodeu će vratiti te podatke.
* Availability: ako komuniciram s jednim nodeom, odgovorit će, tj. neće delayati zauvijek.
* Partition-tolerance: ako je mreža particionirana (poruke ne mogu putovati iz jednog nodea u drugi), i dalje će vrijediti sve što sustav garantira, tj. consistency i availability.

U praksi biraš između A i C, odnosno što ćeš izgubiti kad se dogodi particija:
* Availability će dopustiti da se čita prije nego update propagira na sve nodeove, pa se u slučaju particije gubi Consistency.
* Consistency će zabraniti čitanje prije nego se update propagira, pa se u slučaju particije gubi Availability.

Mreža je u osnovi asinkrona, što znači da se paketi u mreži mogu izgubiti, duplicirati, kasniti ili promijeniti redoslijed. TCP stvara pouzdani kanal komunikacije koji garantira da će paketi stići bez gubitaka, duplikacije i u ispravnom redoslijedu. Međutim, problemi u mreži i dalje stvaraju delay zbog kojeg bi se distribuirani sustav mogao lockati zauvijek, pa se uvodi timeout koji prekida konekciju ako očekivani paket ne stigne nakon određenog vremena.

## Redis

Redis je single-server single-thread aplikacija, pa izbjegava probleme distribuiranog sustava. U slučaju da Redis server ima više replika za čitanje, jedan server je master u kojeg se piše, i koji šalje podatke ostalima iz kojih se samo čita. Particija mreže može uzrokovati da istovremeno postoje 2 mastera, a nakon oporavka jedan od njih će odbaciti sve podatke koje je primio, što može dovesti do znatnog gubitka.

Redis je cache i koristi ga kao takvog. Nemoj ga koristiti kao queue ili lock.

## ElasticSearch

ElasticSearch je distribuirani search engine. Java library Lucene je zadužen za disk storage, indexing i search, a ElasticSearch je zadužen za API i distribuciju.

ES se skalira na dva načina: sharding (podatci su podijeljeni na mnogo chunkova, svaki chunk se daje različitom nodeu) i replication (svaki chunk je istovremeno na nekoliko nodeova, ako jedan faila drugi mogu preuzeti njegove requeste).

Prilikom bilo kakve particije mreže ES može gubiti writeove. Cluster se usto može deadlockati, pri čemu administrator mora intervenirati.

ES nikako nemoj koristiti kao primarni data store. Umjesto toga, drži podatke u sigurnijem dbu, a od tamo postepeno šalji podatke u ES na indeksiranje. Na taj način se u slučaju gubitka podataka možeš automatski oporaviti.

## Clustering vs Sharding (TODO)

*Clustering*:
* podatci se distribuiraju automatski,
* prebacuju se iz jednog clustera u drugi,
* rebalansiraš podatke za distribuirati load,
* nodeovi komuniciraju međusobno
* kompleksni dogovori između nodeova, upgrade je jako težak, data corruption se proširi na sve nodove, blargh

*Sharding*:
+ podatke distribuiraš ručno,
+ ostaju na jednom mjestu,
+ splitaš podatke da redistribuiraš load,
+ nodeovi nisu svjesni jedan drugoga

Sharding:
+ ne možeš koristiti joinove ni transakcije
+ ručno provjeravati unique constrainte i raditi agregacije


# Literature

* https://jepsen.io/analyses
* https://aphyr.com/posts/288-the-network-is-reliable - primjeri particija mreže u stvarnom svijetu.
