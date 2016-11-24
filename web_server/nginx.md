# nginx

nginx radi kao proxy koji prosljeđuje requeste *upstream serverima*.
* `proxy_pass http://example.com` unutar matchanog `location` bloka, prosljeđuje request na dani proxy. Path koji slijedi nakon matchanog dijela dodaje se na url proxija.
* `Host` header se postavlja na `$proxy_host` (ime i port proxija), jer smo samo za taj host sigurni da Proxy odgovara.
* `proxy_set_header X-Real-IP $remote_addr` za postavljanje custom headera
* `$http_` headeri dolaznog requesta su dostupni kao varijable (npr `$http_host`)

* `upstream <pool_name> { server host1.example.com; server host2.example.com; }` definira pool servera kojem možemo proslijeđivati requeste s `proxy_pass http://<pool_name>`. Po default se koristi round-robin, ali može se promijeniti. svakom serveru se može dodati i `weight=3` koji definira koliko puta više requesta će primati.
* ako su klijenti spori, `proxy_buffering` omogućuje da nginx dohvati podatke od proxija i zatvori konekciju prije nego klijentu pošalje sve podatke. Može se podesiti s `proxy_buffers` i `proxy_buffer_size`. Ako imaš samo brze klijente, disablanje bufferinga će odmah flushati pakete klijentu.

config: `/etc/nginx/sites-available`
`listen 80 default_server` opcija hvata sve nematchane hostove (nemoj koristiti `server_name _;`)

# Literatura
https://www.nginx.com/resources/wiki/start/topics/tutorials/config_pitfalls/
