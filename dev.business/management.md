# Management

## Procesi

**Waterfall model** je ekstremni primjer sekvencijalnog modela razvoja, gdje se projekt dijeli u odvojene faze: definicija zahtjeva, kodiranje, testiranje, održavanje. Kad jedna faza završi, iduća započinje. Često u svakoj fazi rade različiti ljudi.

**Agile** je inkrementalni pristup gdje se početni dizajn fokusira na mali podskup ukupnih funkcionalnosti. Taj dio se dizajnira, implementira i testira. Zatim se pronalaze problemi trenutnog dizajna, popravljaju se, i ponovno se dodaje nekoliko featurea. Svaka iteracija evaulira cjelokupni dizajn, i omogućava da se popravi dok je sustav još malen; također se skuplja iskustvo pa je razvijanje budućih featurea lakše.

## Trajanje projekata

Većina projekata propadnu jer traju puno duže od planiranog.

Programeri su optimistični u svojim procjenama i ne predviđaju da će naići na probleme i zastoje. Ali što je projekt veći, veća je šansa da će se naići na neki problem, i da će zastoj jednog zadatka odužiti sve druge koji ovise o njemu.

Menadžeri su optimistični i računaju da, ako projekt ne napreduje dovoljno brzo, uvijek mogu dodati još ljudi na njega. Problem je što dodavanje ljudi zahtjeva vrijeme da se uvedu u projekt, vrijeme da se tim reorganizira i vrijeme za svu dodatnu komunikaciju. **Dodavanje ljudi na projekt koji kasni će samo učiniti da kasni još više.**

Što projekt postaje veći, razvoj se na njemu sve više usporava. Istraživanja su pokazala da je vrijeme razvoja ne raste linearno s brojem linija koda, nego polinomijalno.

## Milestones

Projekti rijetko kasne zbog jednog velikog problema, već zbog niza malih dnevnih problema koji se s vremenom nakupljaju. Da bi izbjegao dnevno kašnjenje, potrebno je imati dobro definiran raspored i milestone.

Dobiti uvid u stvarno stanje projekta je teško - preko pola vremena sustav će biti u fazi **90% dovršenosti**. Zato milestoni moraju biti konkretni, precizno definirani događaji. Ako je milestone nejasan, svatko će ga interpretirati na način prema kojem je sve pod kontrolom. Ako je milestone kristalno jasan, nećeš moći zavarati samog sebe.

## Procjena

Kada daješ rok do kojeg ćeš nešto napraviti, uzmi u obzir da imaš tek dio dana za samo programiranje, a da ćeš ostatak trošiti na sastanke, komunikaciju i ostale popratne poslove.

Posebno je važno alocirati dovoljno vremena za sistemsko testiranje. S obzirom da su to testovi koji se rade pri kraju projekta, nitko neće biti svjestan problema dok ne bude prekasno.

## Prototip

Kada god koristiš novu tehnologiju ili istražuješ novi koncept, prvi prototip koji razviješ će biti jedva uporabljiv - prespor, prevelik, ili nespretan za korištenje. Jedino riješenje je početi iznova, i ispraviti pogreške koristeći stečeno iskustvo.

Stoga u situacijama kada dodaješ novi dio sustava o kojem još ne znaš dovoljno, uvijek planiraj da će prva verzija većim dijelom završiti u smeću. Učini ga što modulurnijim i odvojenijim od ostatka sustava.

## Dokumentacija

Stil dokumentacije mora biti precizan i potpun. Korisnik će često čitati samo jednu sekciju, pa u svakoj sekciji treba iznova ponoviti osnovne postavke. Zbog toga dokumentacija nije najuzbudljivije štivo za čitanje, ali preciznost je važnija od zanimljivosti.

Kad god možeš izbjegavaj zamjenice. Bolje je ponoviti imenicu, nego ostati nejasan (npr. umjesto "Izmjenili smo token u APIju jer je prošli imao bug." napiši "Izmjenili smo token u APIju jer je prošli token imao bug.")

## Efikasnost i Otpornost

Efikasnost je obrnuto proporcionalna otpornosti na promjene. Ovo vrijedi za poslovne, ali i za sve ostale društvene i ekonomske procese.

Efikasnost daje brzinu po cijenu manje otpornosti na nepredviđene događaje. Jednom kad se sustav počne optimizirati za efikasnost, postaje podložniji katastrofalnim problemima ako se početni uvjeti promjene.

## Komunikacija i veličina tima

Najbolji timovi su mali, s jednim tehničkim direktorom (bavi se dizajnom sustava) i jednim producentom (osigurava resurse i komunicira s vanjskim svijetom). Nekad to može biti ista osoba, ali uglavnom je dobro da su dvije. Nažalost, mali timovi su prespori za razvoj velikih projekata. Ali veliki tim možeš podijeliti na više samostalnih malih timova.

Ako `n` ljudi radi na projektu, postoje potencijalnih `O(n^2)` komunikacijskih kanala i `O(2^n)` timova koje je potrebno koordinirati. Zadatak organizacije je da smanji potrebnu količnu komunikacije i koordinacije.

Jedan od popularnih oblika komunikacije je **standup**. Cilj standup sastanka je omogućiti da svi započnu radni dan s maksimalno informacija relevantnih za njihove zadatke. Standup nije mjesto za donošenje odluka o arhitekturi i rješavanje sistemskih problema.

Voditelj projekta mora znati prepoznati koje informacije zahtjevaju akciju, a koje ne. Ako može staloženo čitati izvještaj statusa bez panike i ishitrenih reakcija, onda je svima u interesu da budu iskreni kad ih pišu. U suprotnom, svi će maskirati stvarno stanje jer ne žele još jednu dodatnu brigu. Stoga je korisno jasno razlikovati **status-review sastanke** i **problem-action sastanke**.

## Povjerenje

Učinkoviti timovi trebaju povjerenje. Manjak povjerenja nadoknađuje se birokracijom (npr. code reviewima, tjednim izvještajima, detaljnim praćenjem potrošenog vremena). Što više povjerenja i međusobne odgovornosti tim posjeduje, to će biti manje birokracije i tim će biti efikasniji.

Povjerenje se gradi vremenom i kontaktom. Suradnja s remote timovima ili pojedincima je zato nezgodna, jer zbog manjka stvarnog kontakta tehničke nesuglasice interpretiramo kao neznanje, lijenost ili zlonamjernost. Ulaži što više vremena u aktivnosti u kojima će se članovi povezati: team buildings, 1-na-1 razgovori, kolaborativne rasprave i sl.

Druga stavka povjerenja je redovita komunikacija. U ljudskoj prirodi je pretpostaviti najgore kada se druga strana ne javlja. Zato se potrudi da šalješ redovne reportove o obavljenom poslu, a ne čekaš da ih druga strana zatraži.

## Delegiranje

Kad delegiraš zadatak:
- objasni što želiš postići i zašto (big picture)
- objasni što želiš da ta osoba postigne
- definiraj resurse koji su dostupni, i ograničenja
- odredi precizno određeno vrijeme kad treba biti gotovo
- reci kako želiš da te izvještava o napretku/problemima

Ono što ne želiš definirati je **način na koji će zadatak biti izvršen**.

## Pogreške

Kada napraviš bitnu pogrešku, prvo javi nadređenima (uvijek je bolje da saznaju od tebe, a ne od klijenta ili šefa). Reci im koji je problem i da radiš na rješenju. Postavi se kao vlasnik problema i da timeline u kojem ćeš ga riješiti, ili moći dati više informacija.

Kada trebaš nekome reći da je napravio pogrešku, napravi to u četiri oka. Kada želiš nekoga pohvaliti, napraviti to javno.

Prije svega, pričekaj i razmisli prije ispraviš nečije ponašanje. Ne radi to dok si emocionalan. Ispravljanje mora biti proporcionalno ozbiljnosti pogreške. Ako je pogreška nebitna, tvoje pametovanje će samo demotivirati osobu, pa ga radije zadrži za sebe. Nikad ne ispravljaj nekog samo da dokažeš da si pametniji od njega.

Ako je potrebno, promijeni procedure u firmi da se ta pogreška ne može ponoviti.

Ako je osoba neiskusna, daj joj do znanja da je griješiti normalno, i da se događa svima.

*A young executive had made some bad decisions that cost the company several million dollars. He was summoned to Watson’s office, fully expecting to be dismissed. As he entered the office, the young executive said, “I suppose after that set of mistakes you will want to fire me.” Watson was said to have replied: “Not at all, young man, we have just spent a couple of million dollars educating you.”* (Edgar Schein, Organisational Culture and Leadership)

# Literatura

* [https://komoroske.com/slime-mold]
* [https://academia.stackexchange.com/a/103331]
* [https://github.com/basecamp/handbook]
