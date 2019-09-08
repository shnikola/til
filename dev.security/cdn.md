# Cache Poisoning

## Subresource Integrity

Korištenje CDN-a pretpostavlja potpuno povjerenje u njega. Ukoliko CDN bude komprimitiran (koliko god to bilo malo izgledno, događa se) sva standardna zaštita od XSS-a i korištenje HTTPS-a postaje uzaludna.

*Subresource Integrity* je oblik zaštite kojeg podržavaju moderni browseri. Na CSS i JS tagove dodaje se atribut `integrity="sha256-1a6d873cf..."` s digestom resourca. Browser pri dohvaćanju automatski provjerava digest i odbija loadati resource ako je različit.

# Stories

## Web Cache Deception Attack

https://omergil.blogspot.hr/2017/02/web-cache-deception-attack.html

CDN je iskonfiguriran da cachira sve URL-ove koji završavaju s `.css`. Paypalov server pri upitu na nepostojeći `/myaccount/attack.css` vraća `/myaccount` što CDN cachira makar cache policy bio postavljen na no-cache. Na taj način napadač može natjerati korisnika da napravi request na nepostojeći url, te dohvatiti rezultat requesta iz CDN cachea, došavši do osobnih informacija koje su dostupne na `/myaccount` stranici.

Zato je važno ne dopuštati "pametno" prepoznavanje URL-ova, nego na sve nepostojeće URL-ove odgovarati s 404.

## CloudFlare Memory Leak

https://blog.cloudflare.com/incident-report-on-memory-leak-caused-by-cloudflare-parser-bug/

Zbog buga u handlanju pointera u HTML parseru Cloudflarea, u HTTP response dodavao se sadržaj memorije servera. Između ostalog tu su bili dijelovi tuđih HTTP requesta s headerima, cookijima, API keyevima i sl. Još gore, search enginei su cachirali stranice s tim osjetljivim sadržajem, dopustivši svakome da im pristupi.
