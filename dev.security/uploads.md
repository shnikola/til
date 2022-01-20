# Uploads

## Napadi na server

Korisnički upload može kompromitirati server uploadanjem skripte (npr. PHP backdoor `c99shell`) koja će se pokrenuti unutar aplikacije i dobiti pristup shellu, bazi i filesystemu.

Za zaštitu se preporuča ima whitelistu dopuštenih file typeova (ne samo ekstenzije), ne dopuštati da korisnik bira ime ili putanju filea, i osigurati se da uploadani fileovi nemaju permission za execute. Općenito je dobro uploadane fileove držati na poptuno odvojenom serveru ili domeni (npr. S3).

Drugi napad je floodanje servera s dugim i velikim uploadovima. Za zaštitu radi rate limiting i definiraj maksimalnu dozvoljenu veličinu filea.

## Napadi na klijenta

Napadač može uploadati klijentske skripte (npr. HTML s JS-om, Java applete, Flash objekte) koji će zbog istog origina imati pristup korisnikovim cookijima.

I ovo se može spriječiti ograničavanjem dozvoljenih file typova i držanje uploada na odvojenoj domeni. Kada pružaš userima download filea, uvijek ga vraćaj s `Content-Disposition: attachment` headerom kako se ne bi otvorio u browseru.

When allowing other users to download the file, it is sometimes useful to force it as attachment using the  HTTP header

## MIME Sniffing

U slučaju da server za asset vrati `Content-Type` koji ne odgovara sadržaju (npr. `plain/text` umjesto `application/javascript`), neki browseri će parsirati sadržaj i sami odrediti MIME type. Ovo ponašanje se nazova *MIME sniffing*, i otvara mogućnosti za napad.

Napadač može uploadati `attack.txt` na `example.com`. Ako na siteu postoji način za injectanje HTML-a, može injectati `<script src="https://example.com/attack.txt">` kojeg će browser interpretirati kao JS. U ovom slučaju CSP ne pomaže jer skripta dolazi s iste domene.

Većina novijih browsera ne prakticira MIME sniffing, ali možeš se još dodatno osigurati header `X-Content-Type-Options: nosniff` s assetom i pobrineš se da uvijek vraćaš ispravan MIME type.

