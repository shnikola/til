# Arhitektura računala

## Logička vrata

Osnova rada svakog električnog računala su logička vrata, komponenta koja propušta struju ako primi kontrolni signal.

Najstarija vrsta logičkih vrata bili su releji, mehaničke sklopke ispod kojih je zavojnica. Kada kroz zavojnicu prođe struja (kontrolni signal), magnetizam povlači sklopku i zatvara strujni krug, propuštajući struju. Problem s relejima je što su mehanički, pa su jako spori (množenje 2 broja trajalo je 6 sekundi) i skloni kvarovima (bube su se znale uvlačiti u njih).

Iduća verzija je vakuumska cijev. Kada se elektroda zagrijava, otpušta elektrone. Ako je kontrolna elektroda pozitivno nabijena, elektroni prelaze na nju i struja teče. Ako je negativno nabijena, struja ne teče. Nema pokretnih dijelova, pa su puno brže i manje se kvare (iako svejedno mogu pregoriti kao žarulje).

Posljednja verzija su tranzistori. Kontrolna žica upravlja hoće li poluvodič provoditi struju ili ne. Za razliku od krkih vakuumskih cijevi, u potpunosti su čvrsti materijal, tako da se gotovo nikad ne kvare, i mogu biti mikroskopski mali.

## Storage

Volatile memory - ako nestane struje, podatci se izgube. Potreban je način da zapišemo podatke zauvijek. To je storage. Prije je volatile memorija (npr. RAM) bila brza, a storage spor, ali danas je storage usporedivo brz.

Punch cards - Kapacitet 960 bitova. Ne treba im struja, papir je jeftin i izdržaiv. Problem je što su spore za čitanje i write-once (ne možeš popuniti probušenu rupu), pa se ne mogu koristiti za privremenu memoriju.

Delay line memory - Cijev napunjena tekućinom (npr. živom) s jedne strane ima zvučnik. Kada pošalje signal, on putuje kao val i dolazi do mikrofona, koji vraća signal nazad na početak. Na taj način možeš slati jedinice (val) i nule (pauza). Problem je što možeš očitati samo bit po bit, i još moraš čekati da dođe na red - sekvencijalna memorija.

Magnetic core memory - mali magneti donuti oko kojih je omotana žica. Slanje struje mijenja polaritet magneta koji se može očitati. Jedan magnet - jedan bit, ali ako ih se postavi u grid možeš pristupati bilo kojem dijelu memorije instantno - random access memory. Jedan grid je obično imao 1024 bitova.

Magnetic tape - vrpca prolazi kroz magnetsku glavu za pisanje, te joj se dijelovima mijenja polaritet. Glava za čitanje detektira polaritet bez da ga promjeni. Podijeljena na paralelne trake bitova, imala je veliku gustoću, pa si na traku moga o spremiti gotovo 2MB. Traka je jeftina, te se i danas koristi za arhiviranje. Nedostatak je što je sekvencijalna.

Magentic drum memory - metalni cilindar premazan magnetskim slojem na kojem su se zapisivali podatci. Cilindar se konstanto vrti, a cijelom dužinom su postavljeni čitači u liniju, pa je delay čitanja mali. Kapacitet 10Kb.

Hard disk - ista tehnologija kao magnetic drum, samo raspoređena na disku. Pošto su diskovi tanki, možeš ih stackati zajedno. Glava za čitaje putuje gore dole i namješta se po radijusu, dok se diskovi vrte. Prvo IBM-ovo računalo sa hard diskom, RAMAC 305 (1956.) imalo je 50 diskova s ukupnim kapacitetom 5 MB. Brzina traženja je bila 0.6 sekunda. To je presporo za memoriju, pa je imao još i drum i magnetic core memoriju.

Floppy drive - isto kao hard disk, samo je disk savitljiv a ne čvrst. Kapacitet 1.44 MB, kasnije su došli gušći mediji (zip drive 250mb)

Optical storage - sličan princip magnetksim diskovima, samo rade na principu svjetlosti - imaju male rupice zbog kojih se svijetlost reflektira drukčije u optički senzor. 30cm Laserdisk (300 MB), Compact Disk (800 MB), Digital Versatile Disc (4.7 GB)

Solid state drive - integrirani sklopovi bez pokretnih dijelova, ali nisu volatile. Jako brzi, ali i dalje sporiji od RAM-a.

## Optimizacija procesora

Bottleneck CPU-a je RAM, jer dohvaćanje podataka i slanje preko busa traje tisuće clock cyclova. Jedno riješenje je staviti cache u procesor, te u njega spremati cijele blokove koje RAM vraća.

Paraleliziranje fetch-decode-execute blokova utrostručuje brzinu izvođenja. Ali pritom procesor mora paziti da se instrukcija koju je unaprijed dohvatio nije promijenila, pa mora pratiti data dependency i u tom slučaju pauzirati pipeline. Napredni procesori čak znaju mijenjati redoslijed instrukcija kako bi minimizirali zaustavljanje pipelinea (*out-of-order execution*).

Codnitional jump instructions također zaustavljaju pipeline jer rezultat koji se provjerava nije poznat do samog izvođenja. Napredni procesi ne zaustavlju pipeline, nego pogađaju rezultat i pune pipeline kao da se dogodio (*speculative execution*). Ako je očekivani rezultat pogrešan, procesor mora napraviti pipeline flush, koji ga dodatno usporava. Kako bi se smanjila posljedica flushinga, koristi se *branch prediction* i današnji procesori pogađaju s 90%-om točnosti.

Čak i u pipelineu, iskoristivost CPU komponenata nije maskimalna. Ako je naredba dohvaćanje iz RAMa, ALU će biti idle u execute fazi. Superskalarni procesori dohvaćaju više naredbi odjedanput, i istovremeno izvršavaju naredbe koje zahtjevaju različite komponente. A popularne komponente se mogu duplicirati (npr. mnogi procesori imaju i po 8 ALU jedinica) kako bi paralelno mogle izvršavati dohvaćene naredbe.

Drugi način za poboljšanje je paralelno izvršavanje različitih programa. Tome služe višejezgreni procesori. Rade kao više odvojenih procesora, ali pošto su integrirani mogu dijeliti resurse poput cachea.

Dynamic Frequency Scaling - prilagođavanje rada clocka kako bi se uštedilo na struji.
