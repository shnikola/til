# HTTP

Rubijev stdlib je odličan, s iznimkom šugavog `Net::HTTP` librarija. On koristi verbozan i nekonzistentan API, s mnogo low level detalja kojima se developer mora gnjaviti. Izbjegavaj ga koristiti ako ne moraš.

## HTTP.rb

Pure ruby HTTP klijent je odličan u svojoj jednostavnosti:
`HTTP.post("https://example.com/path", form: params)` šalje request.

`HTTP.headers("Accept" => "application/json").get(...)` postavlja headere requesta.
`HTTP.basic_auth("nikola", "secret").get(...)` postavlja autentifikaciju.
`HTTP.timeout(connect: 3, write: 3, read: 3)` detaljne postavke za timeout.
Postavke se mogu chainati: `HTTP.headers(...).timeout(...).get(...)`
`HTTP.use(:auto_deflate)` gzipa request body.
`HTTP.use(:auto_inflate)` unzipa response body.

`HTTP.persistent("https://example.com") do |http|` otvara perzistiranu konekciju i svi upiti unutar bloka je koriste (npr. `http.get("/")`).

`response.status.code` vraća status integer.
`response.status.ok?` provjerava je li request uspješan.
`response.headers.to_h` vraća hash s headerima.
`response.body.to_s` dohvaća cijeli body.

Streaming omogućava konstantu potrošnju memorije čak i pri uploadu i downloadu velikih podataka.

Streaming upload radi automatski za IO objekte, npr:
`HTTP.post(url, body: File.open("path/to/file.txt"))` streama file.
`HTTP.post(url, body: IO.popen("shell command"))` streama pipe.

Streaming download response bodyja je također moguć:
`response.body.readpartial` čita chunk odgovora.
`response.body.each { |chunk| ...}` dohvaća chunk po chunk.
