# Engine - Main Loop

Većina igara je napravljena da kontinuirano loopa kroz 4 stanja:

1. Dohvaćanje inputa
2. Prevođenje inputa u akcije
3. Izračunavanje novog stanja kroz akcije
4. Prikaz novog stanja.

Loop se može pokretati na userov input (turn based igre) ili za svaki frame (real time igre).

## Kretanje

Za kretanje se koriste jednadžbe Eulerove integracije:
```
acceleration = force / mass
velocity += acceleration * timestep
position += velocity * timestep
```

Za kretanje, dok je tipka smjera stisnuta postavi akceleraciju (ograničenu maksimalnom brzinom). Kada nema pozitivne akceleracije, aktiviraj trenje u suprotnom smjeru dok se brzina ne smanji na 0.

Za skakanje, kad je tipka stisnuta dodaj vertikalnu brzinu, i primjenjuj gravitaciju dok ne dotakne tlo. Za varijabilnu visinu skoka, dodaji vertikalnu brzinu dok je tipka stisnuta, do maksimalnog vremena (npr. 150 ms).

## Collision Detection

Pronalaženje kolizije među objektima najčešće se detektira korištenje *bounding boxa* u obliku pravokutnika ili kruga, što je lako izračunati. Kolizija između konveksnih poligona može se izračunati koristeći *Separating Axis Theorem* koji je kompleksniji i sporiji.

Da se kolizija ne bi provjeravala između svaka dva objekta, detekcija može u prvoj fazi tražiti samo objekte koji su možda u koliziji. Za ovo se koriste prostorne strukture poput Quad Trees, R-Trees ili Spacial Hashmaps.

## Pucanje

Unaprijed napravi pool metaka koje ćeš reusati. Za oružja koja pucaju metak po metak, pool size je 1. Za oružja koja pucaju rafalno, samo povećaj pool size.

Prateća raketa ima konstantnu pravocrtnu brzinu i brzinu zakretanja. Kut joj se podešava prema cilju brzinom zakretanja (`angle += TURN_RATE`), a vektor brzine računa pomoću kuta (`vx = cos(angle) * SPEED; vy = sin(angle) * SPEED` ). Za dodatni karakter, može se dodati wobble na angle koji oscilira između dvije vrijednost, npr. od -15 do 15 stupnjeva.

## Frame rate

U javascriptu, main loop se najčešće implementira kroz `requestAnimationFrame`, što daje browseru kontrolu da poziva loop kad je spreman za iscrtavanje novog framea. Nedostatak ovog je što će brzina igre ovisiti o brzini računala na kojem se izvodi.

Za konstantan frame rate, potrebno je izračunati vrijeme `delta` između dva framea, te pozvati `update` s fiksnim timestepom dok se `delta` ne potroši:
`while (delta >= timestep) { update(timestep); delta -= timestep; }`.

Pritom treba pripaziti da se za velike vrijednosti `delta` update ne izvršava predugo, što bi dovelo do još duže `delte` i još dužeg updatea sve dok se igra ne zamrzne. Zato se može dodati dodatna provjera i prekinuti update loop nakon određenog broja izvršavanja. U slučaju da se prečesto događa, možeš povećati `timestep`.

Timestep vrijednosti `1000/60` je dobar odabir je većina monitora radi na 60Hz. Ako igra zahtjeva puno izračuna, možeš ga povećati na `1000/30`, čime se frame rate efektivno ograničava na 30 FPS. Za preciznije simulacije i high-end gaming ekrane koriste se 75, 90, 120 i 144 FPS.

Korisno je pratiti FPS kako bi se lakše detektirao njegov pad, npr. koristeći exponential moving average. U slučaju da FPS postane prenizak, možeš reagirati tako da smanjiš kvalitetu grafike, aktivnosti izvan main loopa ili broj nekritičnih updateova.

U slučaju da `update` u main loopu i dalje traje predugo, dio koji se ne treba izvršavati između svaka dva framea možeš izbaciti u Web Worker. Taj dio će se izvoditi u zasebnom threadu što će osloboditi više vremena main loopu.

## AI

Praćenje se implementira tako da izračunaš vektor između lika i cilja i dodaš mu brzinu u tom smjeru. Ako želiš niz likova koji prate cilj, neka prvi prati cilj, a svaki idući prethodnoga (kao lanac). Za prirodnije kretanje, neka svaki pamti history kretanja, a pratitelj neka prati history cilja.

Za line of sight, povuci liniju između lika i cilja, te provjeri siječe li linija ikakve definirane prepreke (npr. zidove). Ako ne siječe, lik može vidjeti cilj.

# Literatura

* http://www.isaacsukin.com/news/2015/01/detailed-explanation-javascript-game-loops-and-timing
* https://gamemechanicexplorer.com
* http://gafferongames.com/game-physics/
* https://www.youtube.com/watch?v=SDS5gLSiLg0
