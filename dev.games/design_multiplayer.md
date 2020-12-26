# Multiplayer

## Turn Based

Turn based igre dijelimo na **static resource** u kojima imaš pristup svim resursima na početku igre (npr. šah) i **developed resource** u kojima razvijaš resurse s vremenom (npr. go, magic, starcraft).

Pri dizajnu turn based igara, treba imati na umu prednost koju ima prvi igrač, pogotovo u developed resource igrama. Ta prednost se nekom mehanikom mora nadoknaditi za drugog igrača, npr. `Go` daje 7.5 bodova crnom igraču.

## Dodavanje featurea

Kada dodaješ novu sposobnost za jednog lika, bitno je da ona bude uzbudljiva ne samo za korištenje, nego i za igrače na kojima će se koristiti. Npr. super jaki snajper je loš feature jer je smrt od snajpera prilično iritanta; ali jaki tenk koji mora slati male slabe tenkove u izvidnicu je odličan jer otvara nove taktike u igri.

## Ekonomija

Svako dodavanje novaca u ekonomiju stvara **inflaciju**. Novci u MMO-u dolaze iz monstera - svaki put kad se monster stvori, efektivno printaš novac. Svaki put kad ubiješ monstera, dobiješ zlato ili loot koji možeš prodati za zlato. A igrači zahtjevaju da se monsteri stalno spawnaju. Što MMO svijet duže postoji, to vrijednost zlatnika postaje sve manja.

Uobičajeno rješenje je stvoriti **money sinks** mehanike koje će izvlačiti novac iz svijeta (Runescape ima doslovnu rupu u koju možeš bacati novac). Neke od mehanika su:
* Kažnjavanje umiranja može značiti da ti se oprema nakon umiranja slomi i da moraš platiti njen popravak. Ovo najčešće nije dovoljno samo za sebe, ali uspješno je u Eve Online gdje developeri stalno huškaju nove ratove kako bi se uništilo što više skupih brodova.
* Razlika u cijeni predmeta kad ih prodaješ i kupuješ.
* Predmeti koji se konzumiraju (hrana, potioni, municija)
* Usluge (brže putovanje, liječenje, slanje poruka)

Ako stvoriš predmet koji je jako popularan (npr. potion koji će svatko htjeti imati prije raida), i učiniš ga dostupnim samo u trgovini, onda će njegova cijena fiksirati vrijednost valute, i valuta nikad neće postati bezvrijedna.

Ipak, nijedan money sink neće moći u potpunosti nadoknaditi količinu koju su igrači spremni grindati. Zato možeš koristi **reserve currency** kako bi povezao in-game novac s nečim što ima stvarnu vrijednost, i spriječio da mu vrijednost pada. Npr. u igru osim zlatnika možeš uvesti i dijamante, koji se mogu kupiti stvarnim novcem ili zlatnicima. Dijamantima možeš kupiti predmete iz storea, ili ih možeš zamjeniti za zlatnike; ali ne možeš dijamantima kupovati predmete od igrača, inače bi dijamanti zamijenili zlatnike. Dok god postoje predmeti koji se mogu kupiti samo sa zlatnicima, igrači će ih i dalje koristiti.
