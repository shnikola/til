# Transport Layer

## UDP

UDP je super lightweight (zovu ga i _null protocol_). Šalje se IP-om (koji je nepouzdan) i sve što dodaje userovom payloadu su 4 polja: source port (optional), destination port, length i checksum (optional, IP ima svoj).

* Ne garantira isporuku paketa.
* Ne garantira redoslijed isporuke.
* Ne postoji connection state.
* Nema congestion control.

Za razliku od TCP-a čija se poruka može sastojati od više paketa, u UDP-u je 1 datagram = 1 poruka. (Inače, *datagram* je naziv za paket poslan kroz nepouzdan servis, bez notifikacije o uspjehu ili neuspjehu isporuke)

Glavni feature UDP-a je da nema featurea, i zato je prikladan za izgradnju novog protokola nad njim. Ali nemoj to raditi pobogu, koristi nešto postojeće i zrelo (npr. WebRTC).

