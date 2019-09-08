# Email

Korišteni email klijenti: http://emailclientmarketshare.com.
iPhone, Gmail, iPad, Android i Apple Mail čine 80%.

Plain text mailovi su čisti plain text, HTML mailovi su čisti HTML.
MIME (Multipurpose Internet Mail Extensions) omogućuju da u bodiju šalješ i plain text i HTML format maila, pa će klijenti odabrati onaj koji podržavaju.

## HTML

`div` elementi stvaraju problem mnogim klijentima, i dalje je najsigurnije držati se `table`. Usto:
* `padding` umjesto `margin`.
* `background-color` umjesto `background`
* ne zaboravi `border="0" cellpadding="0" cellspacing="0"`.
* dodaj `role=presenation` na svaki table zbog accessibilitija.

Neki klijenti prepoznaju samo inline CSS. Pošto pisanje inline stilova nije skalabilno, koristi gem kao `Premailer`.

Centrirane buttone napravi s tablicom koja centrira sadržaj unutar koje je druga tablica koja boja pozadinu, s linkom u sebi.

Najlakše je držati se sistemskih fontova (Helvetica, Arial), ali moguće je koristiti web fontove s `@import url(http://fonts.googleapis.com/css?family=Pacifico)` i webkit media querija `@media screen and (-webkit-min-device-pixel-ratio:0) ... font-family: Pacifico, ...;`.

Dodaj `font-family`, `font-size` i `color` stilove u svaki `td` element kako klijenti ne bi koristili nešto svoje.

Klijente možeš targetati s conditionalima:
* `<!--[if mso]>` za Word-based Outlook Express
* `<!--[if (IE)]>` za IE-based Outlook Express
* `@media screen and (-webkit-min-device-pixel-ratio:0)` za WebKit-based

Outlook neće prikazati slike po defaultu; Apple Mail i Gmail hoće. Dodaj `alt` tekst na `img` elemente. Animirani `gif` je podržan kod većine klijenata, a tamo gdje nije prikazat će se prvi frame. Apple Mail i Outlook čak podržavaju video background, što izgleda jako kul: https://litmus.com/blog/how-to-code-html5-video-background-in-email.

## Responsiveness

*Fluid* emailovi drže sve u jednom stupcu: `max-width: 600px; width: 100%;`

*Responsive and adaptive* imaju više stupaca koji se pretvore u jedan kad je viewport preuzak. To zahtDio klijenata ne podržava media querije (npr. Yahoo, Windows Phone 8 i Gmail za Android). Postoji nekoliko rješenja za stvaranje responzivnih mailova.

## Client specific

Gmail Actions dodaju mali button kraj emaila u Gmail klijentu. Više detalja na https://developers.google.com/gmail/markup/actions/actions-overview.

Klijenti uzimaju prvi komad teksta da ga prikažu previewu. To možeš (zlo)upotrijebiti tako da sakriješ taj tekst s `color: transparent; display: none !important; height: 0; max-height: 0; max-width: 0; opacity: 0; overflow: hidden; mso-hide: all; visibility: hidden; width: 0;`.

# Literatura

* https://litmus.com
* https://www.smashingmagazine.com/2017/01/introduction-building-sending-html-email-for-web-developers
