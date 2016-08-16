## data-uri
URI koji sadrži inline data. Koriste se kako bi se uštedio request.
Formata je `data:[<media type>][;base64],<data>`. (npr. `data:image/png;base64,iVBORw0KGgoAA...`)

## window.opener
https://mathiasbynens.github.io/rel-noopener/
Kada se link otvara u novom tabu, taj site ima pristup tvom windowu s `window.opener`, čak i ako na različitoj domeni!
Pomoću toga napadač može redirectati originalni tab na phishing site bez da user primjeti.
`rel=noopener` na linku osigurava da će `window.opener` biti null. Usto i primjetno poboljšava performanse browsera (novi tab ne mora dijeliti isti thread.)
Lekcija: ne koristi `target=_blank` na linkovima koje unose useri.
