# Design

## Frontend Design

https://www.toptal.com/front-end/to-designers-from-a-front-end-developer

* Mini style guide s tipografijom i elementima koji se ponavljaju
* Ne koristi više od tri font varijante na jednom siteu.
* Linkovi imaju barem 3 stanja: base, hover, focus. Plus ti isti za aktivne linkove u navigaciji.
* Neka hitbox za mobilne linkove bude dovoljno velik (barem `42px` širine i visine)
* Custom radio i check boxevi imaju stanja koja se kombiniraju (checked/unchecked, hover/not, focus/not, enabled/disabled, error/not)
* U formama definiraj labele, input help i errore.
* Definiraj kako interkaktivnost izgleda i koristi to samo za interaktivne elemente. Ne koristi boju branda za linkove ako je koristiš i za naglašavanje bitnog sadržaja.
* Definiraj izgled dinamčnog sadržaja: što ako je teksta premalo ili previše? Što ako sadržaja nema?
* Kada to sve napraviš, dobit ćeš stabilan, konzistentan i dosadan dizajn. Tada pričaj s klijentom i odredi koji dijelovi su najbitniji te njih učini posebnim.

## 8 Point Grid System

https://medium.com/built-to-adapt/intro-to-the-8-point-grid-system-d2573cde8632

Za sve veličine (height, width, padding, margins) koristi višekratnike broja 8. Na taj način će UI biti konzistentan, uređaji s većom pixel gustoćom neće imati problema s mutnih 0.5 piksela, a usto se i ne moraš misliti da li staviti `8px` ili `10px`.

## Information Design

2 golden rules of information design: show the data; show comparisons.

## Full background video

Koristi `video` tag sa sljedećim propertijima: `muted` (ne želimo smetati korisniku), `autoplay`, `loop`, i `playsinline` (da radi na iOS).

Da ispuni cijelu stranicu u CSS dodaj `video { object-fit: cover; width: 100vw; height: 100vh; position: fixed; top: 0; left: 0; }`.

Za accessability: dodaj keyboard accessible pause button na stranicu. Osiguraj da tekst preko videa bude čitljiv. Pripazi na bandwith.

## Bret Victor - Inventing on Principle

https://vimeo.com/36579366

Inspirativan video. Princip u kojem govori je: stavaraoci moraju imati neposrednu vezu s onim što stvaraju. Pronalazi primjere gdje je taj princip zapostavljen poput programiranja ili animacije, i pokazuje načine na koji možemo učiniti proces stvaranja izravnijim i otvorenijim za eksperimentiranje.

Koristimo elektroničke simbole napravljene za prikaz na papiru, ali imamo potpuno novi medij koji nam omogućuje mnogo više toga. Isto je s programiranjem - programski jezici dizajnirani su za bušene kartice. Umjesto da simuliramo izvođenje algoritma u glavi, zašto ga ne bi simulirali na računalu dok programiramo?

# Literatura

* Designing for performance: http://designingforperformance.com/
