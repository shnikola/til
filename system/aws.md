# Amazon Web Services

## Elastic Beanstalk

Koristi se za deployanje aplikacija na unaprijed konfigurirani EC2 stroj, s instaliranim OS-om i Rubijem određene verzije.

`eb init` lokalno inicijalizira aplikaciju. Stvara se `.elasticbeanstalk/config.yml` u kojem je lokalna konfiguracija koja se ne pusha na git.

`eb create project-production` stvara novi environment aplikacije koji se šalje na server i pokreće.
`eb list` ispisuje sve postojeće environmente.

`eb use project-staging` odabire trenutni lokalni environment.
`eb status` ispisuje detalje o trenutnom environmentu.

`eb setenv SECRET_KEY_BASE=...` postavlja env varijble u trenutnom environmentu.

`eb deploy` šalje lokalni kod (ili, ako je git postavljen, koristi `git archive` zadnjeg commita) u trenutni environment aplikacije.
`eb deploy project-staging` za deployanje na određeni environment.

## CLI

Najlakši način za handlanje više AWS računa na istom stroju je pomoću `aws configure --profile project-name` za svaki projekt.

Da bi koristio određeni profile, pozivaj naredbe iz konzole s `--profile` opcijom. Za odabir profila u project directoriju, neki servisi imaju svoj config file, npr. `.elasticbeanstalk/config.yml`.

`--region` u naredbi dopušta da odabereš različiti region za odabrani profile.

Konfiguracija se gleda po sljedećem redu:
1. `--profile` opcija u naredbi
2. `AWS_ACCESS_KEY_ID` i `AWS_SECRET_ACCESS_KEY` env varijable.
3. `~/.aws/credentials` credentials file
4. `~/.aws/config` config file

## SDK

SDK će automatski koristiti tvoj defaultni profil. Ako želiš koristiti neki drugi, koristi `AWS_PROFILE` env varijablu.

# Literatura

* https://www.expeditedssl.com/aws-in-plain-english
* https://github.com/open-guides/og-aws

