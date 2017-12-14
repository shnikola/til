# API Design

## Versioning

S verzioniranjem kreni od starta kako bi izbjegao glavobolje u budućnosti. Zahtjevaj da svi klijenti šalju eksplicitnu verziju (ne dopuštaj defaultnu), najbolje u headeru, npr: `Accept: application/vnd.utorkom+json; version=3`.

`vnd.` je vendor prefix koji označava da je MIME type custom. `+json` znači da se koristi JSON format, ali je definirana dodatna semantika iznad JSONa.

## Status Codes

Uspješni request:
* `200 Success` za `GET`, `POST`, `PATCH`, `DELETE` koji se izvršio sinkrono.
* `201 Created` za `POST`, `PUT` koji se izvršio sinkrono i stvorio novi resource. Dodaj `Location` header s urlom novog resourca.
* `202 Accepted` za `POST`, `PUT`, `PATCH`, `DELETE` koji će se izvršiti asinkrono.
* `206 Partial Content` za `GET` čiji je odgovor podijeljen u dijelove, koristi `Range` header.

Neuspješni request:
* `401 Unauthorized` korisnik nije autentificiran.
* `403 Forbidden` korisniku nije dopušten pristup traženom resourcu.
* `422 Unprocessable Entity` request je shvaćen, ali sadrži invalid parametre.
* `429 Too Many Requests` rate limited, pokušaj kasnije.
* `500 Internal Server Error` skršio se server, provjeri site status ili prijavi issue.

## Requests

Koristi URL path za identifikaciju resursa, body za sadržaj, a headere za metapodatke.

Prihvaćaj serijalizirani JSON kao request body, vraćaj serijalizirani JSON kao response body. Minificiraj JSON response tako da makneš whitespace iz njega. Za human readable, možeš dodati podršku za `?pretty=1` parametar.

Bez iznimke zahtjevaj HTTPS pristup APIju. Ne radi automatski redirect, jer se time potiču neispravni klijenti. Umjesto toga, ne prihvaćaj connectione na port 80, ili vraćaj `403 Forbidden`.

Omogući referenciranje s unique atributima koji nisu id. Neka rade i `https://service.com/apps/97addcf0-c182` (id) i `https://service.com/apps/www-prod` (ime aplikacije).

Izbjegavaj nestanje `/orgs/{org_id}/apps/{app_id}/dynos/{dyno_id}`. Umjesto toga, drži resource u root pathu: `/orgs/{org_id}`, `/orgs/{org_id}/apps`, `/apps/{app_id}`, `/apps/{app_id}/dynos` itd.

## Responses

U svaki resource dodaj `created_at` i `updated_at` timestampove. Koristi UTC zonu, ISO8601 format, tj. `2012-01-01T12:00:00Z`.

Umjesto foreign keya (`user_id: 5`) uključi nestani resource u response (`user: { name: ... }`).

Velike response podijeli na više dijelova koristeći `Range` header.

Koristi `ETag` header u svim odgovorima za cachiranje.

U svaki response dodaj `Request-Id` header s UUID za lakše logiranje i debugiranje između servera i klijenta.

## Errors

Neka svaki error bude strukturiran jednako: `id` za programsko prepoznavanje, `message` za prikaz korisniku, i `url` za link na više informacija.

Npr. `{ id: 'rate_limited', message: "Account reached its API rate limit.", url: "https://docs.service.com/rate-limits"}`

## JSON Schema

JSON schema omogućuje definiranje API specifikacije kroz JSON.

`prmd` je alat koji pomaže s validacijom sheme i automatskim generiranjem dokumentacije.

# Literatura

* https://www.youtube.com/watch?v=d0m0jIzJfiQ (How To Design Deep Games with Jonathan Blow)
