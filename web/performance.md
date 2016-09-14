# Performance

## Basic measurement
* https://www.webpagetest.org
* YSlow extension
* Google Page Speed


## How browsers work internally
http://www.html5rocks.com/en/tutorials/internals/howbrowserswork/
Svaki browser koristi rendering engine (Trident, Gecko, Webkit, Blink). Renderiranje stranice izvodi se u nekoliko koraka:
  1. Parsiranje HTMLa i izgradnja DOM stabla (elementi i atributi)
  2. Izgradnja Render stabla (vizualni elementi redom kojim će biti prikazani i izračunavanje stylea)
  3. Layout (određivanje pozicija i veličina)
  3. Painting (prolazak kroz render tree i iscrtavanje na UI)
Savjeti za izbjegavanje re-layouta i re-painta:
  * Dohevaćanje stila elementa (npr. `.width`) natjerat će browser da flusha, nemoj to raditi često.
  * Bolje je napraviti mnogo izmjena na nevidljivom elementu pa ga onda prikazati.
  * Bolje je promijeniti `class` elementa nego puno inline styleova.
  * Animiraj elemete koji su `absolute` ili `fixed`.


## Optimiziranje imagea
http://blog.pamelafox.org/2014/01/improving-front-page-performance.html
1. `tinypng` za optimiziranje PNGova, `SVGO` za SVG
2. data-uri za manje slike koje se samo jednom pojavljuju
3. Spriteovi/font za ikonice
4. Picturefill za retina/responzivne slike
5. Delayed load za veće slike, videove i iframeove.


# Stories

## 30K Page Views for $0.21: A Serverless Story
https://fmlnerd.com/2016/08/16/30k-page-views-for-0-21-a-serverless-story
Site je kalkulator za predviđenu zaradu filmova. Site je statičan i generira se na AWS-u.
* CloudWatch cron job nekoliko puta dnevno trigerira dohvaćanje novih podataka. *Free tier*
* Lambda na trigger dohvaća config s S3 i scrapea podatke, spremajući ih u JSON na S3. *Free tier <1M req/month*
* Iduća Lambda na event kreiranja JSONa dohvaća JSON i stvara od njega JS file i HTML.
* Sav trošak se svodi na S3 (oko *20 centi* mjesečno)


## Reddit 2010 (270M Page Views A Month)
http://highscalability.com/blog/2010/5/17/7-lessons-learned-while-building-reddit-to-270-million-page.html
* `Memcachiraju` sve živo: DB data, session data, renderirani html, memoizirane metode, *rate limiting*, locking, *change password linkove* (ne drže ih u bazi).
* Sve računaju unaprijed i cacheiraju, ništa on demand, npr. sortiranja hot, new, top, old su sva unaprijed izračunata.
* Kada user upvotea, napravi se minimum odmah (u bazu se doda upvote), a ostatak procesiranja se stavi u queue (sortiranje linkova, prepoznavanje spama, računanje nagradi...). Nema potrebe da user čeka za išta od toga. Za queuing koriste `RabbitMQ`.
