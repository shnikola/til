# DNS

## Tko je tko
**Registrar:** onaj kod kojeg si kupio domenu. Drži podatke o lokacijama name servera.
**DNS hosting provider:** hosta name servere. Može biti u sklopu registrara, server hostinga ili dedicated kompanija.
**Name server:** hosta ili cacheira prijevode domena u IP adrese. Poznat kao i DNS server.
**DNS zone:** jedna ili više domena kojim upravlja jedan manager. Konfiguracija se drži u Zone Fileu.

## Zone File
Sadrži mappinge domena u IP adrese ili druge resource. Svaki mapping ima `name` (što se mapira),
`record class` (IN za Internet), `record type`, `record data` i `ttl` (vrijeme trajanja cachea)

**Record types:**
* `A`: usmjerava (sub)domenu IPv4 adresu. Host može biti `@` (root), `pitalica` (subdomena), ili `*` (fallback)
* `AAAA`: isto kao i `A`, samo za IPv6
* `CNAME`: alias na `A` record (`puzzle` -> `pitalica`) ili drugu domenu (`blog` -> `journal.srijeda.com`)
* `MX`: usmjerava na `A` record ili drugu domenu kojoj će proslijediti primljeni email. `@` host za glavnu domenu.
* `TXT`: drži tesktualne podatke, uglavnom za verifikaciju
* `NS`: nameserveri authoritive za domenu. Host može biti `@` (za trenutnu zonu) ili `pitalica` (delegiranje subodmene u drugu zonu)

## Command line
`nslookup utorkom.com` - name server lookup, najstariji. Vraća IP i DNS server kojeg je pitao.
`host utorkom.com` - dobar za jednotavni query. Ima i reverse lookup: `host 213.11.172.228`. Vraća više informacija s `-a`.
`dig utorkom.com` - najviše opcija. `dig NS utorkom.com` vraća nameservere. `dig utokom.com any` za cijeli zone file. `dig @ns1.example.com utorkom.com` traži podatke od zadanog nameservera.
`whois utorkom.com` - dohvaća podatke o registriranim korisnicima Internet resourca (npr. domene ili IP bloka)


## What really happens when you navigate to a URL
http://igoro.com/archive/what-really-happens-when-you-navigate-to-a-url/
1. Unesemo url wikipedia.org/login u browser
2. Browser traži IP adresu za "wikipedia.org". Browser cache -> OS cache -> Router cache
   -> ISP DNS cache -> Root nameserver -> .org nameserver -> wikipedia.org nameserver
3. Browser šalje request na IP: URL, tko sam (User-Agent), što želim (Accept, Accept-Encoding),
   da održi TCP vezu (Connection), i stanje klijenta (Cookies)
4. Server odgovara s `301 Moved Permanently`, `Location: https://www.wikipedia.org/`
5. Browser šalje isti request na novu lokaciju.
6. Server generira HTML i vrati ga nazad.
7. Browser počinje renderirati HTML, radeći dodatne requestove pri nailasku na
   objekte koje treba dohvatiti (images, css, js). Svaki od tih dodatnih requestova
   prolazi kroz ovaj isti proces (uključujući i DNS). Ali statični fileovi se često
   serviraju iz browser cachea, koristeći Expires (datum) i ETag (verzija) headere.

Kako iza domene može stajati više IP-jeva:
 - Round-robin DNS - vraća jedan od nekoliko IP-jeva (nije preporučen http://serverfault.com/questions/60553/why-is-dns-failover-not-recommended)
 - GeoDNS - vraća IP ovisno o korisnikovoj lokaciji
 - Load Balancer - stroj koji prosljeđuje request ostalim serverima (npr. HAProxy)
 - Anycast je routing metoda u kojoj je IP mapiran na više fizičkih servera, ali se ne koristi s TCP-om.
