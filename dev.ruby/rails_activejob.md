# Background Jobs

## Librariji

**Delayed::Job** zapisuje jobove u DB. U slučaju system failura, lako se mogu nastaviti. S druge strane, velik broj jobova može opteretiti DB.

`bin/delayed_job -n 2 start` pokreće deamon proces koji stvara 2 worker procesa za izvršavanje zapisanih jobova.

**Sidekiq** zapisuje jobove u Redis. Redis drži podatke u memoriji pa je brži od DB, pa je dohvaćanje i obrada jobova brža. S druge strane, ako se sistem crasha između snapshotova, jobovi se mogu izgubiti.

`bundle exec sidekiq` pokreće proces koji obrađuje jobove, stvarajući thread za svakog workera. To troši manje memorije, ali pripazi da ti je kod threadsafe.

Sidekiq dolazi s ugrađenim dashboardom gdje možeš vidjeti stanje svih jobova.

**SuckerPunch** zapisuje jobove u memoriju aplikacije. Svaki job ima svoj queue s poolom threadova koji će ih obrađivati. Ne zahtjeva nikakav dodatan dependency, ali je i najnesigurniji - bilo kakvo gašenje procesa će obrisati sve jobove. Zato ga ne možeš koristiti u rake i scheduled taskovima. Koristi ga samo za non-mission-critical zadaće.

## Active Job

ActiveJob je univerzalni API za korištenje različitih background jobova. Svaki job ima zasebnu klasu s defiranom `perform` metodom, te se poziva s `perform_later` ili `perform_now`.

Slobodno možeš proslijediti cijeli objekt u `perform_later(model)`. ActiveJob će ga pretvoriti u GlobalID, i ako dođe do pogreške u deserijalizaciji, prijavit će grešku a job se neće retryati.

## Exceptions

Ako se exception dogodi prilikom izvršavanja ActiveJob-a, job će se discardati. Moraš eksplicitno navesti exceptione koji se trebaju retryati:
`retry_on Timeout::Error, wait: :exponentially_longer, attempts: 10`

Ako se exception dogodi prilikom izvršavanja `.delay` joba, DelayedJob će ga retryati 15 puta s eksponencijalnim odmakom.

Obje opcije imaju smisla u određenim slučajevima, samo treba biti svjestan koji koristiš.

## Mailers

`email_address_with_name("nikola@email.com", "Nikola Šantić")` će ispravno escapati sve znakove za email polje s imenom.

## Savjeti

Imaj na umu da `wheneverize` i slični cron gemovi pokreću cijeli Rails environment, što zna potrajati par minuta, pa nekoliko takvih jobova može pojesti CPU i memoriju. Zato uvijek pokušaj pisati light-weight skripte ili ih pozivati iz već pokrenutog background joba.
