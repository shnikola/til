# Security
TODO:
dns hijacking
dns spoofing
uploads

## Security vs Integrity

Security znači da nitko ne može doći do tvojih podataka.
Integrity znači da nitko ne može uništiti tvoje podatke.

## DNS Rebinding

http://bouk.co/blog/hacking-developers

Zli server ima registriranu domenu s jako kratkim TTLom, koja se zbog toga ne cacheira. Kada se korisnik prvi put spoji na taj server, on pomoću `iptables` zamjeni svoju IP adresu s nekom zlobnom, i svi idući requestovi se šalju na nju, a browser misli da i dalje vrijedi same-origin policy.

Kreativni napad je zamjeniti serverovu IP adresu s *korisnikovom privatnom*, npr. `127.0.0.1` ili `192.168.0.1`. Nakon toga napadač može slati requestove na korisnikove lokalne servise koji se vrta ne nekom portu, npr. `Redis` ili `ElasticSearch`.

Potencijalna obrana od ovog je da browseri uvedu *DNS pinning*, tj. da uvijek koriste IP koji su dobili u prvom requestu.

## SQL injection metode

https://www.troyhunt.com/everything-you-wanted-to-know-about-sql

Tehnike SQL injectiona:
* `WHERE id = 1 OR 1=1` dohvaća sve podatke.
* `WHERE id = 1 UNION ALL SELECT password FROM user` dohvaća iz druge tablice.
* `WHERE id = 1 and select top 1 substring(name, 1, 1) from sysobjects ... = 'a'` pogađa prvo slovo imena tablica
* `WHERE id = 1; SHUTDOWN` gasi server

## Web Cache Deception Attack

https://omergil.blogspot.hr/2017/02/web-cache-deception-attack.html

CDN je iskonfiguriran da cachira sve URL-ove koji završavaju s `.css`. Paypalov server pri upitu na nepostojeći `/myaccount/attack.css` vraća `/myaccount` što CDN cachira makar cache policy bio postavljen na no-cache. Na taj način napadač može natjerati korisnika da napravi request na nepostojeći url, te dohvatiti rezultat requesta iz CDN cachea, došavši do osobnih informacija koje su dostupne na `/myaccount` stranici.

Zato je važno ne dopuštati "pametno" prepoznavanje URL-ova, nego na sve nepostojeće URL-ove odgovarati s 404.

## Upload Security

- ne dopuštaj da korisnik bira ime ili putanju filea
- provjeri maksimalnu veličinu
- whitelista file typeova

## URI validation

https://blog.steve.fi/If_your_code_accepts_URIs_as_input__.html

Dopusti samo `http://` i `https://` inpute. Inače ti napadač može poslati `file:///etc/passwd`.

## Reflections on Trusting Trust by Ken Thompson

Trojanski konj je program koji glumi koristan program. Npr. unixov `login` koji osim što prihvati tvoju lozinku, šalje je i napadaču email. Ukoliko je promijenjen samo binary naredbe `login`, dovoljno ju je rekompilirati da uklonio zlonamjerni kod.

Ali ako napadač u compiler doda kod koji prepoznaje da se kompilira naredba `login` i doda joj zlonamjerni kod, trojanac će se održati čak i ako u potpunosti reinstaliramo `login` iz bilo kojeg sourca.

Ovo je moguće napraviti čak i bez da se ostavi trag u sourceu compilera. Prvo se u source compilera doda prepoznavanje kompiliranja `logina`, ali i prepoznavanje kompiliranja samog compilera. Zatim se taj binary spremi kao službeni C kompiler, a iz sourcea se izbriše spomenuti zlonamjerni kod. Svaka buduća reinstalacija kompilera će umetnuti ovaj samogenerirajući kodu binary kompilera, iz kojeg god sourca se on kompilirao.

## Literatura

* https://www.owasp.org/index.php/Category:Attack

## Tools

* https://github.com/rapid7/metasploit-framework - penetration testing, napisan u Rubyju.
