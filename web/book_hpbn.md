https://hpbn.co

# Primer on Latency and Bandwidth
**Latency:** vrijeme od slanja paketa s izvora do primitka u odredištu (sekunde)
**Bandwith:** maksimalna propusnost logičnog ili fizičkog komunikacijskog kanala (bytovi po sekundi)

Na latenciju utječu:
  *propagation delay* (vrijeme putovanja paketa po žici, ovisi o udaljenosti),
  *transmission delay* (vrijeme slanja paketa s routera na žicu, ovisi o routeru i veličini paketa),
  *processing delay* (vrijeme čitanja headera paketa, provjera bit errora, i računanje odredišta paketa)
  *queueing delay* (vrijeme čekanja da router obradi paket - ovisi o routeru i prometu)

Brzina signala u fiber optičkom kablu je 1.5x sporija od brzine svjetlosti - oko 200.000.000 m/s.
To znači da je Round Trip Time od New Yorka do Sydneya u najboljem slučaju 160ms. U stvarnosti, s
delayovima, radi se o 200-300ms. Što je i dalje brzo, uzevši sve u obzir! Ali najsporiji dio je "last mile" -
spajanje klijenta na lokalni ISP node. Prije nego se paket uopće počne slati na odredište, proći će
desetke milisekunda.

Optical fiber je obična svjetlosna cijev (signal uđe, signal izađe), ali različite valne duljine omogućuju
istovremeno slanje preko 400 signala u jednom kanalu. Stoga jedan fiber link dostiže bandwith od preko 70 Tib/s!
Ali u odnosu na taj superbrzi "backbone" interneta, kapacitet na rubovima mreže mnogo je manji. Bandwith dostupan
korisniku jednak je najmanjem kapacitetu između njega i servera. Prosječan bandwith cijelog svijeta je 5 Mbps, što
nije loše, ali na knap ako želiš streamati HD video (50% svog prometa na netu je streamanje videa.)

Zahtjevi za bandwithom rastu, a tehnologija ne može zauvijek napredovati. Zato je potrebno raditi aplikacije koje će
na pametan način sakriti latenciju.
