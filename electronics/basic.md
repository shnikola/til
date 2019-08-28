# Elektronika

## Struja

Struja je tok električnog naboja. Glavni nositelji naboja su elektroni, i element je bolji vodič što mu je lakše otpustiti i primiti elektron.

Zatvoreni krug provodljivog materijala stvara put kojim elektroni mogu kontinuirano teći.

Statički elektricitet se stvara kada se skupljaju suprotni naboji odvojeni izolatorom. Kada privlačnost postane toliko jaka da elektroni pronađu put kroz izolator, događa se statičko pražnjenje. Ako putuje kroz zrak, elektroni se sudaraju s elektronima zraka otpuštajući energiju u obliku iskre, ili munje.

## Otpornici

Otpornici ograničavaju protok elektrona u strujnom krugu. Oni su pasivna komponenta, što znači da samo troše snagu (ne generiraju je). Najčešće se dodaju kao komplementi aktivnim komponentama, npr. pojačalima, mikrokontrolerima i drugim IC-ima.

2 osnovna tipa su:
- PTH (plated through hole), imaju duge nožice koje se stavljaju u rupu na breadboardu. Koriste se za prototipiranje.
- SMD (surface-mount device) su jako mali četverokuti (0.8mm) koji mora robot zalemiti. Koriste za masovnu proizvodnju.

Vrijednost otpora nikad nije savršeno precizna. **Tolerancija** otpornika (4. prsten) označava koliko stvarni otpor može varirati. Npr. tolerancija od 5% za 1k otpornik znači da može imati otpor od 0.95kΩ do 1.05kΩ.

**Disipirana snaga** na otporniku računa se kao `I^2*R` ili `V^2/R`. Klasa snage (power rating) određuje koliku maksimalnu snagu mogu izdržati. Uobičajeni otpornici imaju klasu `0.25W` ili `0.5W`.

Otpornici se koriste da **smanje jačinu struje** kako se druga komponenta u seriji, npr. LED dioda, ne bi spržila. Ako je `Vf` napon potreban da dioda zasvjetli (*typical forward voltage*, uglavnom 1.7V and 3.4V), a `If` maksimalna struja koju dioda može izdržati (*maximum forward current*, oko 20mA), onda je potreban otpor `(Vs - Vf)/If` da bi se struja smanjila.

### Voltage divider

Otpornici se koriste i za **dijeljenje napona**, odnosno pretvaranje napona u manji. Dva otpornika R1 i R2 se spoje u seriju s izvorom napona `Vin`. Napon od točke `Vout` između njih će onda biti jednaka `Vin * R1 / (R1 + R2)`.

**Potenciometri** rade na istom principu, gdje je srednja nogica točka između dva otpornika.

Ako je R2 **otpornički senzor** (npr. fotoćelije), promjena u otporu se može na ovaj način očitati kroz promjenu napona.

Nemoj koristiti dijeljenje napona da smanjiš napon izvora, jer su snaga i voltaža previsoke, pa će vjerojatno otopiti otpornik.

### Pull-up resistor

**Pull-up otpornici** se koriste za postavljanje ulaza mikrokontrolera. Ako ulazni pin nije spojen ni na što (*floating*), ne može se garantirati je li na visokom ili niskom naponu. Zato se ulaz spaja na visoki napon od 5V preko otpornika, a preko sklopke na GND. Kada je sklopka otvorena, ulaz je na visokom naponu, a kada je zatvorena ulaz je na niskom naponu.

Otpornik je tu potreban kada je sklopka zatvorena, jer bi inače visoki napon i uzemljenje bili spojeni, pa bi došlo do kratkog spoja. Otpor treba biti dovoljno velik da se generira velika snaga kad je sklopka zatvorena, ali za red veličine manji od otpora inputa, kako ne bi oduzeo previše napona kada je sklopka otvorena. Najčešće je to vrijednost oko `10kΩ`.

## Kondenzatori

Kondenzatori su spremnici električne energije. Kapacitet energije koju mogu spremiti mjeri se u mikrofaradima.

Kondenzator se sastoji od dvije ploče odvojene dijalektrikom (papir, plastika). Kada struja prolazi kroz kondenzator, elektroni zapnu na jednoj ploči jer ne mogu proći kroz dijalektrik, i odbijaju elektrone s druge strane, stvarajući razliku potencijala. Pozitivna i negativna ploča se privlače, ali ne mogu doći u kontakt. Na taj način se kondenzator puni, dok ne dođe do punog kapaciteta kada ne može primiti više elektrona.

Struja teče kroz kondenzator samo ako se napon mijenja, po formuli `i = C*dV/dt`. Ako je napon konstantan, struja ne teče. Kondenzator uvijek radi protiv promjene napona - ako napon raste, koristit će tu energiju da se napuni. Ako napon pada, oslobodit će energiju.

Kondenzatori blokiraju niske, a propuštaju visoke frekvencije.

**Probojni napon** (*maximal voltage*) je najveći napon kojeg kondenzator može izdržati. Nakon toga nastaje proboj dijalektrika i kondenzator se uništava.

**Struja gubitaka** (*leakage current*) je slaba struja koja prolazi kroz dijalektrik (najčešće u nanoamperima) zbog kojeg kondenzator sporo ali sigurno gubi pohranjenu energiju.

Kondenzatori uvijek imaju mali **otpor** (oko `0.01Ω`), koji može biti problematičan kada jaka struja prolazi kroz njega, stavarajući toplinu i gubitak snage.

Vrste kondenzatora su:
* keramički, mali i malog kapaciteta, s manjim gubitcima.
* elektrolitski, cilindrični, koristi ako trebaš veliki kapacitet. Polazirirani su, što znači da trebaš paziti da anodu (duža nogica) staviš na viši napon od katode (označena s `-`).
* superkondenzatori, napravljeni da spremaju veliku količinu energije.

Iako ne mogu čuvati energije koliko i baterija iste veličine, kondenzatori je mogu otpustiti mnogo brže, i imaju duži vijek trajanja.

Kondenzatori u paraleli zbrajaju svoje kapacitete, a u seriji se zbrajaju inverzno (kao otpornici u paraleli).

Kondenzatori se koriste za **otklanjanje šuma** iz izvora napona (*decoupling / bypass capacitors*). Kratkotrajne promjene u naponu bi mogle oštetiti IC, pa se kondenzator stavi između izvora i uzemljenja, ispred IC-a. Visoko frekvencijski šum će tako proći kroz kondenzator umjesto kroz IC. Također, ako napon na izvoru privremeno padne, kondenzator će osloboditi spremljenu energiju kako bi održao napon na ulazima. Najčešće se dodaje nekoliko kondenzatora, `0.1µF` do `10µF`.

Kondenzatori se koriste za pretvaranje AC (iz zida) u DC (koju sklopovi koriste), zajedno s diodnim ispravljačem.

## Signal

Signal je u elektronici je vremenski promjenjiva vrijednost koja prenosi neku informaciju. Najčešće se radi o naponu koji se mijenja kroz vrijeme (npr. sinusoida).

# Literatura

* https://learn.sparkfun.com/tutorials
