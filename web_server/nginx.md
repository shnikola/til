# nginx

nginx radi kao proxy koji prosljeđuje requeste *upstream serverima*.

`proxy_pass http://example.com` unutar matchanog `location` bloka, prosljeđuje request na dani proxy. Path koji slijedi nakon matchanog dijela dodaje se na url proxija.

`Host` header se postavlja na `$proxy_host` (ime i port proxija), jer smo samo za taj host sigurni da Proxy odgovara.

`proxy_set_header X-Real-IP $remote_addr` za postavljanje custom headera

`$http_` headeri dolaznog requesta su dostupni kao varijable (npr `$http_host`)

`upstream <pool_name> { server host1.example.com; server host2.example.com; }` definira pool servera kojem možemo proslijeđivati requeste s `proxy_pass http://<pool_name>`. Po default se koristi round-robin, ali može se promijeniti. svakom serveru se može dodati i `weight=3` koji definira koliko puta više requesta će primati.

Ako su klijenti spori, `proxy_buffering` omogućuje da nginx dohvati podatke od proxija i zatvori konekciju prije nego klijentu pošalje sve podatke. Može se podesiti s `proxy_buffers` i `proxy_buffer_size`. Ako imaš samo brze klijente, disablanje bufferinga će odmah flushati pakete klijentu.

Config za svaki site definiran je u `/etc/nginx/sites-available`.

`listen 80 default_server` opcija hvata sve nematchane hostove (nemoj koristiti `server_name _;`)

`sudo nginx -t` provjerava sintaksu config filea.

`sudo service nginx reload` iznova učitava konfiguraciju.

## Redirect

```
server {
  server_name .utorkom.com;
  return 301 https://www.srijeda.com$request_uri;
}
```

## nginx Reverse Proxy Cache

`nginx` ima svoj vlastiti ugrađeni proxy cache - nema potrebe za odvojenim rješenjima poput Varnisha.

U `nginx.conf` dodaj:
* `proxy_cache_path /var/lib/nginx/cache levels=1:2 keys_zone=backcache:8m max_size=50m`
  * `/var/lib/nginx/cache` definira directory u kojem će s držati cache (pripazi da postoji i da ga ownaš)
  * `levels` način organiziranja cachea po folderima (nebitno)
  * `keys_zone` definira ime zone, veličinu rezerviranu za keyeve (`8MB`) i maksimalnu veličinu za cache
* `proxy_cache_key "$scheme$request_method$host$request_uri$is_args$args"` način na koji se generira cache key.
* `proxy_cache_valid 200 302 10m` storea uspješne response i redirectove na 10 minuta, a `404 1m` na 1 minutu.

U konfiguraciju servera dodaj:
* `proxy_cache backcache` definira koji cache zone se koristi.
* `add_header X-Proxy-Cache $upstream_cache_status` dodaje header je bio cache hit ili miss.

Ako imaš baš jako veliki traffic, može se dogoditi *cache stampede*. Više istovremenih requestova počne dohvaćati resource koji je expirao pa cache prosljeđuje request na server za svakog (umjesto samo za jednog). Ovo se može izbjeći lockingom: `proxy_cache_lock on;` i `proxy_cache_use_stale updating;`

Na aplikacijskom serveru vraćaj `Cache-Control: public` samo za stranice koje ne koriste cookije.

# Literatura

* https://www.nginx.com/resources/wiki/start/topics/tutorials/config_pitfalls/
