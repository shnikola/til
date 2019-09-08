# DNS

Svaki request prema tvom serveru zahtjeva i pripadajući DNS resolving. Resolving se sastoji od niza poruka razmijenjenih između klijentovog browsera i raznih nameservera. Komprimitiranje bilo kojeg dijela lanca DNS komunikacije omogućuje napadaču da usmjeri promet s ciljanog servera na svoj. Tamo može izvršiti phishing napad, ili pratiti promet koji je namijenjen tvojem serveru.

## Domain Hijacking

Domain hijack daje napadaču pristup registraru i potpunu kontrolu nad tvojom domenom. Najčešći napadi iskorištajavaju slabosti u autentifikaciji registrara poput dopuštanja brute forcea passworda, ili lažnog predstavljanja koristeći WHOIS podatke koji su javno dostupni. Jednom kada dobije pristup, napadač može prebaciti domenu u drugi registrar gdje dobija potpunu kontrolu nad njom ili je može prodati trećoj strani.

Kako bi otežali domain hijack, ICANN zahtjeva period od 60 dana za zahtjev transfera domene u drugi registrar. Osim toga preporuča se korištenje 2FA u registraru i povezanim email računima.

## DNS Hijacking

DNS hijacking preotima DNS komunikaciju i usmjerava korisnika na napadačev nameserver, ili nameserver kojeg napadač kontrolira. Ovo se često izvodi pomoću malwarea koji mijenja korisnikove DNS postavke.

Jedna od metoda napada je *Drive-by Pharming* gdje se napadač ulogira u korisnikov router i izmjeni mu DNS konfiguraciju, usmjeravajući ga na svoj nameserver. Posebno osjetljivi na ovo su routeri nezaštićenih wireless mreža, gdje je router na standardnoj adresi `192.168.0.1` i zaštićen slabim passwordom. Druga metoda je navesti korisnika koji je već autentificiran na stranicu s napadačevom skriptom, jer velik broj routera ne koristi zaštitu protiv CSRF-a.

Osim napadača, ovu metodu znaju prakticirati ISP-ovi i DNS provideri kako bi usmjerili promet na vlastite servere gdje prikazuju reklame, skupljaju statistiku ili vrše cenzuru. Ovakvo ponašanje može ugroziti funkcionalnost i sigurnost mnogih protokola (npr. očekuje se da nepostojeća domain vraća NXDOMAIN error, a oni te šalju na svoju stranicu).

## DNS Spoofing

Kada klijent nema IP adresu, poslat će upit nameserveru. DNS paketi se šalju preko UDP-a, pa ih je lako krivotvoriti. Ako je žrtva na otvorenoj wireless mreži ili istom subnetu s napadačem, napadač će moći prepoznati kada je poslan DNS upit. U tom trenutku on šalje lažni odgovor, usmjeravajući ga na zli server.

Ukoliko napadač ne može vidjeti klijentov promet, može probati nasumično floodati klijenta odgovorima. *Kaminsky attack* je jedan takav napad gdje se iskorištava slabost 16-bitnih ID-jeva paketa. Noviji DNS klijenti koriste source port randomization kako bi se smanjili vjerojatnost nasumičnog pogotka, ali stariji klijenti i oni iza NAT-ova koji ne dopuštaju korištenje source porta su i dalje pod rizikom.

Stariji DNS serveri su osjetljivi na *DNS Cache Poisoning*. Napadač na može umetnuti lažni unos u njihov DNS Cache, a server će ga ubuduće servirati svima koji zatraže tu domenu. Većina DNS softwarea danas je otporna na ovaj napad.

Najbolja obrana od spoofinga je korištenje DNSSEC protokola. On potpisuje DNS odgovore public keyem TLD providera, kako bi klijent mogao provjeriti autentičnost poruka. Korištenje SSL-a će također spriječiti spoofing, jer napadač neće imati private key domene koju glumi kako bi dovršio SSL handshake.

## DNS Zone Transfer Attack

DNS zone transfer se obično koristi da DNS master nameserver pošalje kopiju svog zonefilea replikama. Napadač može glumiti repliku i zatražiti zonefile kako bi dobio više informacija i mogao lakše spoofati odgovore.

Kako bi se zaštitio, disablaj zone transfer ili ga omogući samo whitelistanim IP-ima.

## DNS Rebinding

Zli server ima registriranu domenu s jako kratkim TTLom, koja se zbog toga ne cacheira. Kada se korisnik prvi put spoji na taj server, on pomoću `iptables` zamjeni svoju IP adresu s nekom zlobnom, i svi idući requestovi se šalju na nju, a browser misli da i dalje vrijedi same-origin policy.

Na ovaj način napadač može pristupi podatcima s privatnih IP adresa koji su dostupni samo korisniku. Također se može koristiti za spamming i DDOS napade od strane nesumnjajućih korisnika.

Potencijalna obrana od ovog je da browseri uvedu *DNS pinning*, tj. da uvijek koriste IP koji su dobili u prvom requestu.

# Stories

## Taking Control of All .io Domains With a Targeted Registration

https://thehackerblog.com/the-io-error-taking-control-of-all-io-domains-with-a-targeted-registration/

Pronašao je da su 4 od 7 navedenih nameservera top level domene `.io` neregistrirani i čak dostupni za kupnju. Kupio ih je i mogao je bez problema servirati lažne DNS rezultate.

## Hacking developers

http://bouk.co/blog/hacking-developers

Kreativna upotreba DNS Rebindinga je zamjeniti serverovu IP adresu s *korisnikovom privatnom*, npr. `127.0.0.1` ili `192.168.0.1`. Nakon toga napadač može unutar same-origin policija slati requestove na korisnikove lokalne servise koji se vrta ne nekom portu, npr. `Redis` ili `ElasticSearch`.

