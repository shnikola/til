# 12 Factor App

https://12factor.net/

Metodologija za deployment aplikacija koja omogućuje automatizaciju, portabilnost i skalabilnost.

Jedna aplikacija ima točno jedan codebase (repository). Aplikacija može imati više pokrenutih instanci (deployeva) s različitim branchevima codebasea (npr. staging), ali sve pripadaju istom codebaseu.

Dependecies moraju biti eksplicitno deklarirani i izolirani. Aplikacija ne smije očekivati da u sustavu postoji instaliran dependency (declaration), niti dopustiti da implicitni dependeciji "leakaju" iz sustava u aplikaciju (isolation). U Railsu, `Gemfile` je zadužen za deklaraciju, a `bundle exec` za izolaciju. Ovakav pristup također olakšava početni setup novim developerima.

Konfiguracija se drži u environmentu. Konfiguracija je sve ono što se razlikuje između deployeva, npr. lokacija DBa, credentialsi za AWS i sl. Konfiguracija ne bi smjela biti u kodu, već u environment varijablama. ENV varijable ne bi smjele biti grupirane za staging, produkciju i sl., već biti potpuno neovisne jedna od druge.

Deployment razlikuje build, release i run korake. Build dohvaća kod i dependecije, te kompajlira binarije i assete; Release kombinira stvoreni build s konfiguracijom; Run pokreće tako postavljenu aplikaciju. Build će se pozvati svaki put kada se gurne promjena koda, release kada se promijeni konfiguracija, a run svaki put kad se restartira server.

Lokalne servise (MySQL, Redis, RabbitMQ) treba tretirati kao bilo koji eksterni resurs (NewRelic, S3, Sendgrid). Spajaj se na njih preko URLa, pa ih neće biti problem zamijeniti third party servisom (npr. Amazon RDSom) ili backupom bazom u slučaju corruptane baze.

Aplikacija se binda na port na kojem osluškuje requestove. Server requestove koje prima routa na taj port.

Aplikacija se pokreće kroz stateless procese. Aplikacijski procesi su potpuno neovisni i ne dijele ništa, te aplikacija ne pretpostavlja da sistemska naredba (npr. skidanje datoteke lokalno da disk) ostavlja posljedicu koju drugi procesi mogu iskoristiti.

Aplikacija se lako skalira dodavanjem novih procesa kad procesi ne dijele ništa. Developer može dodijeliti uloge svakom procesu, npr. za serviranje HTTP requestova, za obavljanje background i scheduled jobova. Aplikacija ne smije biti zadužena za pisanje PID fileova ili daemoniziranje. Time se treba baviti proces manager OS-a, npr. Upstart ili Foreman zadužen za handlanje outputa, crashanih procesa i korisničkih restarta.

Administrativne naredbe poput migracije baze ili pokretanja jednokratnih skripti pokreću se u one-off procesima u environmentu identičnom ostalima.

Procesi su disposable, što znači da se mogu instatno paliti i gasiti, što olakšava skaliranje, deployment i mijenjanje konfiguracije. Zato bi procesi trebali imati što kraći startup time (oko par sekundi) i gracefully se ugasiti po primitku SIGTERM signala.

Za web procese, graceful gašenje postiže se prestankom slušanja porta kako se novi requesti ne bi primali, dovršavanjem trenutnih requestova, te gašenjem procesa. Za worker procese, postiže se vraćanjem trenutnog joba nazad u queue.

Development i production environment trebali bi biti što je moguće sličniji. To znači da se isti backing servisi koriste na lokalnim i produkcijskim strojevima, da isti ljudi developaju i deployaju.

Aplikacija se ne bi smjela brinuti za management logfileova. Umjesto toga, treba ispisivati logove na `stdout`, a o sakupljanju svih logova i slanju na konačno odredište treba se brinuti serverski environment. Tako se logovi mogu zapisivati u file, ili slati u neki agregacijski sustav kao Splunk ili Hadoop.
