# Database

TODO: paginacija http://www.xarg.org/2011/10/optimized-pagination-using-mysql/
Memcached, redis

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


## The Log
https://engineering.linkedin.com/distributed-systems/log-what-every-software-engineer-should-know-about-real-time-datas-unifying
*Log* je append-only lista. Novi unos ide na kraj liste, pa je sortirana po vremenu dodavanja. U njega zapisujemo evente (npr. klikove korisnika) ili modifikacije podataka (npr. `create` ili `update`).

Koristan je za integraciju različitih sustava za prikupljanje podataka:
* Svi podatci se drže u centralnom logu koji omogućava real-time subscription.
* Izvori podataka mogu biti aplikacije koje trackaju eventove (npr. klikove), ili db tablice koje primaju updateove. Oni očiste i pripreme podatke prije nego ih pošalju u log.
* Potrošači mogu biti cache sustavi, Hadoop, search sustavi itd. Svaki potrošač vodi svoj lokalni `time` dokad su pročitali log. Ako je potrebno, mogu zapisivati i stanje u lokalnoj memoriji ili tablici, a u slučaju pogreške, uvijek ga mogu iznova izgraditi iz loga.

Prednosti loga su:
* Potrošači ne moraju znati jesu li podatci došli iz RDBMS-a, key-value storea ili iz nekog real time sustava. Svi podatci dolaze jednakog formata, na jedno mjesto.
* Log se ponaša kao veliki buffer koji omogućava procesima da se restartaju ili failaju bez da uspore ostatak sustava.

Skaliranje i optimiziranje loga (ipak ne možemo čuvati svaki živi event zauvijek):
* Particioniranje recorda po nekom id-ju, batching čitanja i pisanja
* Za event data, čuvati samo window, stare evente brisati.
* Za keyed data, brisati recorde čije novije verzije imamo u logu.


## Indexes
Indeksi su korisni samo ako dohvaćaš mali broj rezultata; ili za izbjegavanje sortiranja.
Engine automatski neće koristiti indeks ako procjeni da je sekvencijalni scan brži (npr. ako ima previše rezultata)
Unique indeksi su uvijek potrebni, jer `validates_uniqueness_of` ne štiti od concurrent requestova.
Kada testiraš indekse, ne radi to na development mašini. Hoće li se indeks iskoristiti i kako ovisi o konfiguraciji db servera koji je vjerojatno drugačiji kod tebe.
Pronalaženje nekorištenih indeksa.


## Beginner Mistakes
* Ne spremaj image u bazu, stavi ih na S3.
* Paginacija s `OFFSET` i `LIMIT` nije skalabilna, i griješi ako novi itemi dođu u bazu.
* Koristi UUID umjesto INT za id da se lakše distribuiraš.
* Dodaj novi stupac bez defaultne vrijednosti, pa dodaj defaultnu vrijednost, i onda updateaj stare podatke.


## Remote Memcached?
Memcached radi na pretpostavci kako je brže dohvatiti podatak iz memorije nego obaviti skupu operaciju ili komunicirati s remote db-om koji drži podatke na disku. U tom slučaju je čak i network poziv, ako je istoj regiji (npr. na AWSu) brža opcija. Naravno, lokalni Memcached je najbrži, ali nije skalabilan.


## Data Analysis
Ako dobijaš 1TB podataka godišnje, i podatci su strukturirani, ne treba ti Hadoop/Hive/Presto. Savršeno dobro će poslužiti replika Postgres baze, BigQuery ili Redshift.

## Literatura
* https://sqlbolt.com/ - interaktivni tutorial, super za učenje
* http://use-the-index-luke.com - sve o indeksima
