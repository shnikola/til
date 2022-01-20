# Performance

Što manje bytova šalješ, to će se frontend prije prikazati. Izbaci librarije koje ne koristiš ili ih zamijeni optimalnijima.

Renderiraj HTML na serveru, ili još bolje, serviraj unaprijed buildani HTML s CDN-a gdje možeš. Koristi `gzip` ili `brotli` kompresiju, i `HTTP2` za paralelno dohvaćanje asseta.

Skripte učitaj na samom kraju, prije `</body>` taga. Tako će se osnovni HTML moći prikazati prije nego JS blokira rendering.

Assete šalji s `Cache-control: public, max-age=31536000, immutable`. Za invalidaciju stavljaj hash u naziv.

Slike kompresiraj i resizeaj. Koristi `loading="lazy"` atribut da im odgodiš učitavanje. Umjesto `GIF`ova, koristi `MP4` video.

Fontove zamjeni sistemskima ako branding nije pretjerano bitan.

## Mjerenje

Ne vjeruj svom brzom laptopu - većina korisnika imaju spore mobitele s 3G mrežom. Testiraj stranicu na jeftinim uređajima i throttlanim brzinama.

Odaberi jednu ključnu metriku (*key performance indicator*) koju ćeš mjeriti - npr. koliko traje da tekst na homepageu postane vidljiv korisniku. Ovaj broj treba biti poznat svima u timu. U slučaju da marketing želi dodati novi sadržaj na homepage, možeš postaviti pitanje: "Je li nam ok povećati korisnikovo čekanje za 700ms kako bi mogli prikazati tu reklamu ili HD video?". Cilj nije imati najbrži site na svijetu, ali treba imati benchmark prema kojem se možeš ravnati.

Korisni siteovi za mjerenje:
* [https://www.fastorslow.com]
* [https://www.webpagetest.org]
* [https://developers.google.com/speed/pagespeed/insights]

Ciljaj na sljedeće rezultate:
* inicijalni load u `< 1s`.
* response na akciju u `< 100ms`.
* animacije s `16ms` po frameu.

## Initial Render

1. Browser čita byteove, prevodi ih u charactere (prema zadanom encodingu), razdvaja tekst u tokene (`<html>`, `<body>`, itd.), tokene pretvara u nodeove s propertijima i pravilima, te objekte povezuje u stablo.
2. Od HTML-a izgrađuje **DOM tree** koje definira hijerarhiju nodeova. Od CSS-a izgrađuje **CSSOM tree** koje definira izgled.
3. Od DOM i CSSOM stabala stvara se **render tree**. Ono sadrži samo nodeove potrebne da se prikaže stranica. Preskaču se nodeovi poput `<script>` i `<head>` te elementi koji imaju `display: none;`.
4. Od render stabla računa se pozicija i veličina svakog nodea (**layout**).
5. Nodeovi se pretvaraju u piksele na ekranu (**paint**)

Da bi se stranica prvi put iscrtala, potrebni su DOM i CSSOM. Zato su HTML i CSS *render blocking* resursi, jer se stranica ne može početi prikazivati dok se cijeli ne učitaju i isparsiraju.

## Dohvaćanje CSS-a

Svaki `<link href="style.css">` defaultno blokira prvi render. Da bi se izbjeglo manično mijenjanje izgleda stranice, CSS se ne parsira inkrementalno, nego se čeka da se cijeli stylesheet skine.

Ako baš želiš početi prikazivati stranicu prije učitavanja CSS-a, možeš označiti stylesheet kao neblokirajući koristeći `media` atribut (npr. `media="print"` za printanje ili `media="(min-width: 40em)"`. Browser će ga skinuti, ali neće zbog njega čekati na render.

Drugi način neblokirajućeg skidanja CSS-a je korištenjem `preload` linka koji će se na `onload` postaviti u stylesheet: `<link rel="preload" as="style" href="style.css" onload="this.rel='stylesheet'">`. Koristiš li ovu metodu, pripazi da imaš fallback za starije browsere.

Ponekad nije ludo ubaciti inline CSS u `head`, pogotovo ako se radi o maloj stranici (`< 14KB`) na koju se korisnici ne vraćaju, pa se caching ne koristi. Komplikacije tipa "above-the-fold" inlininga izbjegavaj.

## Dohvaćanje JS-a

Kada browser pri parsiranju HTMLa dođe do `<script>` elementa (bilo inline, bilo remote), pauzirat će izgradnju DOM stabla dok ne skine i izvrši cijelu skriptu.

Ako želiš da rendering ne čeka na JS, stavi `<script>` tagove na dno `<body>` elementa. Tako će se učitati kad je HTML već parsiran. Ovo eventualno može biti sporo za velike HTML dokumente.

`<script defer>` u headu će skidati skriptu *paralelno s parsiranjem* HTML-a, a izvest će je tek kad DOM tree bude spreman (prije `DOMContentLoaded` eventa). Na ovaj način skripta je prije spremna, a rendering nije blokiran. Postoji još i `<script async>` koji se izvodi tijekom parsiranja, ali to najčešće nije korisno.

CSS može blokirati JS. Skripta se neće početi izvršavati dok se svi stylesheeti navedeni prije nje ne učitaju i ne isparsiraju. Browseri to rade kako bi izbjegli rijedak slučaj da JS želi očitati stil nekog elementa, a CSS se još nije učitao. Ovo je čest problem sa skriptom za Google Analytics koji učitavaju veći JS file pomoću `document.createElement`, jer učitavanje neće početi dok se CSS ne učita, pa se gubi paralelnost. Zato se GA skriptu preporuča staviti na vrh `<head>` elementa (koliko god čudno bilo).

## Izvršavanje JS-a

Nakon što je skripta dohvaćena, parsira se i izvršava. Za to vrijeme stranije je neinteraktivna. Parsiranje velikih skripta može trajati nekoliko sekundi, pa je dobro držati JS bundle što manjim.

Parsiranje `defer` i `async` skripti browser izvršava u zasebnom threadu (a Chrome to radi i za blokirajuće skripte).

Nikad nemoj koristiti `document.write`. Korištenje toga prisiljava da se HTML iznova parsira i gradi DOM stablo. Umjesto toga, koristi `document.createElement`.

Moderni JS engini pretpostavljaju da se većina funkcija neće odmah izvoditi, pa rade samo pre-parse koji provjerava ispravnost sintakse. Ali za immediately invoked funkcije ovo će zapravo usporiti izvođenje, jer će se raditi pre-parse i onda puni parse.

`optimize-js` (https://github.com/nolanlawson/optimize-js) pri buildanju dodaje zagrade na sve IIFE funkcije, kako bi se engineu dalo do znanja da prekoči pre-parse korak. Time se dobije speed boost i do 50%.

## Dohvaćanje slika

Slike su redovno najveći udio skinutih bytova. Što manji broj i veličinu slika budeš imao, više bandwitha ćeš osloboditi za druge resurse. Provjeri možeš li prikazati sliku drugom tehnologijom, npr. web fontovima i CSS efektima.

Browser automatski skida sve `<img>`, `<video>` i `<iframe>` elemente, čak i one koje nisu vidljive korisniku. Ako želiš to izbjeći, koristi `loading="lazy"` atribut.

Slike definirane u CSS-u (npr. `background-image`) će se skidati tek kad se izgenerira render stablo, i to samo one koje su na vidljivim elementima (iako neki browseri skidaju i one nevidljive).

Za optimiziranje SVG-a koristi `svgo`, i šalji ih gzipane. Za rasterizirane slike koristi `compressor.io` ili neki drugi optimizer.

Koristi `data-uri` za manje slike koje se pojavljuju samo na jednom mjestu.

## Dohvaćanje fontova

Najjednostavnije opcija je koristiti sistemske fontove: `font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif, "Apple Color Emoji", "Segoe UI Emoji", "Segoe UI Symbol"`.

`WOFF` i `WOFF2` formati su dovoljni za većinu modernih browsera. Dodaj `TTF` ako želiš podržavati stari Android, i `EOT` za `<IE9`.

Kada koristiš `@font-face`, unutar `src` koristi `format('woff')` kao hint. U tom slučaju browser neće morati skinuti font da bi odredio podržava li ga.

Fontovi se učitavaju tek kad se izgradi render tree (koji ovisi o DOM i CSSOM trees). Tek nakon što se loada CSS, počet će se loadati potrebni fontovi. Dok se font ne loada neki browseri (npr. _Safari_) neće uopće prikazati tekst (*Flash Of Invisible Text*), a neki će čekati 3 sekunde prije nego prikažu fallback font (_Chrome, Firefox_).

Kako bi izbjegao FOIT, koristi preloading: `<link rel="preload" href="font.woff2" as="font" type="font/woff2" crossorigin>` će preloadati font prije nego se učita CSS gdje je definiran.

Korisno je i definirati kako će se browser ponašati dok se font ne učita pomoću `font-display` u `@font-face`. Ako si siguran da će font stići brzo, `font-display: block` će sakriti tekst dok ne stigne. S druge strane, `font-display: swap` će koristi fallback dok se font ne loada.

Da minimiziraš *Flash Of Unstyled Text* zbog fallback fonta, prilagodi `font-size` fallbacka da se tekst što manje miče. Za dodatnu optimizaciju, prvo loadaj samo `normal` weight (browser će simulirati ostale debljine), a tek onda ostale.

U slučaju velikog broja glyphova (npr. azijskih jezika), podijeli font na subsetove i koristi `unicode-range`, pa će browser skinuti font samo za gliphove koji se nalaze na stranici.

## Rendering promjena

Svaki put kada se HTML ili CSS promijene, cijeli render proces se iznova izvodi kako bi se odredilo koje piksele treba nacrtati. Cilj nam je smanjiti vrijeme provedeno u gornjim koracima kako bi se stranica što prije prikazala, i kako bi se animacije fluidnije izvodile.

Savjeti za izbjegavanje re-layouta i re-painta:
* Dohvaćanje stila elementa (npr. `.width`) natjerat će browser da flusha, nemoj to raditi često.
* Bolje je napraviti mnogo izmjena na nevidljivom elementu pa ga onda prikazati.
* Bolje je promijeniti `class` elementa nego puno inline styleova.
* Animiraj elemete koji su `absolute` ili `fixed`.
* Nemoj se bindati na `scroll` event, već tretiraj dolazak do određene točke u scroll kao event koji se okine jednom.

## Hinting

`<link rel="preload" href="image.png">` unaprijed učitava resource za trenutnu stranicu. Browser inače preloada sve resurse navedene u HTMLu (js, css, img), ali ovo je korisno za resource koji se učitavaju iz CSS-a ili JS-a (npr fontovi). Za fontove dodaj `as="font" crossorigin`. Ako ti je svaka milisekunda bitna, možeš ga poslati u response headeru kao `Link: https://example.com/image.png; rel=preload; as=image`, pa će browser moći početi preloadati bez da isparsira HTML.

`<link rel="prefetch" href="image.png">` navodi resource koji će vjerojatno biti potreban u nekoj od sljedećih stranica. Browser će ga skinuti i cachirati s niskim prioritetom u backgroundu.

`<link rel="dns-prefetch" href="...">` unaprijed dohvaća DNS podatke za domenu kako bi umanjio latenciju DNS resolutiona. Moderni browseri inače to rade automatski za linkove i resurse (js, css, img) prisutne na stranici. Dodaj ako želiš ručno prefetchati neku domenu koje nema na trenutnoj stranici, ali očekuješ da će biti na drugim stranicama.

`<link rel="preconnect" href="...">` unaprijed odrađuje DNS lookup, TCP i SSL konekciju na dani host. Korisno ako imaš više blokirajućih resourca s različitih hostova, odradiš im sav connection overhead istovremeno, a oni se naknadno bez overheada skinu serijski.

## Scrolling Performance

Gotovo svi browseri obavljaju scrolling u zasebnom threadu. Zato možemo scrollati dok javascript blokira glavni thread.

`wheel`, `touchstart` i `touchmove` listeneri će blokirati scroll ne dopuštajući mu da se izvede u background threadu. Ostali eventovi ne blokiraju: `scroll` se poziva nakon scrolla, a Pointer Events API je posebno napravljen da ne blokira.

`document.addEventListener('touchstart', handler, {passive: true})` dodaje passive event listener koji ne blokira, tako što obeća browseru da neće koristi `preventDefault()`. Browser pritom može scrolling izvesti u backgroundu.

Nekim browserima pomaže ako ne dodaš listener globalno, nego na određeni element stranice.

# Literatura

* [https://www.youtube.com/watch?v=K1SFnrf4jZo] Adapting to the Mobile Web Present
* [https://developers.google.com/web/fundamentals/performance/optimizing-content-efficiency]
* [https://developers.google.com/web/fundamentals/performance/critical-rendering-path]
* [https://hackernoon.com/10-things-i-learned-making-the-fastest-site-in-the-world-18a0e1cdf4a7]
