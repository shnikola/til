## Data exploration
http://www.drbunsen.org/explorations-in-unix
`(head -5; tail -5) < data` - ispisuje prvih 5 i zadnjih 5 linija podataka
`paste data-1 data-2 > agg-data` - spaja podatke u 2 stupca.

# Using usr/local
http://hivelogic.com/articles/using_usr_local/
Kad se eksterni disk spoji na računalo, mounting se dogodi automatski, a lista mounting pointova nalazi se u direktoriju `/mnt` (`/Volumes` za OSX)
`/usr/local` - programi koje si sam downlodao ili buildao. Prednost stavljanja programa ovdje je što ih system update *neće* overwriteati, pa će sve custom promjene ostati.
Pri buildanju koristi `prefix=/usr/local`, a u $PATH dodaj `/usr/local/bin` i `/usr/local/sbin`
