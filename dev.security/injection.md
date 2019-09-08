# Injection

## SQL injection

https://www.troyhunt.com/everything-you-wanted-to-know-about-sql

Tehnike SQL injectiona:
* `WHERE id = 1 OR 1=1` dohvaća sve podatke.
* `WHERE id = 1 UNION ALL SELECT password FROM user` dohvaća iz druge tablice.
* `WHERE id = 1 and select top 1 substring(name, 1, 1) from sysobjects ... = 'a'` pogađa prvo slovo imena tablica
* `WHERE id = 1; SHUTDOWN` gasi server

## OS Command Injection

Npr. `http://sensitive/something.php?dir=%3Bcat%20/etc/passwd`

Varijanta toga je *Local File Inclusion* gdje referenciraš file koji je na serveru, npr. `http://vulnerable_host/preview.php?file=../../../../etc/passwd`.

Kada validiraš userove URL-ove, dopusti samo `http://` i `https://` inpute. Inače ti napadač može poslati `file:///etc/passwd`.
