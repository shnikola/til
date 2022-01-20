# Erlang

## Procesi

BEAM je virtualna mašina (runtime layer) kojeg koriste Erlan, Elixir i srodni jezici. BEAM omogućava visoku razinu concurrencija koristeći actore koji se stvaraju brzo i troše minimalno memorije.

Centralni koncept BEAMa je **proces**, jedinica koja izvršava neki dani kod. Svaki proces je zaseban program: ima svoj execution flow, stack i heap, garbage collection. Proces nije nativni OS proces niti nativni OS thread, implementiran je mnogo optimalnije unutar samog VM-a, pa BEAM program može imati i milijune procesa.

Cijeli sustav se izvodi u jednom OS procesu BEAM VM-a. Za scheduling su zaduženi scheduler threadovi (najčešće ih ima koliko i CPU jezgri).
