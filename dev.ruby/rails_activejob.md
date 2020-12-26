# Background Jobs

## Librariji

**Delayed::Job** zapisuje jobove u DB. U slučaju system failura, lako se mogu nastaviti. S druge strane, velik broj jobova može opteretiti DB.

`bin/delayed_job -n 2 start` pokreće deamon proces koji stvara 2 worker procesa za izvršavanje zapisanih jobova.

**Sidekiq** zapisuje jobove u Redis. Redis drži podatke u memoriji pa je brži od DB, pa je dohvaćanje i obrada jobova brža. S druge strane, ako se sistem crasha između snapshotova, jobovi se mogu izgubiti.

`bundle exec sidekiq` pokreće proces koji obrađuje jobove, stvarajući thread za svakog workera. To troši manje memorije, ali pripazi da ti je kod threadsafe.

Sidekiq dolazi s ugrađenim dashboardom gdje možeš vidjeti stanje svih jobova.

**SuckerPunch** zapisuje jobove u memoriju aplikacije. Svaki job ima svoj queue s poolom threadova koji će ih obrađivati. Ne zahtjeva nikakav dodatan dependency, ali je i najnesigurniji - bilo kakvo gašenje procesa će obrisati sve jobove. Zato ga ne možeš koristiti u rake i scheduled taskovima. Koristi ga samo za non-mission-critical zadaće.

## perform_later

Slobodno proslijedi cijeli objekt u `perform_later(model)` metodu. ActiveJob će ga pretvoriti u GlobalID, i ako dođe do pogreške u deserijalizaciji, prijavit će grešku a job se neće retryati.

## perform_now

Ako želiš da se job odmah izvrši, umjesto `UserJob.new.perform` koristi `UserJob.perform_now` jer će ispravno obaviti serijalizacije i housekeeping.

## Mailers

`email_address_with_name("nikola@email.com", "Nikola Šantić")` će ispravno escapati sve znakove za email polje s imenom.

## Savjeti

Ne stavljaj previše koda u ActiveJob, već u njima samo pozivaj service objekt - tako ih je puno lakše testirati.

Imaj na umu da `wheneverize` i slični cron gemovi pokreću cijeli Rails environment, što zna potrajati par minuta, pa nekoliko takvih jobova može pojesti CPU i memoriju. Zato uvijek pokušaj pisati light-weight skripte ili ih pozivati iz već pokrenutog background joba.
