# Random

## optimize-js

https://github.com/nolanlawson/optimize-js
Moderni JS engini pretpostavljaju da se većina funkcija neće odmah izvoditi, pa rade samo pre-parse koji provjerava ispravnost sintakse. Ali za immediately invoked funkcije ovo će zapravo usporiti izvođenje, jer će se raditi pre-parse i onda puni parse.
`optimize-js` pri buildanju dodaje zagrade na sve IIFE funkcije, kako bi se engineu dalo do znanja da prekoči pre-parse korak. Time se dobije speed boost i do 50%.

## asm.js

`emscripten` je alat koji kompajlira C/C++ kod u `asm.js`, podskup javascripta napravljen da se izvodi jako brzo. Neke od optimizacija su: koristi samo brojeve (nema stringova, booleana i objekata); svi podatci drže se u jednom velikom arrayu, heapu (nema globalnih varijabli, closurea i data structurea). `asm.js` se može izvršavati na svakom browseru, ali određeni browseri su toliko optimizirani da ga izvršavaju tek 2x sporije od nativnog C/C++ koda.
