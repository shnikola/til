# Geometrija

## Mjerenje veličine Zemlje

U Asuanu je postojao duboki bunar, i za vrijeme ljetnog solsticija bi se sunce vidilo na njegovom dnu, tj. sunce bi bilo direktno iznad njega. **Eratosten** je pretpostavio da je sunce dovoljno daleko da njegove zrake padaju gotovo paralelno na Zemlju. Zato u Aleksandriji, oko 1000km južnije, na isti dan sunce pada pod kutem koji se može izračunati pomoću štapa i njegove sjene: `tan(kuta) = duljina sjene / visina štapa`. Taj kut je i kut koji Ausan i Aleksanrija zatvaraju sa središtem zemlje, pa se pomoću njihove udaljenosti i kuta mogu istračunati i opseg i radijus zemlje.

## Rezanje

Na koliko se djelova može izrezati krug s N pravaca?

0 pravaca dijeli krug na 1 dio. 1 pravac dijeli na 2 djela. 2 pravca dijele ga na 4 dijela. 3 pravac se sjeće s prethodna dva, i oni ga dijele na 3 sekcije. Svaka od tih sekcija djeli prethodni komad na dva djela, tj. efektivno dodaje novi komad. Svaki idući pravac će sjeći sve ostale pravce, biti dijeljen na sekcije, i dodavati nove djelove. Dakle `n-ti` rez će dodati `n` novih djelova.

Ispečeš baklavu, i želiš je podijeliti na pola, ali netko izreže pravokutnik iz sredine (čak ne mora biti ni paralelan s tepsijom). Kako podijeliti baklavu s rupom na dva jednaka dijela?

Svaki pravac koji prolazi sjecištem dijagonala dijeli pravokutnik na dva jednaka dijela. Nađi sjecište dijagonala rupe, i tepsije, pa napravi rez po pravcu kroz te dvije točke.
