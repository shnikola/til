# Post Mortem

## Github Mysql Outage (2012)

https://github.com/blog/1261-github-availability-this-week

Mysql cluster ima nekoliko nodeova, od kojih je jedan read-write master, a ostali replike. Uloga mastera se može prebaciti na bilo koji node pomoću Percona Replication Managera (dio Pacemaker i Heartbeat stacka).

Prilikom migracije, master je bio pod opterećenjem, pa je health check failao i trigerirao automataski failover, što je samo pogoršalo stvar. Disablali su failover, ali cluster je već ispao iz synca.

Da bi ga syncali nazad, opet su eneblali Pacemaker, koji je segfaultao. Cluster se particionirao i sve je ošlo u kurac.

Redis i Mysql su ispali iz synca, i useri su imali pristup tuđim repozitorijima.

Uz sve to, status page im je imao ogroman promet, pa su upogonili 90 Heroku dynoa, što je skršilo njegovu bazu.

**Pouka:** Automatski failover za baze češće prouzročuje probleme nego što ih rješava. Radije omogući manualni failover od strane developera. Tih par minuta downtimea godišnje si možeš priuštiti.

**Pouka:** Za status page koristi generirane statične stranice, pobogu.

## Apple's Livestream Fiasco (2014)

http://perf.fail/post/97144331419/learning-from-apples-livestream-perf-fiasco

Stranica dohvaća feed.json koji nije GZIPan (90% uštede). Iz feeda se dohvaća svih 500 imagea odmah, umjesto on-demand. Svi imagei imaju max-age < 10s, iako se neće mijenjati, pa CDN ne pomaže puno. Svakih 10 sekundi se dohvaća feed.json iznova, umjesto da postoji mehanizam za diff ili push.
