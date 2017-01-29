# Server Management

## TODO
supervise
chef i puppet za automatsku konfiguraciju servera

## Essential Security

Dovoljna su 2 accounta: `root` i `deploy`. Svi developeri se logiraju u `deploy` pomoću public keyeva. S `passwd` promijeni root password u nešto dugo i složeno. Ne trebaš pamtiti, samo ga spremi na sigurno, ako izgubiš `sudo` password `deploy` usera.

Koristi `apt-get update` i `apt-get upgrade`.

Napravi `apt-get install fail2ban`, dobro iskonfiguriran out of the box.

Iskonfiguriraj SSH s `nano /etc/ssh/sshd_config`:
* postavi `PermitRootLogin no` i `PasswordAuthentication no`
* postavi port na neki visoki broj (npr. `Port 20022`) da izbjegneš port sniffere.
* ako želiš ograničiti na IP, `AllowUsers deploy@<ip>`.
Na kraju, `sudo service ssh restart`.

`ufw` za jednostavni firewall. `ufw allow 22` (dodaj `from <ip>` ako treba), `ufw allow 80`, `ufw allow 443`. Na kraju, `ufw enable`

`papertrail` za praćenje logova.

## 12 Factor App

https://12factor.net/

Metodologija za deployment aplikacija koja omogućuje automatizaciju, portabilnost i skalabilnost.

Jedna aplikacija ima točno jedan codebase (repository). Aplikacija može imati više pokrenutih instanci (deployeva) s različitim branchevima codebasea (npr. staging), ali sve pripadaju istom codebaseu.

Dependecies moraju biti eksplicitno deklarirani i izolirani. Aplikacija ne smije očekivati da u sustavu postoji instaliran dependency (declaration), niti dopustiti da implicitni dependeciji "leakaju" iz sustava u aplikaciju (isolation). U Railsu, `Gemfile` je zadužen za deklaraciju, a `bundle exec` za izolaciju. Ovakav pristup također olakšava početni setup novim developerima.

Konfiguracija se drži u environmentu. Konfiguracija je sve ono što se razlikuje između deployeva, npr. lokacija DBa, credentialsi za AWS i sl. Konfiguracija ne bi smjela biti u kodu, već u environment varijablama. ENV varijable ne bi smjele biti grupirane za staging, produkciju i sl., već biti potpuno neovisne jedna od druge.

Deployment razlikuje build, release i run korake. Build dohvaća kod i dependecije, te kompajlira binarije i assete; Release kombinira stvoreni build s konfiguracijom; Run pokreće tako postavljenu aplikaciju. Build će se pozvati svaki put kada se gurne promjena koda, release kada se promijeni konfiguracija, a run svaki put kad se restartira server.

Lokalne servise (MySQL, Redis, RabbitMQ) treba tretirati kao bilo koji eksterni resurs (NewRelic, S3, Sendgrid). Spajaj se na njih preko URLa, pa ih neće biti problem zamijeniti third party servisom (npr. Amazon RDSom) ili backupom bazom u slučaju corruptane baze.

Aplikacija pruža HTTP tako što se binda na port na kojem osluškuje requestove. Server requestove koje prima routa na taj port.

Aplikacija se pokreće kroz stateless procese. Aplikacijski procesi su potpuno neovisni i ne dijele ništa, te aplikacija ne pretpostavlja da sistemska naredba (npr. skidanje datoteke lokalno da disk) ostavlja posljedicu koju drugi procesi mogu iskoristiti.

Aplikacija se lako skalira dodavanjem novih procesa kad procesi ne dijele ništa. Developer može dodijeliti uloge svakom procesu, npr. za serviranje HTTP requestova, za obavljanje background i scheduled jobova. Aplikacija ne smije biti zadužena za pisanje PID fileova ili daemoniziranje. Time se treba baviti proces manager OS-a, npr. Upstart ili Foreman zadužen za handlanje outputa, crashanih procesa i korisničkih restarta.

Administrativne naredbe ako migracija baze ili pokretanje jednokratnih skripti pokreću se u one-off procesima u environmentu identičnom ostalima.

Procesi su disposable, što znači da se mogu instatno paliti i gasiti, što olakšava skaliranje, deployment i mijenjanje konfiguracije. Zato bi procesi trebali imati što kraći startup time (oko par sekundi) i gracefully se ugasiti po primitku SIGTERM signala.

Za web procese, graceful gašenje postiže se prestankom slušanja porta kako se novi requesti ne bi primali, i dovršavanjem trenutnih, te gašenjem procesa. Za worker procese, postiže se vraćanjem trenutnog joba nazad u queue.

Development i production environment trebali bi biti što je moguće sličniji. To znači da se isti backing servisi koriste na lokalnim i produkcijskim strojevima, da isti ljudi developaju i deployaju.

Aplikacija se ne bi smjela brinuti za management logfileova. Umjesto toga, treba ispisivati logove na `stdout`, a o sakupljanju svih logova i slanju na konačno odredište treba se brinuti serverski environment. Tako se logovi mogu zapisivati u file, ili slati u neki agregacijski sustav kao Splunk ili Hadoop.


## Automatska provjera https certifikata

http://prefetch.net/code/ssl-cert-check

`cd ssl/certs; for pem in *.pem; do ssl-cert-check -a -x 15 -e admin@yourdomain.com -q -c $pem; done`

## Continuous Integration

https://circleci.com/

Kad pushash kod, on automatski povuče to na svoj server, i pokrene sve testove.
Čak možeš podesiti da ga deploya na Heroku ako su svi testovi prošli. Jer si lijena guzica.
