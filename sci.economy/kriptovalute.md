# Kriptovalute

## Blockchain

Blockchain je distribuirana baza koja se sastoji od blokova. Svaki blok je potpisan hashem i sadrži podatke o eventu (npr. novčanoj transakciji), timestamp, i hash prethodnog bloka.

Glavni feature blockchaina je postojanje permanentne i javne liste svih transakcija (*ledger*) koju niti jedan autoritet ne može promijeniti.

Ukoliko se ijedan blok promijeni, morat će se promijeniti i njegov hash, a samim time i hashevi svih blokova koji slijede nakon njega. Block chainovi su distribuirani (nalaze se kod više klijenata), pa je dovoljno usporediti posljednje blokove da se provjeri je li neki od blokova tampered. Ispravan block chain odabire se decentraliziranim koncenzusom.

## Proof of work

Bitcoin i Ethereum za utvrđivanje vrijednosti koriste **proof-of-work** protokol. Sistem koji želi izvršiti neku radnju mora dokazati da je uložio određenu količinu rada u računanje (*computational effort*). Protokol je razvijen `1993` kao način da se obeshrabre spameri i botovi - rad koji bi bio neprimjetan za ljudskog korisnika bio bi preskup za one koji žele masovno zloupotrebljavati mrežu.

`2009` proof-of-work je u kombinaciji s blockchainom upotrijebljen za posve drugu svrhu: implementaciju Bitcoina. Bitcoin *mineri* se natječu u rješavanju proof-of-work zadataka koji potvrđuju transakciju. Miner koji prvi riješi zadatak za nagradu dobija coin koji se za tu priliku stvara.

Ovaj način poticanja minera stvara inflaciju. Svakom transakcijom vrijednost coina pada, pa svi vlasnici novca na taj način "financiraju" transakcije. Dok god vrijednost novca raste brže od ovako izazvane inflacije, većina korisnika to neće ni primjetiti.

S druge strane, mineri ulažu energiju u rješavanje zadataka. Isplati im se rješavati samo dok je vrijednost osvojenog coina veća od cijene energije koju moraju potrošiti.

(TODO: saznaj više o ovom ispod)

Oni koji posjeduju bitcoin se klade da će do sutra porasti u vrijednosti više nego što mogu zaraditi rudareći ga.

Za razliku od stvarnog svijeta gdje stvari imaju vrijednost zbog svoje rijetkosti (*scarcity*), digitalni svijet rijetkost mora simulirati. U slučaju proof-of-worka, rješavanje zadataka s vremenom postaje lakše jer računala postaju brža. Da bi se održala rijetkost, zadatci se moraju neprestano otežavati. Riješavači pritom moraju trošiti sve više energije, pa vrijednost bitcoina mora neprestano rasti.

When a currency grows, there is a lot of demand, leading to a sudden increase in transactions. Physical energy must be used to complete every transaction. The more popular a currency is (increased demand), the more energy is used in processing transactions.

## NFT

*NFTs are basically like buying a special sword in World of Warcraft, except without the game and also without the sword.*

Ideja iza NFT-a je stvoriti tehnologiju koja će umjetnicima dati kontrolu nad vlastitim radovima, olakšati prodaju, te spriječiti korištenje bez dozvola.

Stavljanje umjetnosti na blockchain samo stvara zapis o radovima u opticaju, poput aukcijskog kataloga. Rad pritom nije zaštićen, štoviše, javno je dostupan svima. Blockchain ne podržava spremanje samog rada, već samo sadrži link na stranicu na kojoj je hostan rad. To znači da verifikacija nečijeg vlasništva i dalje zahtjeva postojanje centralnog servera, koji jednom može nestati - sve što nude i postojeće tehnologije, i bez blockchaina.

Zašto je NFT popularan? Prvo, koriste ga tajkuni kao još jedno mjesto u koje mogu pohraniti višak novaca. Vile i jahte imaju troškove za hladni pogon, pa su u zadnje vrijeme kriptovalute mnogo popularnije - pogotovo jer im vrijednost raste dok god ih ljudi kupuju. Problem s kriptovalutama je što njima ne možeš kupiti ništa osim drugih valuta; ili kupiti "umjetnost" u obliku NFTa, za što ti ne treba pretjerano znanje niti ukus.

Tržište umjetnosti je oduvijek bilo mjesto za pranje novaca. Digitalizacija tog tržišta, a pogotovo na nereguliranoj blockhain platformi, samo je privukla još više prevaranata i spammera. Još gore, NFT kultura je postala Herbalife za mlade umjetnike, uvjeravajući da se mogu obogatiti samo ako dovedu još par prijatelja na platformu.

## Problemi

Bitcoin je zamišljen kao decentralizirana valuta, zaštićena od manipulacija vlada i moćnika, pa stoga i sigurniji način za čuvanje vrijednosti. Ali ništa od toga nije realizirano.

Umjesto decentralizacije, rudari se u masivnim koncentriranim operacijama kojima nitko više ne može konkurirati. Umjesto da bude zaštićena od manipulacija, vrijednost raste i pada ovisno o tweetovima influencera poput Muska. Takva volatilnost u kombinaciji s nelikvidnošću čini kriptovalute jako nesigurnim ulaganjem.

Uz sve to, čitava Bitcoin mreža tek 5 transakcija u sekundi (u usporedbi, Visa podržava 2000), a pritom potroši energije koliko američko kućanstvo u 4 tjedna. Ovo nisu problemi mlade i neispolirane tehnologije - Bitcoin postoji od `2009`!

Nakon desetljeća blockchain i dalje nema nikakve praktične uporabe za krajnjeg korisnika. Nijedna aplikacija ne koristi ovu tenhologiju, s iznimkom appova za trgovanje u kriptovalutama. To rezultira u hermetički zatvorenoj valuti, čija je jedina upotreba da se njome trguje i špekulira. Jedino područje u kojem je kripto inovativan je ransomware - sada konačno postoji nereguliran način za prebacivanje velikih iznosa na račun ucjenjivača.

Blockchain ne rješava nijedan problem bolje od već postojećih tehnologija, a kamoli uz cijenu potrošnje energije koju trenutno zahtjeva. Većina developera je svjesno toga, ali investitori su jako zagrizli za blockchain i nitko im ne želi proturječiti dok god ulažu goleme količine novca.

Opća ideja iza kriptovaluta je plemenita: decentralizirani internet kojim korporacije i vlade ne mogu manipulirati, neotuđiva vlasnička prava, poštenije tržište u kojem svatko može sudjelovati. Vizija koju kripto-evangelisti predstavljaju proizlazi iz razočarenja trenutnim sustavom.

Za njih je trenutna ulagačka manija samo prilika da se financira nešto veliko i trajno, makar moralo prolaziti kroz mnoge revizije i tehničke probleme dok se ne stigne do obećane zemlje. Za sve prevare, špekulacije i općeniti apsurd na kriptotržištu, reći će da se isto može naći i u visokim financijskim krugovima.

Iskreni zagovornici kriptovaluta imaju dobre namjere, ali tehnologija u koju investiraju svoje vrijeme i energiju je glupava, a tržište je velika piramidna prevara koje će naštetiti gomili običnih ljudi jednom kad balon pukne.

## Digitalna umjetnost

Digitalni fileovi pohranjuju nebrojeno manje informacija od komada papira. Papir sadrži naslikano djelo, ali i upisanu povijest ruke, olovke, tinte, drveta i šume u svom materijalu. Digitalni djelo nema ništa od toga, a usto zahtjeva napajanje i software koji desetljeće kasnije više neće biti dostupan.

Ono što digitalna umjetnost ima je razmnažanje. Svaka kopija djela je potpuno identična originalu. Svatko s kopijom ima jednako iskustvo kao i s originalom (a ne s, npr. fotografijom skulpture u knjizi). Digitalna umjetnost je medij koja se može širiti mrežom i biti u vlasništvu više ljudi, bez da to umanji njenu vrijednost ili iskustvo iz prve ruke.

# Literatura

* [https://www.theatlantic.com/ideas/archive/2021/04/nfts-werent-supposed-end-like/618488/]

