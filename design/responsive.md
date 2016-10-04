# Responsive Design

## Custom font-size
Defaultna početna veličina fonta u browseru je `16px`, ali korisici mogu to promijeniti u postavkama.
Zato nemoj *nikad* pisati `html { font-size: 20px }`, jer tako ne poštuješ njihove postavke.
Umjesto toga, koristi `html { font-size: 125% }`, a za sve ostale fontove koristi `rem`.


## Responsive Typography
http://typecast.com/blog/a-more-modern-scale-for-web-typography
Omjer headera, teksta i line-heighta ne može biti isti za desktop i za mobile.
Npr. ako su linije duže, line-height mora biti veći.
Na linku gore su predloženi omjeri za različite breakpointe.


## CSS Locks
http://fvsch.com/code/css-locks
Ako želiš mijenjati property poput `font-size`, moraš koristiti breakpointe.
Ako želiš da se kontinuirano i fluidno mijenja veličina, možeš koristiti `vw`.
Ako usto još želiš da imaš definirani minimum, maksimum i linerni rast između, možeš koristito ovu kompliciranu metodu s `calc`.


## Responsive Email Design
https://litmus.com/blog/understanding-responsive-and-hybrid-email-design
* Koristi tablice s `width: 100%` i `max-width`.
* Ako trebaš podržavati Outlook, dodaj condition comente s fiksnom širinom `[if (gte mso 9)|(IE)]`
* Za složenije slučajeve koristi media querije, ali imaj na umu da ih pola email clienta ne podržava (npr. Gmail)


## Responsive Navigation Patterns
http://bradfrost.com/blog/web/responsive-nav-patterns
Kako napraviti responsivnu navigaciju:
* *Top Nav*: samo je ostavi na vrhu stranice. Dobra za malo linkova, ali loše ako zauzima više redova.
* *Footer Anchor*: prebačena u footer, s anchorom na vrhu stranice. Ostavlja više mjesta na vrhu, ali nije baš elegantno i skakanje može zbuniti korisnika.
* *The Toggle*: button na vrhu stranice iz kojeg ispadne navigacija. Elegantno i jednostavno, samo pazi da animacija bude glatka.
* *Left Nav*: klik na button slidea navigaciju s lijeve strane. Daje puno prostora, ali je zeznuta za dobro izvesti.

Ako imaš kompliciranu navigaciju:
* Koristi analyticse da odrediš koji linkovi su ti nebitni (npr. terms ne trebaju biti u glavnog navigaciji).
* Ako imaš milijun sekcija i stranica, istakni *search*.

* *The Multi Toggle*: nested accordion meniji na vrhu. Nije pretjerano fancy, ali je dovoljno dobro.
* *Right-To-Left*: klik na kategoriju slidea lijevo na podmeni. Fancy, ali komplicirano.
* *Skip-the-subnav*: Klik na nad-kategoriju otvara novu stranicu s podnavigacijom. Zahtjeva reload, ali user najčešće želi ići tamo ako je već kliknuo.
