# Random

## asm.js

`emscripten` je alat koji kompajlira C/C++ kod u `asm.js`, podskup javascripta napravljen da se izvodi jako brzo. Neke od optimizacija su: koristi samo brojeve (nema stringova, booleana i objekata); svi podatci drže se u jednom velikom arrayu, heapu (nema globalnih varijabli, closurea i data structurea). `asm.js` se može izvršavati na svakom browseru, ali određeni browseri su toliko optimizirani da ga izvršavaju tek 2x sporije od nativnog C/C++ koda.
