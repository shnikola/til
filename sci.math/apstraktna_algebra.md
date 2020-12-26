# Algebra

Kažemo da je skup `M` **zatvoren** pod operacijom `•` ako je za svaka dva elementa definiran rezultat te operacije i nalazi se u skupu M, tj. `a•b ∈ M`. Takav uređen par `(M, •)` nazivamo **magma**, npr. `(N, pow)` je magma.

Operacija je **asocijativna** ako je možemo primjenjivati bilo kojim redom, tj. `(a⊕b)⊕c = a⊕(b⊕c)`. **Polugrupa** je magma s asocijativnom operacijom, odnosno uređeni par `(S, ⊕)`.

**Neutralni element** operacije je onaj za koji uvijek vrijedi `e ⊕ a = a`. **Monoid** je polugrupa s neutralnim elementom te operacije, odnosno uređena trojka `(M, ⊕, e)`.

Zadanom elementu neki element je **inverzan** ako `a⊕(-a)` daje neutralni element. **Grupa** je monoid kojem svaki element ima svoj inverz. Npr. `(R, +, 0)` je grupa gdje su svi inverzi negativni brojevi.

Operacija je **komutativna** ako možemo zamijeniti redoslijed elementima, a rezultat ostaje isti. **Komutativna ili Abelova grupa** je grupa čiji su svi elementi komutativni.

Operacija `•` je distributivna u odnosu na `⊕` ako je `a•(b⊕c)=(a•b)⊕(a•c)`. **Prsten** (ring) je kombinacija Abelove grupe i Monoida čije su operacije distributivne, odnosno petorka: `(R, ⊕, ⊗, 0, 1)`.

## Posebni slučajevi

Možemo posebno promotriti nizove elemenata (npr. stringovi ili liste). Neka je `A` skup vrijednosti koji zovemo *abeceda*. `A^1` je onda skup svih riječi duljine 1, `A^2 = A x A` je skup riječi duljine 2 itd. Neka je `A^0` riječ nulte duljine. Čitav vokabular ćemo dobiti ako zbrojimo skupove riječi svih duljina: `A* = A^0 + A^1 + A^2 + A^3 + ...`. `A*` se naziva **Kleene star**, odnosno Kleene plus `A+` ako izbacimo `A^0` (otud notacija `*` i `+` za regexe).

**Slobodni monoid** je poseban slučaj monoida nad skupom nizova, operacijom spajanje nizova (*concat*), odnosno uređena trojka `(A*, ⊕, A^0)`.
