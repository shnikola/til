# Graphics

## Frame rate

Naše oko brzinu do `12fps` doživljava kao niz slika, a iznad nje kao pokret. Što je fps viši, pokret se čini fluidniji. Nakon ere nijemog filma filmska industrija je odabrala `24fps` kao standard, većinom iz ekonomskih razloga.

`24fps` je relativno mala brzina u odnosu na stvarni svijet, što dovodi do artefakata kao što je **motion blur**. Ti nedostaci su postali dio i"filmskog izgleda". Zato je Hobit na `48fps` izgledao čudno, iako je vjernije prenosio ono što su kamere snimale.

Za razliku od filma, video igre se prikazuju na barem `30fps`. Igre su interaktivni medij, pa smo smo mnogo osjetljiviji na frame rate. Prenizak fps stvara lošiji osjećaj kontrole. Unatoč visokom fpsu, mnoge igre oponašaju filmske artefakte poput motion blura.

Monitori i televizori su standardizirani da se refreshaju `30`, `60`, `90` ili `120` puta u sekundi. Čak i ako grafička kartica renderira `40fps`, slika na monitoru će se iscrtati samo `30` puta u sekundi. I dalje ima smisla da igra radi na `45fps`: to daje buffera u situacijama kad je grafička opterećena, pa se renderiranje može usporiti bez da se primjeti pad ispod `30`.

**Vsync** se brine da grafička i monitor ostanu u syncu. Ako ga isključiš, može doći do grešaka kao što je screen tearing.

Neke igre lockaju loop na fiskni fps jer su im izračuni za mehaniku igre strogo vezani za fps. To najčešće nije najbolja ideja.
