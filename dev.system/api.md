# Api

## REST

REST koristi sljedeće HTTP metode:
* `GET /users` dohvaća popis usera.
* `GET /users/12` dohvaća određenog usera.
* `POST /users` stvara novog usera.
* `PUT /users/12` updatea postojećeg usera.
* `PATCH /users/12` djelomično updatea postojećeg usera.
* `DELETE /users/12` briše usera.

`PUT`, `POST` i `PATCH` neka vraćaju resurs kao i `GET`.

`HATEOAS` je koncept da se uz resurse vraćaju i endpointi za upravljanje tim resursima. Nema neke koristi od njega, jer u većini slučajeva je klijentu korisnije da unaprijed zna kojim endpointima raspolaže, i u kojim slučajevima će ih koristiti.

## Response

Koristi JSON, svi ga koriste. **CamelCase** je kao JSON standard, ali **snake_case** se dokazano lakše čita.

U responseu vraćaj **pretty print** po defaultu. Razlika u prometu je minimalna, a userima će biti drago.

Provjeri da li koristiš **gzip**, tu je većina uštede.

Za **paginaciju**, koristi `Link` header na sljedeću stranicu. Ako trebaš ukupan broj, koristi custom header, npr `X-Total-Count`.

## Authentication

Bez iznimke zahtjevaj HTTPS pristup APIju. Ne radi automatski redirect, jer se time potiču neispravni klijenti. Umjesto toga, ne prihvaćaj connectione na port 80, ili vraćaj `403 Forbidden`.

Što jednostavnija autentifikacija, to bolja. Za svakog korisnika izgeneriraj random ključ od 16+ znakova i zapiši ga u bazu. Neka ga šalju sa svakim requestom u `Authorization: Bearer` headeru. Klijenti neka ga čuvaju u cookiju (sigurniji su od local storagea).

U rijetkom slučaju kada je preskupo za svaki request raditi upit u bazu (uzmi u obzir da gotovo uvijek ionako radiš upit da dohvatiš usera), koristi stateless token kao JWT.

## Rate Limiting i Rate Throttling

Rate limiting je server side koncept - server ograničava koliko requestova klijent može napraviti u određenom intervalu. Preduvjet je da možemo **identificirati klijenta** za što koristimo API key, token ili, ako je u pitanju public API, IP adresu.

Rate throttling je client side koncept - klijent ograničava broj requesta koji šalje kako bi izbjegao `429 Too Many Requests`.

Postoje različite strategije rate limitinga.
**Leaky bucket** koristi ako želiš ograničiti trošenje resursa servera. Koristi FIFO queue ograničene veličine, a requesti koji dođu kad je queue popunjen će biti odbačeni. Nedostatak je što može dovesti do blokiranja queuea sa sporim requestovima, gdje se nijedan novi neće moći izvršiti.

**Fixed Window** daje svakom svakom klijentu fiksan broj requestova po vremenskom prozoru, npr. 1000 dnevno. Nedostatak je što i dalje dozvoljava da netko napravi svih 1000 requestova odjednom na početku prozora, (još gore ako svi klijenti čekaju na početak prozora) i zaguši server.

**Sliding Window** je strategija u kojoj bucket podijelimo na više manjih intervala, i onda sumiramo zadnjih `n` intervala. Na taj omogućujemo uniformniju raspodjelu requestova.

Server vraća sljedeće headere:
* `X-Rate-Limit-Limit`, broj dozvoljenih requestova u trenutnom periodu.
* `X-Rate-Limit-Remaining`, broj preostalih requestova u trenutnom periodu.
* `X-Rate-Limit-Reset`, broj preostalih sekunda u trenutnom periodu.

## Versioning

Stavi major verziju u path `/v1/tickets` (da se može isprobati iz browsera), a omogući mijenjanje minor verzija pomoću headera.

## Errors

Neka svaki error bude strukturiran jednako: `id` za programsko prepoznavanje i `message` za prikaz korisniku.

## Monitoring

**400 errore** koji dolaze unutar sustava znače da postoji pogreška u integraciji, i treba ih tretirati kao 500-ke.

# Literatura

* https://www.vinaysahni.com/best-practices-for-a-pragmatic-restful-api
