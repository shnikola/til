# Web Accessibility

Poštivanje semantičkih struktura riješit će 90% accessibility problema.
Standarde poput `WAI-ARIA` koristi samo u krajnjoj nuždi.

## Images

Uvijek dodaj `alt` atribut na `<img>`, `<svg>` i `<picture>`. Uvijek!
* Za value stavi opis onog što je na slici (ali nemoj pisati "image of...").
* Ako je slika samo dekorativna, stavi prazan string.
* Ovo pomaže ne samo slijepima, nego i ljudima sa sporim konekcijama.

## Forms

Koristi `<label>`, bilo implicitni (s `<input>` unutar njega), bilo eksplicitni (s `for=input-id`). Nikako nemoj koristiti `placeholder` umjesto `<label>`, jer se skriva na fokus. Za `radio` buttone i njihovo zajednički label koristi `<fieldset>` i `<legend>`.

Za required inpute koristi `required` atribut, ili dodaj `*` u `label`. Nemoj koristiti samo boju da ih označiš - screen readeri to ne razaznaju.

Koristi ispravne inpute za `email`, `url` i `tel`. Mobilni useri će ti biti zahvalni, plus besplatna validacija.

Pobrini se da se `input` na `:focus` vizualno označi (npr. outlineom).

Ako želiš button, koristi `<button>`, a ne `<div>`. Ako ima samo ikonu, dodaj mu `alt` s tekstom. Prednosti su:
* *Tab* će ga automatski selektirati. Inače moraš koristiti `tab-index=0` i `role=button`.
* *Enter* ili *Space* dok je selektiran će triggerirati `click`. Na `div`u moraš loviti `keydown`.

Za custom checkbox, umjesto `display: none` koristi `position: absolute` i `opacity: 0`

## Document Structure

Koristi ispravno hijerarhiju headinga (`<h1>`, `<h2>`, itd). Screen readerima to jako pomaže.

Za podnaslove koristi `<p>`, a ne heading.

Pomažu i landmarks poput `<article>`, `<aside>`, `<footer>`, `<header>`, `<nav>` i `<main>`.

Koristi liste kad nabrajaš stvari.

Na početak sitea možeš dodati listu *skip linkova* s poveznicama na dijelove stranice. Tako korisnik može skipati header ili otići direktno na footer bez da čita cijelu stranicu. Listu možeš vizualno sakriti s `position: absolute`.

## Dynamic Content

U slučaju da JS-om brišeš element, potrudi se pa prebaci `focus` na idući element da korisnik zna gdje je.

## Tables

Dodaj `<caption>` za heading ili summary tablice.

Koristi `<th>` za headere i `scope=row` ili `scope=col` da kažeš u kom smjeru "gledaju".

## Color Contrast

Kontrast između glavnog teksta i pozadine mora biti minimalno `4.5 : 1`. Koristi online alate poput: http://accessible-colors.com

## Font Size

Defaultna početna veličina fonta u browseru je `16px`, ali korisnici mogu na razne načine to promijeniti.

Ako povećaju font na razini OS-a, npr. na 150%, svi elementi na stranici se povećavaju za 150%, neovisno o tome jesu li deklarirani u `px` ili `rem`.

Ako zoomiraju u browseru, opet se svi elementi na stranici proporcionalno povećavaju, neovisno o jedinicama u kojim su deklarirani.

Ako povećaju font na razini browsera, tek se u tom slučaju povećava defaultni font, pa se ostali fontovi pritom povećavaju samo ako koristiš `rem`. Ali u praksi većina najpopularnijih siteova ne poštuje ovo pravilo i koristi `px`, pa i većina korisnika koristi drugu metodu povećavanja fonta.

Ukratko, koristi `px` za fontove, `em` i `rem` donose previše komplikacija da bi bili vrijedni truda.

## Animation

Na OSX, korisnik može odabrati "Reduce Motion" u preferencama. To možeš ispoštovati u animacijama koristeći `@media (prefers-reduced-motion) { animation: none }`. Zasad radi samo u Safariju.

## Mobile

Nemoj disablati *Pinch to Zoom* s `maximum-scale=1.0`! Redizajniraj ako treba, ali nemoj oduzimati korisniku mogućnost da zooma.

## Hiding

https://allyjs.io/tutorials/hiding-elements.html

Za skrivanje:
* vizualno i od screen readera: `display: none` i `visibility: hidden`
* samo od screen readera: atribut `aria-hidden`
* samo vizualno: `.visually-hidden` s gornjeg linka

## Testiranje

U `chrome://flags` uključi Chrome DevTools Experiments, te u DevTools settings uključi accessibility.

Isprobaj `VoiceOver` na svom siteu

## Literatura

https://www.marcozehe.de/2015/12/14/the-web-accessibility-basics - pregled
https://github.com/showcases/web-accessibility - alati
