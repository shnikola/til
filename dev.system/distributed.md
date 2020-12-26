# Distributed Systems

Distribuirani sustavi sastoje se od više nodeova koji komuniciraju preko mreže. Osnovni problem je propagiranje podataka iz jednog nodea u ostale.

**CAP teorem** kaže da distribuirani sustav može imati samo 2 od 3 sljedeća svojstva:
* **Consistency**: ako pišem u jedan node, read na drugom nodeu će vratiti te podatke.
* **Availability**: ako komuniciram s jednim nodeom, odgovorit će, tj. neće delayati zauvijek.
* **Partition-tolerance**: ako je mreža particionirana (poruke ne mogu putovati iz jednog nodea u drugi), sustav će i dalje raditi kao da nema particije.

U praksi ne možeš izbjeći da se dogodi particija mreže, pa biraš između A i C:

**CP** sustav ne garantira Availability, tj. može timeoutati umjesto da vrati rezultat. Korisno za sustave koji moraju garantirati atomarni read i write.

**AP** sustav ne garantira Consistency, tj. vraća posljednju lokalnu vrijednost, ali ne nužno i globalnu. Korisno za sustave koji dopuštaju *eventual consistency* i koji moraju nastaviti raditi unatoč errorima.

## Consistency Patterns

*Weak Consistency* nakon što se podatak zapiše, čitanje će ga možda vidjeti možda neće. Koriste ga Memcached i real time streamovi poput VoiPa, video chata, ili multiplayer igara.

*Eventual Consistency* nakon što se podatak zapiše, čitanje će ga nakon nekog vremena vidjeti (najčešće se radi o milisekundama). Podatci se repliciraju asinkrono. Koriste ga DNS i email.

*Strong Consistency* nakon što se podatak zapiše, čitanje će ga odmah vidjeti. Podatci se repliciraju sinkrono. Koriste ga file sistemi i RDBMS baze.

## Redis

Redis je single-server single-thread aplikacija, pa izbjegava probleme distribuiranog sustava. U slučaju da Redis server ima više replika za čitanje, jedan server je master u kojeg se piše, i koji šalje podatke ostalima iz kojih se samo čita. Particija mreže može uzrokovati da istovremeno postoje 2 mastera, a nakon oporavka jedan od njih će odbaciti sve podatke koje je primio, što može dovesti do znatnog gubitka.

Redis je cache i koristi ga kao takvog. Nemoj ga koristiti kao queue ili lock.

## ElasticSearch

ElasticSearch je distribuirani search engine. Java library Lucene je zadužen za disk storage, indexing i search, a ElasticSearch je zadužen za API i distribuciju.

ES se skalira na dva načina: sharding (podatci su podijeljeni na mnogo chunkova, svaki chunk se daje različitom nodeu) i replication (svaki chunk je istovremeno na nekoliko nodeova, ako jedan faila drugi mogu preuzeti njegove requeste).

Prilikom bilo kakve particije mreže ES može gubiti writeove. Cluster se usto može deadlockati, pri čemu administrator mora intervenirati.

ES nikako nemoj koristiti kao primarni data store. Umjesto toga, drži podatke u sigurnijem dbu, a od tamo postepeno šalji podatke u ES na indeksiranje. Na taj način se u slučaju gubitka podataka možeš automatski oporaviti.

# Literature

* https://jepsen.io/analyses
* https://aphyr.com/posts/288-the-network-is-reliable - primjeri particija mreže u stvarnom svijetu.
