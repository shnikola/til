# Kompresija

Lossless: Run-length encoding, Dictionary coders (npr. huffman codes)
Lossy: Perceptual coding (izbacivanje informacije koje se ne mogu percipirati)

## Video encoding

Sliku prikazujemo kao 2D matricu piksela. Prikazujemo li boje pomoću tri primarne komponente (RGB), imamo tri 2D matrice čije su vrijednosti intenzitet boje. **Bit depth** označava koliko bitova koristimo za zapis intenziteta. Ako koristimo 8 bita po boji (vrijednosti 0-255), bit depth iznosi 24.

Rezolucija predstavlja veličinu jedne matrice, tj. `širina * visina`.
Display aspect ratio predstavlja odnos između širine i visine slike ili videa (npr. 16:9).
Pixel aspect ratio predstavlja odnos širine i visine samog piksela koji ne mora nužno biti kvadrat.

Video definiramo kao niz frameova u vremenu. **Bit rate** se računa kao `širina * visina * bit depth * frames per second`. Bit rate može biti konstantan (CBR), a može biti i promjenjiv (VBR).

Za prividno povećanje frame ratea ekrana bez da se poveća bandwith, prije se koristio *interlaced video* koji bi prikazao pola slike (u trakicama) u jednom frameu, a drugu polovicu u idućem. Danas ekrani koriste *progressive scan* koji omogućuje da se linije svakog framea slijedno iscrtavaju.

Bez kompresije 1h videa u 720p rezoluciji zauzimao bi 278GB. Jedan od načina kompresije iskorištava činjenicu da naše oči mnogo bolje raspoznaju svjetlinu nego boje. Umjesto RGB modela, koristimo **YCbCr** s tri kanala: brightness, chroma blue i chroma red. Znajući da je oko manje osjetljivo na boje, radimo *Chroma subsampling* koristeći manje rezolucije za chroma kanale (npr. 320x180, ako je original video 1280x720).

Drugi način kompresije je smanjenje redundancije. Umjesto zapisivanja cijelokupnih podataka za svaki frame, možemo zapisivati promjenu koja se događa između dva framea. Tako razlikujemo 3 vrste frameova: I-frame (sadrži sve podatke), P-frame (sadrži razliku u odnosi na prošli), B-frame (sadrži razliku u odnosu na prošli i budući)
