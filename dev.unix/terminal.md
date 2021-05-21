# Terminal

## Keyboard controls

`tab` autocomplete.
`CTRL + R` započinje search history. Utipkaj što tražiš, `CTRL + R` cycla kroz rezultate, `enter` izvršava naredbu, a desna strelica je stavlja u command line za editiranje.
`CTRL + U` delete do početka linije, `CTRL + K` delete do kraja linije. `CTRL + W` deleta prethodnu riječ.
`CTRL + L` clear screen.

## Keyboard input

Kada na tipkovnici stisneš tipku `A`, terminalu se šalje **scan code** tipke (`30: down`). Terminal pretvara kod u znak `a` (byte `61`) koji šalje trenutno otvorenoj aplikaciji, npr. shellu. Aplikacija prikazuje znak na trenutnoj poziciji kursora.

**Kontrolni znakovi** nisu printabilni, nego imaju posebne funkcije. Tipka `RETURN` se pretvara u znak `Carriage Return` (byte `0D`) koji prebacuje kursor u novi red. Kombinacija tipki `CTRL+A` prevodi se u kontrolni znak `Start Of Heading` (byte `01`) koji vraća kursor na početak reda.

Kombinacije znakova koje nemaju svoj kontrolni znak šalju se kao **ANSI escape sequence**. On počinje znakovima `ESC [` (bytes `1B5B`), nekad prikazivan kao `^[[` ili `\e[`. Tipka `UP` se prevodi u escape sequence `ESC [ A` koji u shellu prikazuje prethodnu utipkanu naredbu.

Skripte koje ne handlaju escape sequencu, prikazat će je vizualno, npr. `^[[A`. To možeš dobiti i u shellu ako stisneš `CTRL+V` prije kombinacije tipki - sekvenca će se prikazati umjesto da se interpretira.

U programskim jezicima najčešće možeš korisiti `print "\x1b["` ili `print "\e["`.

## Escape sequences

Izgled teksta se definira s `\e[1m`, gdje se koriste sljedeći brojevi: `1` za bold, `4` za underscore, `5` za blink, `7` za inverse, i `8` za nevidljivi tekst; `30-37` za boju teksta; `40-47` za boju pozadine. Neki terminali podržavaju i modifikatore na boje, npr. `30;1` za svijetliju nijansu. `\e[m` resetira na defaultan izgled.

Kursor se apsolutno pozicionira s `\e[2;5H` (2. linija, 5. stupac). `\e[H` postavlja, na "home", gornje lijevo polje. Relativno se pozicionira s `\e[2A` za dva polja gore (`B`, `C`, `D` za dolje, desno, lijevo).

`\e[s` sprema poziciju kursora, `\e[u` je restora.

`\e[2J` briše cijeli ekran, `\e[K` briše cijelu liniju.

`\e[?1049h` sprema trenutni screen i otvara novi; `\e[?1049l` zatvara trenutni i vraća spremljeni.

