# Implementacija jezika

Evaluacija svakog jezika počinje **parsiranjem** teksta s kodom. Ako je sintaksa važeća, parser kao rezultat vraća sintaksno stablo (*abstract syntax tree*)

Ako jezik koristi type checker, u ovom se trenutku provjeravaju tipovi svakog nodea u stablu.

Nakon toga postoje dvije opcije:
1) Koristi se *interpreter* koji uzima AST, izvodi ga i vraća rezultat
2) Koristi se *compiler* koji uzima AST i prevodi ga u neki drugi jezik (npr. strojni kod)

Mnogi jezici kombiniraju obje opcije, npr. Java source se compilira u Java bytecode, kojeg interpretira JVM (a tijekom interpretacije se zbog performansi dio byte koda compilira direktno u strojni kod).

Jezik u kojem su napisani intepreter/complier nazivamo *metalanguage*.

Bitno je naglasiti da intepreter/compiler odabir nije dio programskog jezika, već njegove implementacije. Ne postoje "compilirani" ili "interpretirani" jezici - svaki jezik se može compilirati i interpretirati. Činjenica da se C compilira, a funkcijski jezici interpretiraju je samo posljedica povijesnog razvoja, a ne svojstvo samih jezika.

## Macro

Macroi definiraju novu sintaksu jezika koristeći postojeću. Nova sintaksa će biti zamijenjena starom (*expanded*) prije parsiranja samog programa.

Macroi se expandaju na token levelu pa definicija `car -> head` neće zamijeniti `cart`.

Higijenski macroi će se pobrinuti da nova sintaksa neće doći u koliziju s postojećom, npr:
* održati asocijativnost. C macro `#define ADD(a,b) a+b` neće ispravno raditi za `ADD(a,b)*2`, pa se stavljaju zagrade oko svakog izraza: `#define ADD(a,b) ((a)+(b))`
* zamijeniti sve lokalne varijable macroaa s novim imenima. C macroi se pišu velikim slovima kako bi se izbjegla kolizija.
* varijable korištene u macrou bindati na lexical scope, tj. mjestu gdje je macro definiran.