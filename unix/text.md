# Text Processing

## Output
`cat <files>` ispisuje sadržaj fileova.

`tee <file>` šalje input na *stdout* i u `<file>`. Korisno za zapisati rezultat u file i dalje ga proslijediti u pipe.

`head <file>` i `tail <file>` ispisuju 10 prvih/zadnjih linija.
  * `-n` broj linija.
  * `-F` ispisuje linije kako se dodaju.

`less <file>` omogućuje paginaciju kroz file.
  * `+F` ispisuje linije kako se dodaju.

`split <file> <prefix>` dijeli file u manje fileove, nazvane `<prefix>a`, `<prefix>b`, itd.
  * `-n 100` dijeli na fileove po 100 linija.
  * `-b 50m` dijeli na fileove po 50 Mb.
  * fileove možeš spojiti nazad pomoću `cat filea fileb ... > file`.

`diff <files>` ispisuje razliku između fileova.
  * `-w` ignorira razlike u whitespaceu. `-i` ignorira case.
  * `-q` ispisuje samo linije koje su različite, bez konteksta.

`wc <file>` ispisuje broj linija (`-l`), riječi (`-w`) ili znakova (`-m`) u fileu.


## Combining
`cut <file>` izdvaja dio svake linije.
  * `-c 4-10` izdvaja tekst od 4. do 10. charactera. `-c 4-` izdvaja od 4. do kraja.
  * `-f 3` izdvaja treći stupac. `-d` definira delimiter (defaultno `\t`). `-s` preskače linije u kojima nema delimitera.

`paste <files>` spaja linije više fileova u jednu liniju (obrnuto od `cut`)
  * `-d` definira delimiter kojim će se spajati.

`join <files>` spaja linije više fileova po zajedničkom ključu (slično kao sql join). `1 a` i `1 b` bit ce spojeni u `1 a b`.
  * `-1 3` ključ je u 3. fieldu prvoj filea. `-2` za drugi file.
  * `-t` definira delimiter inputa i outputa (defaultno je whitespace).


## Filteri
`sort <file>` sortira linije abecedno.
  * `-n` sortira po numeričkoj vrijednosti (ako je prva riječ u liniji broj).
  * `-k 2` za sortiranje po 2. stupcu. `-t` definira delimiter (defaultno whitespace).
  * `-r` za reverse.

`uniq <file>` eliminira identične linije koje idu jedna za drugom. Koristi se nakon `sort`.
  * `-u` ispisuje samo linije koje se nisu ponavljale.
  * `-d` ispisuje samo duplikate.
  * `-c` ispisuje broj ponavljanja svake linije.

`grep <pattern> <file>` ispisuje linije koje matchaju dani pattern.
  * `-i` radi case insensitive match.
  * `-e <pattern> -e <pattern>` ili `-E '<pattern>|<pattern>'` za traženje više patterna.
  * `-v` inverse, ispisuje linije koje ne matchaju pattern.
  * `-o` ispisuje samo matching dio linije. Korisni kad imaš cijeli file u jednoj liniji.
  * `egrep` (`grep -E`) za extended regex.
  * `fgrep` (`grep -F`) za fixed stringove, bez regex matchanja.
  * `-C 2` ispisuje 2 linije konteksta prije i poslije. `-A` za after, `-B` za before.


## Stream Editing
`sed` čita liniju po liniju i izvršava operacije nad njima. To znači da ne matcha tekst koji se nalazi u više linija.
  * `-i` radi in-place promjene na samom fileu.
  * `sed 's/<regex>/<text>/<options>'` radi substitution, npr. `s/do+g/cat/g`. Opcije `/g`: global substitute, `/I`: ignore case.
  * `sed 's/dog/cat/g; s/cat/elephant/g' file` za više izmjena.
  * `sed '/<regex>/d'` briše sve linije koje matchaju regex. `sed 1,5d` briše sve od 1. do 5. linije.

`awk` procesira input u recordima (linijama) podijeljenim u fieldove (riječi).
  * Struktura programa je `BEGIN {...}; {...}; END {...};`. `BEGIN` se izvrši jednom prije, a `END` poslije svega. Srednji blok se izvršava za svaku liniju posebno.
  * `/pattern/ {...}` će se izvršavati ako linija matcha pattern.
  * `$0` je cijela linija. `awk '{ print $0 }'` proslijeđuje input koji dobije.
  * `$1`, `$2`... su fieldovi. `awk '{ sum+= $1 }; END { print sum }'` zbraja sve prve vrijednosti.
  * `FS` je field separator (default whitespace). `RS` je record separator (default newline).
  * `OFS` je output field separator (default space). `ORS` je output record separator (default newline).
  * `NF` je broj fieldova u liniji. `$NF` je zadnji field, `$(NF-1)` je predzadnji, itd.
  * `NR` je redni broj linije koja se trenutno obrađuje.


## xargs
`xargs` proslijeđuje rezultat prethodne naredbe kao listu argumenata idućoj, dijeliviši ih u podliste da ne pređe argument size limit.
  * `find . -type f -print | xargs rm` radi isto što i `find . -type f -exec rm {} +`.
  * `find . -type f -print | xargs -I{} grep foo {}` radi isto što i `find . -type f -exec rm {} \;` (pojedinačno izvođenje)
  * `-n 2` definira koliko argumenata da šalje odjednom. `-L 1` šalje liniju po liniju.
  * `-P 24` pokreće do 24 procesa **paralelno**. Pripazi da output ne stiže nužno istim redom kao input.

`xargs` je malo zajeban jer očekuje input u nekom čudnom formatu, pa uvijek preferiraj `find -exec`. Ako ga baš moraš koristiti,
koristi `find -print0` i `xargs -0`.


# Examples

## Obrada šahovskih partija
http://aadrake.com/command-line-tools-can-be-235x-faster-than-your-hadoop-cluster.html
U usporedbi s Hadoop clusterom 200x je brže i ne koristi skoro nimalo memorije. To se može zahvaliti
**paralelizmu** koji dolazi iz pipelinova.

Format rezultat u fileu koje želimo agregirati: `[Result "1-0"]` (može biti `1-0`, `0-1` ili `1/2-1/2`)

`cat *.pgn | grep 'Result' | sort | uniq -c` - broji ukupne različite rezultate.
Vrijeme obrade: `70` sekundi.

`sort | uniq -c` zamjeni s `awk`:
```
awk '{
  split($0, a, "-");
  res = substr(a[1], length(a[1]), 1);
  if (res == 1) white++;
  if (res == 0) black++;
  if (res == 2) draw++;
}
END { print white+black+draw, white, black, draw }'
```
Vrijeme obrade: `65` sekundi.

`htop` ukazuje da `grep` bottleneck sa 100% korištenjem samo jednog CPU corea. To možemo paralelizirati s `xargs`:
`find . -type f -name "*.pgn" -print0 | xargs -0 -P4 -n1 grep "Result"`
Vrijeme obrade: `38` sekundi.

Možemo izbaciti `grep` ako `awk` jednostavno filtrira podatke koje će obrađivati.
`xargs -0 -P4 -n1 awk '/Result/ {...}'` koji vraća rezultate za svaki file individualno. Spojimo to s drugim `awk`:
```
 xargs -0 -P4 -n4 awk '/Result/ {...}
 END { print white+black+draw, white, black, draw }' |
 awk '{ games += $1; white += $2; black += $3; draw += $4; }
 END { print games, white, black, draw }'
```
Vrijeme obrade: `18` sekundi.

Konačno, `awk` možemo zamijeniti s `mawk` koji nekad daje bolji performance.
Vrijeme obrade: `12` sekundi.
