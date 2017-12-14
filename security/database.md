# Database

## SQL injection metode

https://www.troyhunt.com/everything-you-wanted-to-know-about-sql

Tehnike SQL injectiona:
* `WHERE id = 1 OR 1=1` dohvaća sve podatke.
* `WHERE id = 1 UNION ALL SELECT password FROM user` dohvaća iz druge tablice.
* `WHERE id = 1 and select top 1 substring(name, 1, 1) from sysobjects ... = 'a'` pogađa prvo slovo imena tablica
* `WHERE id = 1; SHUTDOWN` gasi server
