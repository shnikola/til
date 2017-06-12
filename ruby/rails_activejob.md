# Background Jobs

## Delayed::Job

Zapisuje jobove u DB. U slučaju system failura, lako se mogu nastaviti. S druge strane, velik broj jobova može opteretiti DB.

`bin/delayed_job -n 2 start` pokreće deamon proces koji stvara 2 worker procesa za izvršavanje zapisanih jobova.

## Sidekiq

Zapisuje jobove u Redis. Redis drži podatke u memoriji pa je brži od DB, pa je dohvaćanje i obrada jobova brža. S druge strane, ako se sistem crasha između snapshotova, jobovi se mogu izgubiti.

`bundle exec sidekiq` pokreće proces koji obrađuje jobove, stvarajući thread za svakog workera. To troši manje memorije, ali pripazi da ti je kod threadsafe.

Sidekiq dolazi s ugrađenim dashboardom gdje možeš vidjeti stanje svih jobova.

## SuckerPunch

Zapisuje jobove u memoriju aplikacije. Svaki job ima svoj queue s poolom threadova koji će ih obrađivati. Ne zahtjeva nikakav dodatan dependency, ali je i najnesigurniji - bilo kakvo gašenje procesa će obrisati sve jobove. Zato ga koristi samo za non-mission-critical zadaće.


