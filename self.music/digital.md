# Digitalizacija

## Sampling rate

Zvuk je oscilacija zraka koja dolazi do uha slušatelja. Da bi digitalno opisali zvuk, mjerimo **amplitudu vala u fiksnim intervalima**.

**Sampling rate** je broj mjerenja koje napravimo u sekundi, npr. `200 Hz` znači da uzimamo uzorak vala 200 puta u sekundi. Ljudsko uho ne može čuti frekvencije više od `20000 Hz`, pa je sample rate odabran za audio CD `22000 Hz`. Moderne zvučne kartice koriste i više rateove, `44100`, `48000` ili čak `96000 Hz`. Lo-fi uređaji poput Arduina i NES-a ne mogu prozvoditi zvuk dovoljnom brzinom, pa koriste niži sampling rate, poput `8000 Hz`.

**Vrijednosti amplitude** se može zapisivati kao float (od -1 do 1), int16 (od -32768 do 32767) ili uint8 (0 do 255).

Zvuk se tako zapisuje u memoriji kao **array brojeva**. Za najjednostavniji slučaj `8000 Hz` s `uint8`, radi se o arrayu bytova.

## Oscilatori

Da bi array bytova proizvodio određeni ton, vrijednosti arraya se moraju ponavljati određenom frekvecijom. Za ton A (`440 Hz`), moraju se dogoditi 440 ponavljanja u sekundi.

Vrijednosti koje se ponavljaju određuju oblik vala i boju zvuka, npr:
* **square** je oblika `[0, 0, 0, ... 255, 255, 255]`, gdje je niz nula dug `8000/(2*440)`
* **sawtooth** je oblika  `[0, 1, 2, ... 254, 255, 0, 1, 2, ...]`, s tim da radimo inkremente od `440*256/8000` (modula 255)
* **sine** je oblika sinusoide, gdje je inkrement `440/(8000*2*PI)`

Druga metoda je generirati sample od`8000/440` nasumičnih vrijednosti koje se ponavljaju.

Za oponašanje tona žice ili kalimbe, u svakoj iteraciji postavi vrijednosti u arrayu na prosjek trenutne i sljedeće vrijednosti. Ton će polagano fadeoutati.

## Play

Unix ima nekoliko alata za sviranje zvuka koji dolazi iz stdina. Za sviranje zvuka sampliranog na :
* `aplay` ili `pacat --rate 8000 --channels 1 --format u8` na Linuxu
* `ffplay -ar 8000 -ac 1 -f u8 -nodisp -` na OSX-u

Za stvaranje bijelog šuma dovoljno je proslijediti niz nasumičnih byteova, npr. `cat /dev/urandom | PLAY`.
