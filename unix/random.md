## Data exploration
http://www.drbunsen.org/explorations-in-unix
`(head -5; tail -5) < data` - ispisuje prvih 5 i zadnjih 5 linija podataka
`paste data-1 data-2 > agg-data` - spaja podatke u 2 stupca.

# Using usr/local
http://hivelogic.com/articles/using_usr_local/
`/bin` - ključni programi tipa `sh`. `/sbin` isto tako, samo za system management programe koje normalni user ne koristi
`/usr/bin` - normalni programi distribucije.
`/usr/local/bin` - programi koje si sam downlodao ili buildao. Prednost stavljanja programa ovdje je što ih system update *neće* overwriteati, pa će sve custom promjene ostati. Pri buildanju koristi `prefix=/usr/local`, a u `$PATH` dodaj `/usr/local/bin` i `/usr/local/sbin`
`/opt` - third party monolitski paketi (tipa Oracle)

# echo vs printf
Nemoj koristiti `echo` za ispisivanje nepoznatih varijabli (nekonzistentno ponašanje za `\n` i `-n`).
Umjesto njega koristi `printf`.

# Skraćeno kopiranje
`cp really_long_filename{,.orig}`

# --
`<cmd> --` označava kraj opcija. Korisno ako treba proslijediti file koji počinje s `-`, npr. `rm -- -file.txt`

# strings
`strings <files>` ispisuje printabilne stringove u binarnom fileu.
  * `-n 10` minimalna duljina stringa (defaultno `4`).