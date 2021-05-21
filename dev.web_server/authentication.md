# API Authentication

## API Key

API key je najjednostavniji oblik API authenticationa. Preporuča se slati u headeru `Authorization: Apikey 1234567890abcdef`.

## OAuth 2.0

OAuth je protokol preko koje jedna aplikacija (**klijent**) može dobiti dopuštenje da koristi korisnikov račun druge aplikacije (**resource server**). Pritom korisnik klijentu ne mora dati svoj password ili ikakvu tajnu informaciju. Autentifikaciju radi **authorization server** koji je najčešće isti kao resource server.

Prednost OAutha nad API keyem je dopušta ograničavanje pristupa na određene scopove. Također koristi expirable tokene, pa će ukradeni token raditi relativno kratko.

### OAuth Authorization Code Grant

Uobičajeni OAuth flow kada user koristi browser.

1) Klijent redirecta browser na auth server s parametrima:
* `response_type: "code"`,
* `client_id` je identifikator klijenta,
* `redirect_uri` je URL na klijentu na koji će dobiti callback,
* `scope` s listom scopova odijeljenim razmakom,
* `state` je CSRF token

2) User se ulogira, prihvati scopove, auth server na `redirect_uri` šalje sljedeće:
* `code` s **auth codeom**
* `state` vraća primljeni CSRF token da dokaže kako je primio originalni request.

3) Klijent sada serverski šalje request na auth server s parametrima:
* `grant_type: "authorization_code"`
* `client_id` je identifikator klijenta,
* `client_secret` je secret klijenta (smije se slati samo serverski),
* `redirect_uri` je isti URL od prvog requesta.
* `code: <auth_code>`

4) Server odgovara s JSON objektom:
* `token_type` je najčešće `Bearer`
* `access_token` s **access tokenom**
* `expires_in` je broj sekunda koliko će access token još trajati
* `refresh_token` s **refresh tokenom**

Klijent sad može koristiti `access_token` da radi serverske requeste na resource API i dohvati npr. podatke o useru. Može i mijenjati korisnikov račun ako mu `scope` to dopušta.

Malo je čudno da se prvo vraća **auth code** a ne **access token**, ali ideja je da se access tokeni sakriju od krajnjeg korisnika. Access code je jedini koji se vidi u URL-u callbacka, a ostatak requestova se obavlja serverski.

Access token je napravljen da traje kratko (defaultno 10 minuta), pa ako ga netko ukrade, neće ga moći dugo koristiti. Jednom kad access token istekne, server koristi **refresh token** da bi dohvatio novi access token. Refresh token isto može isteći, ali to baš nije precizno definirano, i dosta providera ga ne expiraju. Ako netko ukrade refresh token, beskorisan je jer mu treba i **client secret** za dobijanje accessa.

