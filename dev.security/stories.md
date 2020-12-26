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

## Spotify unicode account hijacking

https://labs.spotify.com/2013/06/18/creative-usernames/

Registriranjem korisnika s Unicode imenom `ᴮᴵᴳᴮᴵᴿᴰ` i slanjem password reset maila, promijenio bi password korisniku `BigBird`.

Imali su ugrađenu provjeru protiv homografskih napada tako što su u bazu zapisivali "kanonski" oblik usernamea i koristila za uniqueness provjeru. Ali kanonizacija (`str.upcase.unicode_normalize`) nije bila idempodentna: `ᴮᴵᴳᴮᴵᴿᴰ` > `bigbird` > `BIGBIRD`, pa se u određenim slučajevima mogla izbjeći provjera uniquenessa, a i dalje koristiti username kao tuđi.

**Pouka:** ako normaliziraš Unicode, provjeri je li normalizacija idempodentna (koliko god puta se pozvala uvijek vrati istu stvar).

## Gaining Access through Helpdesk

https://medium.freecodecamp.org/how-i-hacked-hundreds-of-companies-through-their-helpdesk-b7680ddc2d4c

Popularni komunikacijski alati kao Slack, Yammer i Facebook Workplace često dopuštaju registraciju svima koji imaju email s domenom kompanije (`@company.hr`). Za registraciju uneseš email, a oni ti šalju poruku s linkom potvrde.

Istovremeno, neki issue trackeri dopuštaju da otvoriš novi issue tako da pošalješ email virtualnu email adresu (`incoming+initdc/myproject+a67d5cf917...@company.hr`). Uneseš li tu virtualnu adresu u Slack, on će poslati konfirmacijski email na nju, što će stvoriti tebi vidljiv ticket u kojem je link s pristupom Slacku.

Drugi način je korištenjem ticketing sustava kao Zendesk, Kayako i WHMC koji dopuštaju da se registriraš s bilo kojom email adresom bez provjere, i imaš pristup ticketima koji stižu s tog emaila. Registriraš se u Zendesku s `feedback@slack.com`, a na slacku s `support@company.hr`. Slack će poslati mail s linkom na `support@company.hr` s adrese `feedback@slack.com`, što će Zendesk protumačiti kao novi ticket koji si otvorio i dodati ga u tvoju listu.

Na Slacku developeri često na javnim kanalima shareaju tajne podatke poput passworda ili informacija o klijentima, što napadač može iskoristiti za daljnje napade.

**Pouka:** ne dopuštaj korinicima pristup Slacku bez invitea. Ako nije opcija, neka se automatizirani mailovi šalju s različite poddomene, npr. `@reply.company.hr`. Izbjegavaj autentifikaciju samo na račun email domene.

## Hacking Apple

https://samcurry.net/hacking-apple

Apple ima preko 7000 domena i 25000 servera, trebalo je samo pronaći neki zaboravljeni s dovoljno velikom sigurnosnom rupom da se uđe u ostatak sustava. Primjerice, opskurni program za edukatore je koristio Jive forum integriran s Appleovim authom, pri čemu je registracija svim korisnicima postvljala isti password. Iteracijom su našli račun s 3 slova i ulogirali se, ali je `/admin` page je imao IP provjeru; ali `/admin;` nije bio zaštićen.

