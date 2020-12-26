# Game design

## Zašto igramo

Prema seminalnom radu `MDA: A Formal Approach to Game Design and Game Research`, postoje temeljni emocionalni razlozi zbog kojih igramo neku igru (*aesthetics*). Različiti igrači traže različite stvari, ali iznenađujuće, postoji konačan popis razloga zbog kojih uživamo u igrama:

1. **Sensory:** Zadovoljstvo iz osjetila: vizualnog, zvučnog, taktilnog (npr. figure kod boardgameova).
2. **Fantasy:** Zadovoljstvo iz dobijanja uloge koja ti nije dostupna u stvarnom životu.
3. **Narrative:** Zadovoljstvo iz praćenja kako se priča razvija.
4. **Challenge:** Zadovoljstvo iz iskušavanja sebe, igra je niz izazova koje treba preći.
5. **Fellowship:** Zadovoljstvo iz društvenih interakcija i suradnje s drugim igračima, a igra je samo okvir za druženje. Ovo je jedna od glavnih prednost društvenih igara nad video igrama: omogućuju da se igrači povežu na nov način.
6. **Discovery:** Zadovoljstvo iz istraživanja i otkrivanja novih stvari.
7. **Expression:** Zadovoljstvo iz kreativnog izražavanja unutar okvira igre.
8. **Abnegation:** Zadovoljstvo iz puštanja mozga na pašu i izvršavanja repetitvnog zadatka (npr. grindanje).

Većina igara odaberu dvije ili tri estetike na koje će se fokusirati. Neke imaju više, npr. za uspjeh Minecrafta je dijelom zaslužan i velik broj kategorija kojih pokriva.

U svim ostalim umjetnostima, žanrovi se definiraju prema estetikama, tj. osjećajima koje izazivaju kod gledatelja. Samo u igrama se žanrovi definiraju prema mehanici, tj. načinu interkacije. Zato developeri često zanemare estetiku u odnosu na mehaniku, iako je estetika glavni način na koji se igrač povezuje s igrom.

## Depth vs Complexity

Dubina (*depth*) predstavlja broj odluka koje se mogu donijeti unutar skupa pravila. Bitno je da se dubina odnosi samo na odluke kojih su igrači svjesni.

Kompleksnost (*complexity*) je mentalni napor koji se stavlja na igrača: pravila koju trebati pamtiti, podatci i kalkulacije koje trebaju obaviti da bi donijeli odluku.

Drugim riječima, kompleksnost su broj alata koje postavimo ispred igrača, a dubina je broj stvari koje mogu napraviti s tim alatima. Igre kao što je Go s malim brojem pravila mogu imati veliku dubinu.

## Donošenje odluka

Izbor možemo definirati kao trenutak tijekom igre u kojem igrač može napraviti više akcija, ali mora odabrati jednu. To može biti u kontekstu mehanike (npr. kretanje, pucanje) ili u kontekstu narativa (npr. opcija unutar dijaloga). Osnovna zadaća izbora je igraču stvoriti osjećaj da njegove **odluke imaju značenje**.

Bitno je da odluka koju igrač donosi nije arbitrarna. Igrač mora imati sistem prema kojem će odvagati opcije. To ne znači da mora biti svjestan posljedica svog izbora, bitno je samo da vjeruje da ima sistem.

Ako se odluka može izraziti čisto matematički, onda postoji očiti najbolji odabir, pa odluka nema značenje. Izbjegavaj takve situacije, ili ih barem pokušaj prikriti.

Iluzija izbora je korisna stvar jer nitko nema neograničeno vrijeme i budžet za razvoj igre. Treba biti oprezan da iluzija nije preočita. Korisno je imati barem neke posljedicu, npr. achievement ili liniju teksta (npr. "Pero will remember this"). Igrač želi priznanje da je nešto napravio.

## Information Horizon

Želimo balansirati količinu informaciju koja je dostupna igraču prilikom donošenja odluka. Ako je informacija premalo, igrač će doživljavati igru kao nasumičnu i neće raditi nikakve planove. Ako je informacije previše, igrač će ili biti paraliziran pred odlukom, ili će mu igra biti monotona i predvidljiva nakon što povuče potez.

Idealno, nove informacije igraču treba davati u spikeovima koji se događaju u dovoljno udaljenim intervalima (npr. Pandemic epidemije, taman kad misliš da imaš plan one ga pošemere).

Informacije se mogu ograničiti privremenim skrivanjem (npr. fog of war), eksponencijalnom komplesnošću (npr. šah) ili nasumičnošću.

## Promjena

Ljudska psihologija se dobro prilagođava na podražaje - ako stalno nudiš uzbuđenje, nakon nekog vremena igrač će se naviknuti i nestat će čarolije. Zato je bitno dobro rasporediti uzbudljive trenutke u igri oko mirnijih razdoblja u kojima će igrač imati vremena da procesira i smiri emocije.

Na ovaj način funkcioniraju horor igre, koje alterniraju između tensiona (mirno razdoblje) i releasa (uzbuđenje).

**Promjena je najmoćniji alat u dizajnu igara**. Svi ostali alati (krivulje pažnje, tročinska struktura, hero's journey...) su samo okviri za stvaranje promjene.

Kada se igra mijenja, iskustvo koje stvara se mijenja s njom. Izvedeno na pravi način, stvara osjećaj razgovora - igra aktivno sluša i odgovara igraču kroz svoje promjene.

## Prilagodba težine

Većina igara te pita da postaviš težinu na početku što je blesavo, jer još nisi ni počeo igrati. Otkud da znaš koliko je igra teška? Neke igre igračima ponude da promjene težinu tijekom igre (npr. ako si umro više puta na istom mjestu), ali većina igrača to ne voli koristiti jer se osjećaju kao varaju.

Idealno, mehanike unutar igre omogućuju igračima da odaberu vlastitu težinu, pod vlastitim uvjetima. Npr. `Dark Souls II` ima range oružja i Ring of life koji olakšavaju borbu s većinom neprijatelja. Možeš ići u borbu u co-op modu, gdje su neprijatelji malo jači, ali ne dvostruko jači. Za igrače koji žele dodatan izazov, napravili su posebne achievemente i modove.

## Level Scaling

Kako igrači napreduju i postaju snažniji, čini se kao dobra ideja skalirati i snagu generičnih neprijatelja koje susreću (npr. slimeova). Ovo je generalno loša ideja, jer će igraču oduzeti osjećaj napretka, a i često nema narativnog smisla (zašto me banditi levela 100 napadaju zbog par novčića).

Jedini slučaj kada je dobro skalirati snagu neprijatelja su posebni eventi poput bossova, za koje želimo da budu izazov koliko god igrač snažan bio.

## Achievementi

Achievementi su dobri ako inspiriraju da isprobaš drugi način igre koji dizajneri nisu htjeli staviti u prvi plan (npr. pacifist run).

Achievmentima je dobro i iznenaditi igrača ako je napravio nešto posebno misleći da se developer nije toga sjetio (npr. popeo na neki nemoguć vrh). Ako ga igra u tom slučaju nagradi makar i porukom, to će mu ostati u sjećanju.

Izbjegavaj preplavljivanje achievementima bez značenja (napravio se nešto što je dio igre) samo da bi stvorio kolekcionarsku opsesiju.

## Skrivene mehanike

Dobar dio dizajna igara svodi se na stvaranje iluzija. Manipuliranjem ljudske psihe uvjerit će igrača da njegove odluke imaju važan utjecaj na priču, ili da je uspio nadvladati nemoguće izazove. Sva ta iskustva su pažljivo orkestrirana. Dizajneri postavljaju točke znajući da će ih naša psiha rado povezati.

Naši mozgovi su programirani da traže zadovoljstvo, stimulaciju i pohvalu. Zadatak dizajnera je da zaokupi i zabavi igrača, i da ih ponekad odvede putovima za koje nisu znali da žele.

Primjerice, najbolji trenutci akcijske igre su oni u kojima nam se čini da smo preživjeli za dlaku, s crticom healtha i zadnjih par metaka. Na dizajneru je da omogući ovakve situacije: zadnja crtica je možda zapravo četvrtina healtha; zadnjih par metaka možda rade puno više damagea; fatalni udarac te možda šalje na 1 health i daje imunost na kratko vrijeme umjesto da te instantno ubije.

Neke igre imaju mehaniku da neprijatelji uvijek promaše kada prvi put zapucaju na igrača, dajući priliku da se regrupira bez osjećaja zasjede.
