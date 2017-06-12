# Requests

## XMLHttpRequest

XHR omogućuje asinkronu komunikaciju između klijenta i servera, bez reloadanja stranice za svaki request. Browser se brine za low level detalje poput managementa konekcija, dogovora protokola, cachinga, redirectova i sl.

`var xhr = new XMLHttpRequest()` stvara request objekt.
`xhr.open('GET', '/auth/user.json')` postavlja metodu i url.
`xhr.responseType = 'json'` definira kako će se dekodirati body responsa. Ako se ne postavi, browser će koristiti content type responsa.

`xhr.send()` šalje request bez bodija.
`xhr.send("text string")` šalje tekst kao body.
`xhr.send(file)` šalje file kao raw blob.
`xhr.send(formData)` za slanje forme. `formData = new FormData(); formData.append('id', 2)`.

`xhr.timeout = 5000` postavlja timeout requesta na 5s. Defaultno nema timeouta.
`xhr.addEventListener('load', ...)` postavlja callback za uspješni request.
`xhr.addEventListener('error', ...)` postavlja callback za neuspješni request.
`xhr.addEventListener('progress', ...)` postavlja callback za download progress.
`xhr.upload.addEventListener('progress', ...)` postavlja callback za upload progress.
Postotak progressa se može izračunati unutar callbacka ako je `event.lengthComputable`, kao `event.loaded / event.total`.

### XHR Streaming

XHR ne podržava streaming, ali radi se na tome da ga se integrira sa Streams API. Umeđuvremenu, postoje djelomična rješenja.

Za upload, možeš slati file u chunkovima pomoću više `xhr.send` poziva i `Content-Range` headera. Na taj se način može i napraviti retry neuspješno uploadanog chunka bez da se cijeli file iznova uploada.

Za download, u `readystatechange` callbacku možeš provjeriti da li se response počeo dohvaćati (`readyState > 2`) i dohvaćati podatke kako stižu `newData = xhr.responseText.substr(xhr.seenBytes)`, `xhr.seenBytes = xhr.responseText.length`. Nažalost, ovo je ograničeno samo na text podatke, te se moraš brinuti za odijeljivanje poruka u streamu. Također, iako pristupaš poruci u chunkovima, i dalje se cijeli response drži u memoriji, što može biti problem za streamanje velikih fileova.

### Polling

Najjednostavniji način za dobiti update podataka sa servera je pomoću XHR pollinga. Periodički se (npr. svakih 60 sekundi) šalje XHR request koji dohvaća sve promjene koje su se dogodile na serveru. Nedostatak ove metode je  delay u prikazu podataka - update se može dogoditi npr. odmah nakon obavljenog requesta, što će klijent saznati tek 60 sekundi kasnije. Smanjenje perioda pollinga će smanjiti delay, ali i povećati promet na serveru.

Za eliminaciju delaya koristi se *long-polling*. Klijent napravi XHR request na kojeg server ne odgovori odmah, već drži konekciju dok se update ne dogodi, i tek onda mu pošalje odgovor. Kad klijent primi odgovor, odmah radi idući long poll request i čeka idući update. Ovime se eliminirao delay između servera i klijenta.

U slučaju da server generira puno updateova, long polling će biti generirati mnogo više requestova nego što bi polling slao. U tom slučaju je bolje koristiti polling s mehanizmom agregacije updateova na serveru.

### Fetch API

Omogućuje elegantnije dohvaćanje resourca koristeći promise: `fetch('flowers.jpg', options).then(response => ...).catch(err => ...)`. Dodatno, za razliku od XHR podržava streaming download.

Request opcije mogu definirati: `method`, `url`, `referrer`, `mode` (`cors`, `no-cors`, `same-origin`), `credentials` (`omit`, `same-origin`). Headeri se dodaju s `headers: new Headers({ ... })`. Za slanje podataka koristi `body: new FormData(...)` ili `body: JSON.stringify(...)`.

Response objekt proslijeđen u `then` sadrži propertije: `headers`, `status`, `statusText`. `response.body` je ReadableStream i omogućuje streamano dohvaćanje podataka, bez da se cijeli response mora držati u memoriji. Drugi načini za dohvaćanje podataka su stream readeri koji čekaju da se cijeli response učita: `response.arrayBuffer()`, `response.blob()`, `response.json()`, `response.text()`, `response.blob()`.

Imaj na umu da se `catch` blok događa samo u slučaju da se dogodi greška s konekcijom. Statusi `500`, `400` i sl. će se izvoditi u `then` bloku.

## Server-Sent Events (SSE)

Server sent events omogućuju učinkovito slanje tekstualnih poruka sa servera klijentu. Klijent stvara konekciju šaljući XHR request s `Accept: text/event-stream`, a server mu odgovara `Content-Type: text/event-stream` i u nastavku streama poruke. Format poruke je iznimno jednostavan: sadržava liniju koja počinje s `data: ` s podatcima, a dodatno može sadržavati `id:` liniju i `event:` liniju s tipom poruke.

Klijent koristi browserov Event Source API koji za njega obavlja uspostavu konekciju i parsiranje odgovora.

`source = new EventSource("/path/to/stream-url")` otvara novu konekciju.
`source.addEventListener("open", ...)` callback kad je konekcija stvorena.
`source.addEventListener("error", ...)` callback kad konekcija faila.
`source.addEventListener("message", e => e.data)` callback za svaku poruku koja stigne.
`source.addEventListener("foo", e => e.data)` callback za poruku tipa `foo`.
`source.close()` zatvara konekciju.

Event Source u slučaju prekida konekcije automatski obavlja reconnect. S klijenta šalje zadnji primljeni ID tako da server može ponovno poslati sve neprimljene poruke, ili samo nastaviti odašiljati ukoliko su izgubljene poruke prihvatljive.

Prednosti SSE-a su mala latencija i minimalni overhead zahvaljujući konstantnoj konekciji, te apstrakcija Event Source APIja koja omogućava da primaš poruke kao DOM eventove. Ograničenja su što podržava samo komunikaciju od servera prema klijentu, pa ne omogućuje streaming upload. Drugo, SSE je dizajniran da prenosi UTF-8 tekst. Binarni promet je moguć preko Base64 encodinga, ali uz 33% povećanje prometa nije učinkovit.

## WebSocket

Websocketi omogućuju dvosmjerni streaming tekstualnih i binarnih podataka između klijenta i servera. API omogućuje rad skoro na razini socketa, ali i dalje za tebe obavlja low-level zadatke poput uspostave konekcije i framinga poruka.

`ws = new WebSocket('wss://example.com/socket')` otvara novu websocket konekciju.
`ws.addEventListener("open", ...)` callback kad je konekcija stvorena.
`ws.addEventListener("close", ...)` callback kad je konekcija zatvorena.

`ws.addEventListener("message", msg => msg.data)` primanje poruka od servera. Ako se šalje 1MB, callback će biti pozvan tek kad je cijela poruka dostupna na klijentu.

`ws.send("Hello")` slanje tekstualne poruka na server. Slanje se izvršava asinkrono, tj. funkcija vraća rezultat prije nego se poruka pošalje. `ws.bufferedAmount` pokazuje koliko podatka za slanje još čeka u browseru.

Poruke se šalju redom kojim su queueane na klijentu. Multipleksiranje nije podržano, pa će velika poruka blokirati slanje mnogo malih iza sebe. U slučaju slanja većih poruka preporučljivo ih je podijeliti u više manjih kako bi se izbjegao head-of-line blocking.

Kao i SSE, WebSocket omogućuje manju latenciju i overhead u odnosu na XHR zahvaljujući stalnoj konekciji. Usto je jedini protokol koji omogućuje dvosmjeru komunikaciju između klijenta i servera preko iste TCP konekcije. Imaj na umu da je `ws` zaseban protokol i browser ne može koristiti cachiranje, kompresiju i ostale prednosti HTTP-a. Pripazi, za razliku od HTTP/2, WebSocketi otvaraju novu konekciju za svaki tab.

Kao i u slučaju drugih modernih protokola, preporuča se korištenje web socketa preko TLS-a (`wss`) kako bi se eliminiralo prtljanje raznih proxija po tvojim paketima. Usto, treba podesiti vlastite servere i load balancere da podržavaju dugoživeće konekcije.

### Opinion: WebSockets, Caution Required!

https://samsaffron.com/archive/2015/12/29/websockets-caution-required

Za realtime aplikacije ne trebaš websockete, trebaš pouzdan način za slati podatke sa servera. Umjesto websocketa koji unose još jedan kanal slanja podataka, provjeri da li su za tvoju aplikaciju dovoljni long-polling ili čak obični polling svakih 30 sekundi.

Za SSE ili long-polling u Rubyju koristi `message_bus` koji je odličan.

Use caseovi u kojima su websocketi opravdani: gaming i SSH preko weba.

## WebRTC

WebRTC omogućuje peer-to-peer dijeljenje audia, videa ili podataka između browsera. Browser omogućuje različite APIje: `MediaStream` za dohvaćanje video i audio streamova, `RTCPeerConnection` za dijeljenjea audio i video podataka, `RTCDataChannel` za dijeljenje aplikacijskih podataka.

Browser se automatski brine za voice i video engine, uključujući noise reduction, echo cancellation, encodiranje i sinkronizaciju. Aplikacija preko `navigator.getUserMedia` direktno dobija optimizirani media stream kojeg prosljeđuje preko WebRTCa.

WebRTC koristi UDP, DTLS (datagram secure layer), i još puno protokola za signaliziranje, uspostavu konekcije i prijenos podataka. Muka živa, i to samo za 1-na-1 veze. Ako imaš više od dvoje ljudi, stvari tek onda postaju problematične, i često se uvodi centralni proxy server preko kojeg se svi spajaju kako bi se uštedilo na bandwithu.

