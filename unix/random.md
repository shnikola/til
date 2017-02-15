## Data exploration

http://www.drbunsen.org/explorations-in-unix

`(head -5; tail -5) < data` - ispisuje prvih 5 i zadnjih 5 linija podataka
`paste data-1 data-2 > agg-data` - spaja podatke u 2 stupca.

## Using usr/local

http://hivelogic.com/articles/using_usr_local/

`/bin` - ključni programi tipa `sh`. `/sbin` isto tako, samo za system management programe koje normalni user ne koristi
`/usr/bin` - normalni programi distribucije.
`/usr/local/bin` - programi koje si sam downlodao ili buildao. Prednost stavljanja programa ovdje je što ih system update *neće* overwriteati, pa će sve custom promjene ostati. Pri buildanju koristi `prefix=/usr/local`, a u `$PATH` dodaj `/usr/local/bin` i `/usr/local/sbin`
`/opt` - third party monolitski paketi (tipa Oracle)

## echo vs printf

Nemoj koristiti `echo` za ispisivanje nepoznatih varijabli (nekonzistentno ponašanje za `\n` i `-n`).
Umjesto njega koristi `printf`.

## Skraćeno kopiranje

`cp really_long_filename{,.orig}`

## hacky hacky

`strings <files>` ispisuje printabilne stringove u binarnom fileu.
`-n 10` definira minimalnu duljinu stringa (defaultno `4`).

`cat file.exe | hexdump -C` ispisuje file u hex obliku i sidebarom s formatiranim tekstom.

`strace -o <file> <cmd>` ispisuje sve systemske pozive koje izvršena naredba radi.

## The Haiku Vector Icon Format

http://blog.leahhanson.us/post/recursecenter2016/haiku_icons.html

Haiku OS ima poseban format za vektorske ikone - HVIF - koji ima jako male veličine (500 byteove) pa smanjuje broj potrebnih čitanja s diska da se file prikaže u GUIju.
