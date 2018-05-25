# Statistics

## Mode, Median, Mean

Promatrajući sortirani niz vrijednosti `x = [...]`, želimo pronaći jednu vrijednost koja će ga najpreciznije opisati. Postoji nekoliko opcija:
* `mode` je vrijednost koja se najčešće pojavljuje u nizu.
* `median` je vrijednost koja se nalazi u sredini niza.
* `mean` je prosječna vrijednost elemenata niza.

Postoji logičan način kako se dođe do ova 3 pojma. Traženje opisne vrijednosti `m` zapravo je traženje vrijednosti koja se najmanje razlikuje svih vrijednosti niza. Pitanje je samo kako definiramo "razliku".
* Ako je razlika `|x[i] - m|^0`, svaki različit element brojimo kao `1`, a svaki jednaki kao `0`. Ukupna razlika će biti najmanja kada je `m` vrijednost koja se najčešće pojavljuje - mod.
* Ako je razlika `|x[i] - m|^1`, mjerimo apsolutnu razliku između svakog elementa i `m`. Ukupna razlika je najmanja kada koristimo medijan.
*  Ako je razlika `|x[i] - m|^2`, mjerimo kvadrat razlike između svakog elementa i `m`. Ukupna razlika je najmanja kada koristimo mean.

