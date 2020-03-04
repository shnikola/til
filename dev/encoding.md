# Encoding

Sve što je zapisano u memoriji računala su nule i jedinice. Način na koji se tekst pretvara u bitove naziva se **character encoding**.

**ASCII** je encoding u kojem svaki znak zauzima 1 byte. Vrijednosti od `0-31` su kontrolni znakovi (npr. `7` je bell). Vrijednosti od `32-127` su slova, brojevi i interpunkcija. ASCII je koristio samo 7 od 8 bitova, pa su vrijednosti od `128-255` ostale neiskorištene. Svi su imali različite ideje kako iskoristiti te bitove, dok nije donesen ANSI standard.

**ANSI** je ekstenzija ASCII-a koji koristi svih 8 bitova. Vrijednosti `128-255` se mapiraju prema različitim **code pagevima**, pa se `134` može prikazati kao `å` (ako koristiš CP437), hebrejsko `ז` (CP862), ili ćirilično `Ж` (CP1125). Nedostatak je što se računalo mora odlučiti samo za jedan code page, pa se znakovi iz različitih stranica ne mogu istovremeno prikazivati. Također, veće abecede (npr. kineska) i dalje ne stanu u 8 bitova.

**Unicode** je sistem koji pokriva sve postojeće (i izmišljene) abecede, tako što se svakom znaku dodjeljuje broj nazvan **code point**. Npr. slovo `A` ima codepoint `U+0041`. Unicode nije encoding nego univerzalni popis znakova i njihovih codepointa.

Kako se codepointi zapisuju u memoriju, ovisi od encodinga. Unicode se može kodirati bilo kojim encodingom (npr. s CP437), ali zvog ograničenja veličine, većina znakova se neće moći zapisati (pa će se prikazivati kao `?`). Zato je najbolje koristiti neki od **UTF** encodinga.

**UTF-16** koristi 2 bytea za svaki codepoint. Za codepointe veće od 2^16, koristit će 4 bytea. Na početku filea može još biti i **BOM** (byte order mark) - oznaka koristi li se big-endian (`FE FF`) ili little-endian (`FF FE`). Ovaj encoding je dosta rastrošan, jer većina engleskih znakova stane u 1 byte, a on koristi minimalno 2 bytea po znaku.

**UTF-8** koristi 1 byte za code pointe `1-127`, 2 bytea za `127-2047`, itd. Tako se koristi manje memorije za zapisivanje engleskog teksta. Kao bonus, tekst zapisan u ASCII encodingu će se moći pročitati s UTF-8. Danas je defacto standard interneta, i šire.

## Emoji

Emojiji su definirani kao zasebni znakovi u Unicodeu, ali se mogu kombinirati s drugim znakovima. Modifikator boje kože (**Emoji modifier Fitzpatrick type**) se stavlja kao odvojeni znak nakon emojija, što će neki browseri prikazati kao jedan emoji (npr. `👦` i `🏾` daju `👦🏾`). Zastave se prikazuju kao kombinacija slova (**Regional indicator symbol**), pa se `🇦` i `🇺` zajedno prikazuju kao `🇦🇺`.

# Literatura

https://www.joelonsoftware.com/2003/10/08/the-absolute-minimum-every-software-developer-absolutely-positively-must-know-about-unicode-and-character-sets-no-excuses/
