# Mikrofoni

## Tehnologija

**Dinamički mikrofoni** rade na principu vodiču u magnetskom polju. Za membranu je vezana zavojnica oko koje su magneti. Kada zvuk udari membranu, zavojnica se pomiče i stvara se mali napon. Dinamički mikrofoni se preferiraju u nastupima jer su izdržljivi (nauštrb malo lošije kvalitete).

**Ribbon mikrofoni** rade na sličan princip, samo umjesto zavojnice koriste tanku traku aluminija. Imaju dosta malu osjetljivost, pa im treba dobro low-noise pojačalo s puno gaina. Dobro hvataju zvuk ispred i iza mikrofona, a ne love bočno (pattern osmice). Dosta su nježni i treba pažljivo s njima.

**Kondenzatorski mikrofoni** rade na principu kondenzatora. Tanka membrana (najčešće pozlaćena folija) stoji uz metalnu ploču. Kada zvuk udari membranu, ona se približava ploči i mijenja kapacitet kondenzatora. Membrana ima puno manju masu od zavojnice kod dinamičkih mikrofona, pa može preciznije bilježiti zvučne valove. Zato kondenzatorski mikrofoni imaju najširi frekvencijski raspon i najbolje bilježe nagle promjene (*transient response*).

Signal koji daju kondenzatorski mikrofoni je dosta slab, pa je potrebno vanjsko napajanje da se pojača. Prvi su mikrofoni za sklopove koristili vakuumske cijevi koje su su zahtijevale puno struje, pa je svaki mikrofon imao posebnu kutiju s napajanjem. Danas je standardno **P48** fantomsko napajanje, koje jednostavno ide preko uobičejenog 3-pin mikrofonskog kabela. Druga varijanta je **elektret** sloj kojim se trajno naelektrizira kondenzator, pa nema potrebe za vanjskim napajanjem. Njega uglavnom koriste mobiteli, laptopi i drugi uređaji koji moraju biti štedljivi s baterijom. Elektreti su davno bili šugavi, pa se ovi s napajanjem još zovu i "pravi kondenzatorski" mikrofoni, iako su danas po kvaliteti jednaki.

Kondenzatorski mikrofoni dolaze s različitim **veličinama dijafragme** (membrane). Mikrofoni s malom membranom (13mm) daju neutralan, precizan zvuk. Mikrofoni s velikom membranom (25mm) bojaju zvuk da zvuči veće, ljepše, "sounds like a record" osjećaj.

## Usmjerenje

**Omnidirectional** mikrofoni su jednako osjetljivi na zvuk iz svih smjerova. Koriste za snimke orkestra, ili kućne snimke kad želiš uloviti sve instrumente i ambijent sobe.

**Cardioid (unidirectional)** mikrofoni su osjetljivi na zvuk koji dolazi sprijeda. Zvuk sa strane se bilježi samo djelomično, a zvuk odostraga se ignorira. Koriste se za studio snimke vokala, jednog instrumenta, kada želimo da nešto zvuči "suho" ili blizu.

**Figure 8 (bidirectional)** mikrofoni su osjetljivi na zvuk od sprijeda i odostraga, a ignoriraju zvukove sa strane. Koriste ze napredne stereo tehnike.

**Wide cardioid** je kombinacija omni i kardioida, tj. manje usmjereni oblik kardioidnog. Dobar je za snimanje akustične gitare i manjih vokalnih grupa.

**Supercardioid** i **hypercardioid** su kombinacija figure 8 i kardioida, tj. još su usmjereniji na zvuk sprijeda, a blokiraju zvuk iz `150-160°` i `200-210°` pozicija. Koriste se u situacijama kada želiš dobro izolirati mikrofon od drugih izvora, npr. live ili snimanje određenog bubnja.

Usmjereni mikrofoni poput kardioida stvaraju **proximity effect**. Što je izvor zvuka bliži mikrofonu, frekvencije ispod 200Hz će postati glasnije. Zato radio spikeri zvuče kao Barry White. To može biti korisno ako želiš dodati basa slabijem vokalu, ali može zvučati loše kod npr. akustičnih gitara.

## Dodatna oprema

Studijski mikrofoni su osjetljivi na "pops", glasne zvukove koji nastanu pri izgovaranju "B" i "P" (*plosives*). Koristi se **pop screen**, prsten s dva sloja tkanine, kako bi spriječili te zračne valove da udare direktno u mikrofon. Najbolje ih je postaviti `10-ak cm` od mikrofona i lagano nakositi (tako da se zvuk ne reflektira između screena i mikrofona).

**Windscreen**, spužvasta frizura koja se stavi na mikrofon, se koristi za zaštitu od puhanja, ali donekle može pomoći i s plozivima.

## Povezivanje

Mikrofoni imaju **XLR muški izlaz** (s tri igle), koji prenosi analogni signal u *mic levelu* (milivoltima). Da bi ga spojio na laptop, treba ti **audio interface** koji sadrži pretpojačalo koji će signal pojačati na *line level* (volte) i pretvoriti ga u digitalni. Korisno je da ima i sljedeće feature:
* phantom power (P48) za kondenzatorski mikrofon
* line inpute za druge instrumente (npr. synth, ritam mašinu)
* output za slušalice (trebaju ti ako snimaš overdub)
* USB izlaz za spajanje na laptop

Neki manijaci koriste eksterni **preamp** umjesto integriranog u audio interface. Navodno daju bolju kvalitetu zvuka, bolji gain, ili poseban karakter zvuka. Razlika se jedino primjeti ako koristiš dinamički mikrofon s niskim inputom.

Većina mikrofona imaju output **impedanciju** od `200 ohma`, što omogućava korištenje dugačkih kablova bez gubitka kvalitete (za razliku od npr. električnih gitara). Kad ga spajaš na pretpojačalo, pripazi samo da je input impedancija tog uređaja barem 5 puta veća. Ako nije, neće se ništa oštetiti, ali će performanse biti lošije.

## Šum

Šum dolazi od svuda. Čak i u naizgled nijemim predmetima molekule letaju uokolo i proizvode **termalni šum**.

Šum koji mikrofon stvara bez ikakvog izvora zvuka naziva se **self-noise** ili **equivalent noise level**. Mjeri se u `dB-A`, decibelima prilagođenim ljudskoj percepciji (više nam smeta šum u srednjem rasponu nego u nižim frekvencijama). Sve ispod `20 dB-A` je dovoljno dobro, ispod `10 dB-A` su vrhunski low-noise mikrofoni.

Za dinamičke mikrofone se rijetko mjeri self-noise, jer primarno ovisi o pretpojačalu. Računaj da dinamički mikrofon s ultra low-noise pojačalom ima self-noise od oko `18 dB-A`.

## Snimanje

Snimaj s manje gaina (opći savjet je početi s `-10 dBFS`) - signal se uvijek može pojačati, ali clipping se ne može popraviti. Ako želiš da snimka zvuči više "crunchy", možeš povećati gain u overdrive, a smanjiti level da ne dolazi do clippinga.

**Phase reverse** invertira polaritet signala. Za jedan mikrofon je svejedno, ali primjetno je kada koristiš više mikrofona. Npr. ako snimaš snare odozgo i odozdo, mikrofoni gledaju u suprotnim smjerovima, pa su u različtim fazama, te se poništavaju. U tom slučaju treba invertirati jedan od mikrofona, i zvuk će biti puno bogatiji.

# Literatura

* https://www.neumann.com/homestudio/en/filter/Academy
