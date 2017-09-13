# Random tips

## Rails Console

`app` - trenutni session instance, ima `app.get('/projects/6')` i `app.project_path(Project.first)`
`helper` - svi view helperi, npr. `helper.link_to` i `helper.truncate`

## JSON API Schema

Kako validirati JSON podatke koji dolaze u API? strong_params su meh - ne provjeravaju tip, kod se drži u controlleru. Loš data ne bi uopće trebao doću do app layera. Postoji format definiranja ulaznih (i izlaznih) podataka koji se zove JSON Schema. Todo: Pogledaj njegove integracije u railsu.

## Constantize security

Nikad ne koristi `constantize` na stringovima koji su user input, jer to omogućava remote code execution (pomoću `Logger` klase).

## Rails Journey Router

https://www.youtube.com/watch?v=5hKkkQMOj3c

Railsov novi router je lud, izgenerira cijeli DFA (deterministic finite automata) i radi puno bolje nego niz ifova s regexima.
