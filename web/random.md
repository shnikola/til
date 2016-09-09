## data-uri
URI koji sadrži inline data. Koriste se kako bi se uštedio request.
Formata je `data:[<media type>][;base64],<data>`. (npr. `data:image/png;base64,iVBORw0KGgoAA...`)

# TODO
shared caches i https?


## Hoće li se poslati Referrer?
Browser gleda ovim redom:
1. `Referrer-Policy` http header
2. `<meta name="referrer" content="unsafe-url">` u headu
3. `referrerpolicy` html atribut
4. `noreferrer` html atribut
5. nasljeđivanje od parent contexta
