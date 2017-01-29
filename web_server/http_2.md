# HTTP/2

Najveći problem HTTP 1.1 je latencija. Koliki god bandwith imao, latenciju ne možeš smanjiti dok god šalješ request po request. Kako bi se izbjegla latencija, s vremenom su se razvile tehnike poput spritinga i inlininga imagea, spajanja script fileova, i shardinga hostova.

HTTP2 napravljen je da podržava sve postojeće funckionalnosti HTTP 1.1, ali da ukloni problem latencije i smanji broj potrebnih konekcija na pojedinom hostu.

Klijent može zahtjevati korištenje http2 protokola koristeći `Upgrade` header, ali to zahtjeva dodatni roundtrip. U slučaju da se koristi TLS, postoji ALPN protokol koji ubrzava taj dogovor.

HTTP2 ne zahtjeva TLS, ali trenutno ga svi browseri podržavaju samo s TLS-om.

## Protokol

HTTP2 je, za razliku od 1.1, binarni protokol. To omogućuje lakši framing i manje opcionalnog whitespacea. Format svakog framea je: `Length`, `Type`, `Flags`, `Stream ID` i payload.

## Streamovi

Svaka konekcija može sadržavati više paralelnih streamova. To omogućuje da se requesti multipleksiraju preko iste konekcije, odnosno da ista konekcija istodobno prima ili šalje više paketa. To čak vrijedi i za requestove iz različith tabova. `chrome://net-internals#http2` ispisuje listu trenutnih konekcija u browseru.

Svaki stream ima priority pomoću kojeg server ili klijent s ograničenim resursima mogu označiti koji streamovi su im važniji. Tako browser može dinamično mijenjati prioritet streamova kada korisnik promijeni tab ili scrolla po velikoj listi slika.

## Headeri

HTTP2 je i dalje stateless protokol, što znači da svaki request zahtjeva slanje headera koji su najčešće isti. Prednost HTTP2 je što koristi HPACK kompresiju kako bi headeri zauzimali manje.

Također, svi headeri su lowercase.

## Reset

U HTTP 1.1, jednom kad je `Content-Length` poslan, nemoguće je prekinuti poruku bez da se prekina cijela konekcija, što je skupo. HTTP2 podržava prekidanje poruke pomoću RST_STREAM framea bez da se prekine konekcija.

## Server Push

HTTP2 omogućuje da serveru da, ako šalje jedan resource, samostalno klijentu pošalje i drugi za koji vjeruje da će mu trebati. Klijent novi resource sprema u cache kako bi lako dohvatio kad mu bude trebao.

Klijent može odbiti push pomoću RST_STREAM.

# Literatura

* http://http2-explained.haxx.se/content/en
