# Performance

## O Performancu

https://news.ycombinator.com/item?id=2249789

O skaliranju učiš najbolje susrećući se s vlastitim problemima, a ne čitajući tuđe priče (cargo cult). Npr:
* Kad ti jedan server ne bude mogao podnijeti load, proučit ćeš HAProxy.
* Kad site bude imao puno više db readova od writeova, prouči master-slave replication.
* Kad ti baza postane prespora, proči Memcached.
* Kad ne budeš htio da requetovi dolaze do app servera, prouči Varnish.

Dotad, neka stvari budu jednostavne koliko god mogu biti. Preuranjena optimizacija vrijedi i za web stack. Najbitnije je prepoznati problem (profiling, monitoring), a tek onda odlučiti koji alat koristiti za njegovo rješenje.

## Osnovne latencije

https://people.eecs.berkeley.edu/~rcs/research/interactive_latency.html
http://computers-are-fast.github.io/

* CPU cycle: `0.25 ns`
* L1 cache: `1 ns`
* L2 cache: `7 ns`
* RAM read: `60 ns`
* SSD read `20 μs` (`20,000 ns`)
* Round Trip unutar datacentra `500 μs` (`500,000 ns`)
* Hard Disk read `20 ms` (`20,000,000 ns`)
* Round Trip LA - Amsterdam `150ms` (`150,000,000 ns`)
