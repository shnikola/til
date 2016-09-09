# Stories

## Exploitanje call servisa
https://www.arneswinnen.net/2016/07/how-i-could-steal-money-from-instagram-google-and-microsoft
Lik je zeznuo google, facebook i microsoft da pri verifikaciji zovu premium telefonske brojeve od kojih je zarađivao.

## LastPass autofill explout
https://labs.detectify.com/2016/07/27/how-i-made-lastpass-give-me-all-your-passwords
Loš regex URLa u autofill ekstenziji dopuštao je da dohvatiš passworde s nedopuštene domene.
**Pouka:** Pazi kako parsiraš URL.

## Race Conditions on the Web
https://www.josipfranjkovic.com/blog/race-conditions-on-web
* Slanje puno istovremenih requestova s kuponom na Facebook, DigitalOcean i Mega.nz daje višestruko korištenje kupona.
* Slanje puno istovremenih requestova omogućilo je da više puta podigne Bitcoine s bug bounty platforme Cobalt.io.
* Slanje puno istovremenih requestova za Facebook email confirmation, naizmjence sa svojom i tuđom email adresom, poslalo je na njegov mail confirmation code za tuđu adresu.
**Pouka:** Koristi per-user lockove na bitnim dijelovima koda.
