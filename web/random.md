## Automatska provjera https certifikata
http://prefetch.net/code/ssl-cert-check
`cd ssl/certs; for pem in *.pem; do ssl-cert-check -a -x 15 -e admin@yourdomain.com -q -c $pem; done`

## data-uri
URI koji sadrži inline data. Koriste se kako bi se uštedio request.
Formata je `data:[<media type>][;base64],<data>`. (npr. `data:image/png;base64,iVBORw0KGgoAA...`)
