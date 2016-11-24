# Performance

## O Performancu
https://news.ycombinator.com/item?id=2249789
* O skaliranju učiš najbolje susrećući se s vlastitim problemima, a ne čitajući tuđe priče (cargo cult).
  * Kad ti jedan server ne bude mogao podnijeti load, proučit ćeš HAProxy.
  * Kad site bude imao puno više db readova od writeova, prouči master-slave replication.
  * Kad ti baza postane prespora, proči Memcached.
  * Kad ne budeš htio da requetovi dolaze do app servera, prouči Varnish.
* Dotad, neka stvari budu jednostavne koliko god mogu biti. Preuranjena optimizacija vrijedi i za web stack.
* Najbitnije je prepoznati problem (profiling, monitoring), a tek onda odlučiti koji alat koristiti za njegovo rješenje.


## Osnovne latencije
https://people.eecs.berkeley.edu/~rcs/research/interactive_latency.html
* CPU cycle: `0.25 ns`
* L1 cache: `1 ns`
* L2 cache: `7 ns`
* RAM read: `60 ns`
* SSD read `20 μs` (`20,000 ns`)
* Round Trip unutar datacentra `500 μs` (`500,000 ns`)
* Hard Disk read `20 ms` (`20,000,000 ns`)
* Round Trip LA - Amsterdam `150ms` (`150,000,000 ns`)


# Stories

## 30K Page Views for $0.21: A Serverless Story
https://fmlnerd.com/2016/08/16/30k-page-views-for-0-21-a-serverless-story
Site je kalkulator za predviđenu zaradu filmova. Site je statičan i generira se na AWS-u.
* CloudWatch cron job nekoliko puta dnevno trigerira dohvaćanje novih podataka. *Free tier*
* Lambda na trigger dohvaća config s S3 i scrapea podatke, spremajući ih u JSON na S3. *Free tier <1M req/month*
* Iduća Lambda na event kreiranja JSONa dohvaća JSON i stvara od njega JS file i HTML.
* Sav trošak se svodi na S3 (oko *20 centi* mjesečno)


## Reddit: 270M Page Views A Month (2010)
http://highscalability.com/blog/2010/5/17/7-lessons-learned-while-building-reddit-to-270-million-page.html
* `Memcachiraju` sve živo: DB data, session data, renderirani html, memoizirane metode, *rate limiting*, locking, *change password linkove* (ne drže ih u bazi).
* Sve računaju unaprijed i cacheiraju, ništa on demand, npr. sortiranja hot, new, top, old su sva unaprijed izračunata.
* Kada user upvotea, napravi se minimum odmah (u bazu se doda upvote), a ostatak procesiranja se stavi u queue (sortiranje linkova, prepoznavanje spama, računanje nagradi...). Nema potrebe da user čeka za išta od toga. Za queuing koriste `RabbitMQ`.


## Instagram (2012)
http://instagram-engineering.tumblr.com/post/13649370142/what-powers-instagram-hundreds-of-instances
* Amazon Elastic Load Balancer s 3 nginx instance iza njega. SSL završava na load balanceru kako bi se smanjio CPU load na nginx. Amazon Route53 za DNS.
* Ubuntu Linux na Amazon EC2. Django na Amazon High-CPU Extra-Large (25 strojeva).
* Postgres za data storage. Disk je prespor, pa drže što više podataka u memoriji. Podatci za feedove u Redis. Memcached za cachiranje.
* S3 i CloudFront za fotke.
* Pingdom za monitoring servisa, PagerDuty za notifikacije i incidente.


## Wordpress: Load Balancing 70k req/s (2012)
http://highscalability.com/blog/2012/9/26/wordpresscom-serves-70000-reqsec-and-over-15-gbitsec-of-traf.html
* nginx kao load balancer. Zbog: jednostavnosti, mogućnosti rekonfiguracije bez gubitka requestova, direktnog serviranja statičnih podataka, minimalni CPU i memory zahtjevi, sposoban handlati 10,000 req/s po serveru.
* 36 load balancing strojeva: Dual Xeon 5620 4 core CPUs with hyper-threading, 8-12GB of RAM, running Debian Linux 6.0
* A beckend je bjerojatne tisuće dobro cachiranih servera.


## League of Legends: Chat for 70M Players (2014)
http://highscalability.com/blog/2014/10/13/how-league-of-legends-scaled-chat-to-70-million-players-it-t.html
* Iskušano riješenje: XMPP (messaging protocol), EJabberd (Erlang server) i Riak (distributed, fault-tolerant, masterless key-value store), dobra skalabilnost s defaultnom konfiguracijom.
* Share nothing approach: omogućuje linearno horizontalno skaliranje.
* Let it crash: ako se nešto skrši, ne pokušavaj recover, nego restartiraj iz zadnjeg poznatog stanja.
* Make it work, make it right, make it fast: iskoristili gotovu stvar, i onda je malo po malo prilagođavali.


## What I Wish I Had Known Before Scaling Uber to 1000 Services
https://www.youtube.com/watch?v=kb-m2fasdDY
* Uber ima 1000 mikroservisa, jer ima 2000 programera i to je najjednostavniji način da svi budu neovisni
* Problem je što onda svi radije samo gurnu novi mikroservis umjesto da poprave nešto što ne radi
* Svatko radi u svom jeziku pa je teško sharati i reusati kod, i kompanija se fragmentira
* Na tolikom scaleu JSON postaje problem (što ako u trećem koraku netko razlikuje null od praznog stringa), i puno bi značilo da se koristi typed language. Serveri nisu browseri, i lakše komuniciraju kao "poziv funkcije" nego kao "HTTP request"
* Company > Team > Self - politika je sve što se radi protiv te hijerarhije.
