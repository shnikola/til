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

## On Phone Numbers and Identity
https://medium.com/the-coinbase-blog/on-phone-numbers-and-identity-423db8577e58
Napadač je došao u posjed Verizon telefonskog računa (koristio je osobne podatke pronađene na netu).
Zatim je prebacio broj s Verizona na VOIP, i tako dobio pristup broju. Sada je mogao koristiti broj za
recover Facebook accounta i glumiti vlasnika preko SMS-a.
**Pouka:** Posjedovanje kontrole nad telefonskim brojem ne može biti garancija identiteta. Za 2-factor autentifikaciju koristi U2F, Push-based ili TOTP. Sami SMS više nije dovoljan.

## Instagram RCE
http://www.exfiltrated.com/research-Instagram-RCE.php
Našao zaboravljeni server (https://sensu.instagram.com) koji je koristio verziju Railsa vulnerable na Remote Code Execution kroz session. Još bolje, našao je sensu-admin Rails gem u kojem je bio example `secret_token.rb` kojeg su koristili u produkciji (facepalm), te je pomoću toga izgenerirao cookie s kojim je ušao na server i pronašao keyeve za AWS. Drama ensued.
