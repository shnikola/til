# Api

REST koristi sljedeće HTTP metode:
* `GET /users` dohvaća popis usera.
* `GET /users/12` dohvaća određenog usera.
* `POST /users` stvara novog usera.
* `PUT /users/12` updatea postojećeg usera.
* `PATCH /users/12` djelomično updatea postojećeg usera.
* `DELETE /users/12` briše usera.

`PUT`, `POST` i `PATCH` neka vraćaju resurs kao i `GET`.

`HATEOAS` je koncept da se uz resurse vraćaju i endpointi za upravljanje tim resursima. Nema neke koristi od njega, jer u većini slučajeva je klijentu korisnije da unaprijed zna kojim endpointima raspolaže, i u kojim slučajevima će ih koristiti.

Koristi **JSON**, svi ga koriste. **CamelCase** je kao JSON standard, ali **snake_case** se dokazano lakše čita.

U responseu vraćaj **pretty print** po defaultu. Razlika u prometu je minimalna, a userima će biti drago.

Provjeri da li koristiš **gzip**, tu je većina uštede.

Za **paginaciju**, koristi `Link` header na sljedeću stranicu. Ako trebaš ukupan broj, koristi custom header, npr `X-Total-Count`.

Za **rate limiting** vraćaj:
* `X-Rate-Limit-Limit`, broj dozvoljenih requestova u trenutnom periodu.
* `X-Rate-Limit-Remaining`, broj preostalih requestova u trenutnom periodu.
* `X-Rate-Limit-Reset`, broj preostalih sekunda u trenutnom periodu.

Za **versioning**, stavi major verziju u path `/v1/tickets` (da se može isprobati iz browsera), a omogući mijenjanje minor verzija pomoću headera.

## Monitoring

**400 errore** koji dolaze unutar sustava znače da postoji pogreška u integraciji, i treba ih tretirati kao 500-ke.

# Literatura

* https://www.vinaysahni.com/best-practices-for-a-pragmatic-restful-api
