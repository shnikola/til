# Server Push

HTTP streaming:
  * Chunked transfer encoding
  * `multipart/x-mixed-replace`
  * WebSocket
  * Server-sent events

Pushlet (Java)
Long polling

HTTP/2 Webpush protokol

## WebSockets, Caution Required!
https://samsaffron.com/archive/2015/12/29/websockets-caution-required
* Za realtime aplikacije ne trebaš websockete, trebaš pouzdan način za slati podatke sa servera.
* Odaberi framework koji će ti omogućiti korištenje više protokola s fallbackom: `socket.io`, `signalR`, `Nchan`, `message_bus` za Ruby.
* Umjesto Websocketa preferiraj: long-polling with chunked encoding, long-polling i polling
* Koristi HTTPS i HTTP/2 za realtime backend.
* Use caseovi u kojima su websocketi opravdani: gaming i SSH preko weba. 
