# Korporacije

## Conway's Law

Arhitektura svakog softvera odražava komunikacijske strukture organizacije u kojoj je nastao.

Razlog je sljedeći: developeri koji rade na istom kodu moraju moći međusobno komunicirati. Tamo gdje je komunikacija dobra, developeri će moći paralelno programirati unutar istog modula. Tamo gdje je komunikacija otežana, definirat će se API kao tehnička zamjena za društvenu komunikaciju.

## Zašto X zapošljava toliko developera?

Isprva je teško shvatljivo da tvrtka poput Ubera zapošljava 8000 inženjera. Neki od razloga su tehničke naravi: globalno popularne stranice moraju mnogo više uložiti u performanse; isplati se razvijati nove feature (većinu kojih nećeš ni primjetiti) dok god donose novce; što je firma većeg profila, više mora uložiti u security.

Ali osnovni razlog je organizacijski. U velikoj korporaciji produktivna jedinica nije zaposlenik nego tim od ~8 članova. I za svaku stvar koju korporacija radi, potreban je barem jedan tim.

Cijena jednog takvog tima je red veličine `$1M` godišnje. Imaj to na umu ako prodaješ uslugu koja korporaciji može uštedjeti jedan tim.

## Skaliranje organizacije

Najveći problem pri rastu korporacija je kognitivno opterećenje zaposlenika (*cognitive load*). S rastom sustava, svaki developer će morati držati sve više informacija u glavi, i time će razvoj sustava biti sve sporiji. Arhitektura sustava (npr. monolit vs mikroservisi) prvenstveno se treba oblikovati da svaki tim može samostalno dostaviti proizvod za koji je zadužen (da ne ovise o poznavanju komplicirane konfiguracije ili osobi u drugom timu zaduženom za deployment).

Kognitivno opterećenje dolazi iz tehničkih zahtjeva, ali i iz društvenih: kolega s kojim je teško komunicirati, nepouzdan menadžment, nedefiniran smjer firme i sl. Vodstvu je zadatak da ukloni ove smetnje kako bi zaposlenici mogli raditi bez dodatnih opterećenja.

Sustavi prirodno postaju kompleksniji ako ne postoji mehanizam koji će ih u tome spriječiti. Nijedan zaposlenik ni menadžer neće reći: projekt na kojem radim je prejednostavan, nije potrebno toliko ljudi u mom timu. Zadatak dobrog menadžmenta je da se bori protiv te prirodne kompleksnosti.

## Radoholizam u startupima

Startup kultura mitologizira duge radne dane i burnout kao nešto što se očekuje od svakog zaposlenika. Ovaj problem dolazi od ulagača koji za svoje milijune očekuju epske napore, pri čemu se sva obećanja koje vlasnik tvrtke daje prelijevaju na grbaču niže pozicioniranih radnika. Otud motivacija da se zaposlenike zadrži što duže u uredima nudeći im razne povlastice, otud i priča o "misiji da se svijet učini boljim mjestom".

## The Peter Principle

Osoba u organizaciji napreduje tako da je se postavi na novu poziciju za koju je nekompetentna. Jednom kada postane kompetentna, promoviraju je opet na novu poziciju, i tako sve dok ne dođe do razine koju više ne može sustići. Nakon toga neće dobiti otkaz na račun prethodnih uspjeha, ali zbog nekompetencije neće biti ni promaknuta.

Iz toga slijedi da će s vremenom na svakoj poziciji u organizaciji biti zaposlenik koji je nesposoban da radi taj posao.

# Literatura

https://m.signalvnoise.com/trickle-down-workaholism-in-startups-a90ceac76426
