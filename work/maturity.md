# Maturity

## Jednostavna pravila

1. Ne deployaš popodne.
2. Ne deployaš petkom.

## Working with Code

Kompetentan programer zna kako nešto napraviti, proficient programer zna *zašto* i *kada* nešto napraviti. Par savjeta kako ići u tom smjeru:

Razmotri zašto nešto radiš na određeni način. Navedi prednosti i nedostatke tog pristupa u konkretnoj situaciji.

Pitaj druge da ti objasne zašto rade stvari na određeni način.

Preferiraj učenje iz iskustva umjesto čitanja tuđih priča i prateći cargo cult. Npr. kad ti jedan server ne bude mogao podnijeti load, prouči HAProxy; kad ti site bude imao puno više db readova od writeova, prouči master-slave replication; kad ti baza postane prespora, prouči Memcached; kad ne budeš htio da requestovi dolaze do app servera, prouči Varnish. Dotad, neka stvari budu jednostavne koliko god mogu biti.

Uvijek radi na rubu svojih sposobnosti - to je najbolji način da napreduješ.

Prouči primarne izvore umjesto prepričanih članaka. Inspiracija stiže od originalnih ideja.

Odaberi nekoliko specifičnih skillova i postani kompetentan u njima do krajnjeg ekstrema.

Prvo gledaj "big picture", onda radi na detaljima, ali svako malo se vrati na "big picture".

Svaki dan prošetaj sat vremena, sam ili u društvu, razmišljaj ili razgovaraj o projektu.

Očekuj manje programiranja, ali zadrži vlasništvo nad par komponenti na 1/3 svog vremena. Tako ostaješ u toku s featurima i bugovima.

## Working with People

Kad netko zajebe, nemoj ga napadati nego mu pomozi da to popravi, pobrini se da shvati u čemu je pogriješio te da nauči iz te pogreške. Ako je potrebno, promijeni procedure u firmi da se ta pogreška ne može ponoviti.

Ne budi seronja: neka ljudima bude ugodno raditi s tobom. Kada kritiziraš, komentiraj kod, a ne osobu.

Traži konstruktivnu kritiku svojih ideja: sve što radiš utječe na druge. Zajednička rasprava uvijek dovodi do boljih odluka.

Ako netko predloži pristup za koji misliš da je kriv, reci: "Nisam siguran da je to pravi pristup zbog X, Y i Z. Ali bio sam već u krivu, pa sam možda i sad. Koliko misliš da će ti vremena trebati da proučiš taj pristup?"

Ako te netko želi pitati nešto, budi dostupan i pravi se kako te uopće ne smeta što te prekida u nečemu. Ako ti želiš nekog pitati nešto, uvijek provjeri odgovara li im sada i ponudi se doći kasnije ako su usred nečega.

Ne izbjegavaj davati *procjene vremena*: nitko ih ne voli, pa vježbaj da postaneš bolji u njima.

Znaj da će svaki projekt imati dosadnih dijelova: budi prvi koji će te dijelove preuzeti na sebe.

Svaki će projekt, koliko god savršen bio, imati slabih točaka (corner cutting, technical debt). Umjesto da kukaš i proklinješ druge jednom kad dođu na naplatu, osvijesti ih čim se pojave, prihvati i dokumentiraj za vrijeme kada ih budeš mogao popraviti.

Ne prakticiraj *cover your ass engineering* tipa "Ja sam to napravio kako treba, njihova je krivica što ne radi po specifikaciji." Uvijek izađi drugima u susret.

Nauči druge umjesto da rješavaš probleme za njih.

Ne tuži se ni na što ako nemaš barem jedan prijedlog riješenja.

Budi skroman. Proslijedi pohvale na tim, preuzmi kritike na sebe.

## Surviving the Framework Hype Cycle

https://www.youtube.com/watch?v=9zc4DSTRGeM

Nova tehnologija se uvijek čini privlačnijom - jer je još nismo upoznali i zamrzili. Svaka tehnologija prolazi kroz prirodni *Hype Cycle*:

1. *Technology Trigger*: launch uz velika obećanja i nekoliko early adoptera. Za Rails, "Build a Blog in 15 minutes" video. Hacker News blow upa i svi su jako uživljeni.
2. *Peak of Inflated Expectations*: svi pričaju o tehnologiji kao da će ispuniti mokre snove svih programera. DHH na naslovnici Linux časopisa posvećenom Rubyju.
3. *Trough of Disillusionment*: tehnologija ne ispuni nemoguća očekivanja. Ljudi glasno odlaze i kukaju kako je najgore na svijetu. Članci poput "Rails is a Ghetto".
4. *Slope of Enlightenment*: ako tehnologija *preživi*, počinje nazirati svijetlo na kraju tunela. Izlaze verzije 2.0, 3.0. Alati i materijali za učenje postaju sve bolji.
5. *Plateau of Productivity*: dio ljudi koji su ostali postaju produktivni i ne vide potrebu da pretjerano pričaju o tome. MySQL već godinama nije nikome seksi, jer se nalazi ovdje.

Svaki programer spada u jednu od kategorija. Svaka kategorija ne nužna da bi tehnologija napredovala
* *Pioneers*: eksperimentiraju i stvaraju sliku kako bi stvari mogle izgledati.
* *Settlers*: spajaju to sa stvarnim problemima.
* *Town Planners*: skaliraju da dođe do velikog broja ljudi. Dosadno održavanje.

Bitno je znati u kojem se trenutku priključiti tehnologiji. Ja sam settler/planner, pa mi najbolje odgovara 4. korak.

Utjeha:
* Tehnologija koju koristiš neće umrijeti. Rails 2 aplikacije se danas vrte u produkciji i rješavaju probleme za puno ljudi.
* Ako te strah da te vrijeme ne pregazi: na poslu koristi jednu stabilnu tehnologiju, a na side projectu jednu iz znatiželje.
* Dosta s Java-shamingom! Nitko se *ne bi smio sramiti* što voli i koristi neku tehnologiju.

Obećanje nove tehnologije nije da ćeš magično postati 10x programer. Obećanje je da ćeš doći do Plateaua. Tamo ćeš biti dovoljno produktivan da se fokusiraš i sagledaš sliku iz krupnije perspektive.

Inače jako dobra i zabavna prezentacija!

(vidi još: https://www.youtube.com/watch?v=3wyd6J3yjcs, sličan rant, samo iz perspektive Java developera)

## Literatura

* https://www.oreilly.com/ideas/the-traits-of-a-proficient-programmer
* https://www.briangilham.com/blog/2016/10/10/be-kind
* http://boz.com/articles/be-kind.html
* http://www.kitchensoap.com/2012/10/25/on-being-a-senior-engineer
* https://www.youtube.com/watch?v=yYihop9gHj4 (Endurance by Yehuda Katz)
