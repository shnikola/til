# DNS

*Registrar* onaj kod kojeg si kupio domenu. Drži podatke o lokacijama name servera.

*DNS hosting provider* hosta name servere. Može biti u sklopu registrara, server hostinga ili dedicated kompanija.

*Name server* hosta ili cacheira prijevode domena u IP adrese. Poznat kao i DNS server.

*DNS zone* jedna ili više domena kojim upravlja jedan manager. Konfiguracija se drži u Zone Fileu.

*Zone File* sadrži mappinge domena u IP adrese ili druge resource. Svaki mapping ima `name` (što se mapira), `record class` (IN za Internet), `record type`, `record data` i `ttl` (vrijeme trajanja cachea)

## Vrste recorda

**A** usmjerava (sub)domenu IPv4 adresu. Host može biti `@` (root), `pitalica` (subdomena), ili `*` (fallback). **AAAA** je isto kao i A, samo za IPv6

**CNAME** je alias na A record (`puzzle` -> `pitalica`) ili drugu domenu (`blog` -> `journal.srijeda.com`)

**MX** usmjerava na A record ili drugu domenu kojoj će proslijediti primljeni email. `@` host za glavnu domenu.

**TXT** drži tekstualne podatke, uglavnom za verifikaciju

**NS** pokazuje na autorativne nameservere za domenu. Host može biti `@` (za trenutnu zonu) ili `pitalica` (delegiranje subodmene u drugu zonu)

## DNS Resolving

1. Browser traži IP adresu za `wikipedia.org`.
2. Provjerava u browser cacheu; ako nema:
3. Provjerava u OS cacheu; ako nema:
4. Provjerava s resolverom (ISP server); ako on nema u cacheu:
5. Resolver pita root server. Postoji 13 root name IP-jeva (`a.root-servers.net`, `b.root-servers.net`...) na cijelom svijetu, rasprostranjenih po svakom kontinentu. Ako root server ne zna:
6. Resolver se šalje na .ORG Top-level domain server. Resolver cachira adresu TLD servera da ne mora idući put pitati root server. Ako TLD server ne zna:
7. Resolver se šalje na autorativne nameservere (npr. `ns1.wikipedia.org`). Registrar je zadužen da pri registriranju domene pošalje svoje nameservere TLD registru. `WHOIS` query vraća autorativne nameservere za domenu. Oni će uvijek imati IP adresu domene.
8. Resolver vraća OS-u IP adresu koji je cachira i prosljeđuje browseru.

## Literatura

https://howdns.works/
