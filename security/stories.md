# Stories

## Exploitanje call servisa

https://www.arneswinnen.net/2016/07/how-i-could-steal-money-from-instagram-google-and-microsoft

Lik je zeznuo google, facebook i microsoft da pri verifikaciji zovu premium telefonske brojeve od kojih je zarađivao.

## LastPass autofill exploit

https://labs.detectify.com/2016/07/27/how-i-made-lastpass-give-me-all-your-passwords

Loš regex URLa u autofill ekstenziji dopuštao je da dohvatiš passworde s nedopuštene domene.

**Pouka:** Pazi kako parsiraš URL.

## Race Conditions on the Web

https://www.josipfranjkovic.com/blog/race-conditions-on-web

* Slanje puno istovremenih requestova s kuponom na Facebook, DigitalOcean i Mega.nz daje višestruko korištenje kupona.
* Slanje puno istovremenih requestova omogućilo je da više puta podigne Bitcoine s bug bounty platforme Cobalt.io.
* Slanje puno istovremenih requestova za Facebook email confirmation, naizmjence sa svojom i tuđom email adresom, poslalo je na njegov mail confirmation code za tuđu adresu.

**Pouka:** Koristi per-user lockove na bitnim dijelovima koda.

## Instagram RCE

http://www.exfiltrated.com/research-Instagram-RCE.php

Našao zaboravljeni server (https://sensu.instagram.com) koji je koristio verziju Railsa vulnerable na Remote Code Execution kroz session. Još bolje, našao je sensu-admin Rails gem u kojem je bio example `secret_token.rb` kojeg su koristili u produkciji (facepalm), te je pomoću toga izgenerirao cookie s kojim je ušao na server i pronašao keyeve za AWS. Drama ensued.

## CloudFlare Memory Leak

https://blog.cloudflare.com/incident-report-on-memory-leak-caused-by-cloudflare-parser-bug/

Zbog buga u handlanju pointera u HTML parseru Cloudflarea, u HTTP response dodavao se sadržaj memorije servera. Između ostalog tu su bili dijelovi tuđih HTTP requesta s headerima, cookijima, API keyevima i sl. Još gore, search enginei su cachirali stranice s tim osjetljivim sadržajem, dopustivši svakome da im pristupi.

## Spotify unicode account hijacking

https://labs.spotify.com/2013/06/18/creative-usernames/

Registriranjem korisnika s Unicode imenom `ᴮᴵᴳᴮᴵᴿᴰ` i slanjem password reset maila, promijenio bi password korisniku `BigBird`.

Imali su ugrađenu provjeru protiv homografskih napada tako što su u bazu zapisivali "kanonski" oblik usernamea i koristila za uniqueness provjeru. Ali kanonizacija (`str.upcase.unicode_normalize`) nije bila idempodentna: `ᴮᴵᴳᴮᴵᴿᴰ` > `bigbird` > `BIGBIRD`, pa se u određenim slučajevima mogla izbjeći provjera uniquenessa, a i dalje koristiti username kao tuđi.

**Pouka:** ako normaliziraš Unicode, provjeri je li normalizacija idempodentna (koliko god puta se pozvala uvijek vrati istu stvar).

## Taking Control of All .io Domains With a Targeted Registration

https://thehackerblog.com/the-io-error-taking-control-of-all-io-domains-with-a-targeted-registration/

Pronašao je da su 4 od 7 navedenih nameservera top level domene `.io` neregistrirani i čak dostupni za kupnju. Kupio ih je i mogao je bez problema servirati lažne DNS rezultate.
