# Privacy

## Deniability and Duress

http://www.mit.edu/~specter/articles/17/deniability1.html

Novinar izlazi iz strane zemlje, i stražar na granici zahtjeva njegove passworde kako bi mu pregledao sve elektroničke naprave. U slučaju da mu ih ne pruži istući će ga gumenim crijevom (*Rubber Hose Cryptanalysis*).

Deniable shema omogućuje da mu da poseban password koji će mu prikazati samo fileove koje je novinar unaprijed odobrio da ih stražar smije vidjeti.

Ovakva shema mra biti dio OS-a, inače će stražaru biti sumnjivo što novinar ima instaliran program koji to omogućuje i opet ga istući.

## Search History Privacy

https://tonywebster.com/2017/03/minnesota-search-warrant-anyone-who-googled/

Lopov je ukrao $20.000 s osobnog računa tako što se lažno predstavio kao on i poslao presliku lažne putovnice sa slikom pronađenom na Google Searchu. Policija je Googlu uručila sudski nalog za search logovima svih korisnika iz tog grada koji su googlali žrtvino ime.

Na prvu to zvuči kao povreda privatnosti korisnika, ali pitanje je koliko se razlikuje od naloga za sigurnosnu snimku iz trgovine nasuprot opljačkane banke?

Razlika je što ljudi Google ne smatraju javnim mjestom poput trgovine, već privatnim mjestom gdje mogu držati stvari koje ne bi podijelili s javnosti.

Ali Google nije servis kojem se može vjerovati na taj način. Štoviše, on koristi tvoje podatke za analizu i prikaz reklama, i vrlo je moguće da desetci inženjera svakodnevno pristupaju tvojim podatcima. Zato uvijek postupaj s pretpostavkom da su podatci koje šalješ googlu javni.

## Ubercookie Fingerprint

http://ubercookie.robinlinus.com/
Koristi `AudioContext` i `getClientRects` da fingerprinta i razlikuje čak i identične uređaje.

## Behaviour profiling

Vjerovao ili ne, ali način na koji tipkaš i mičeš miša može se prilično precizno koristiti za autentifikaciju.
Postoje firme koje razvijaju to za security (npr. `BehavioSec`), ali problem je što se to jednako lako može koristiti za narušavanje privatnosti i trackanje korisnika. Stoga je ovaj lik napravio neku ekstenziju koja sjebe te prepoznavače.
