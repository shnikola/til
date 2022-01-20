# Procesi

`fork { ... }` stvara novi `ruby` proces koji će izvoditi naredbe unutar bloka, dok će parent nastaviti izvoditi naredbe nakon `fork`. Vraća `pid` stvorenog subprocesa. Ruby 2.0+ podržava copy on write.

`if pid = fork ... else  ...` stvara novi proces koji će vratiti `nil` i izvoditi `else` dio izraza, dok će parent vratiti `pid` i izvoditi gornji `if` dio.

`exit(code)` prekida proces i vraća status `code`. Poziva se `at_exit` handler.
`exit!(code)` prekida proces, vraća `code`, ali ne poziva `at_exit`.

`Process.wait(pid)` blokira trenutni proces dok se proces `pid` ne završi.
`Process.wait` blokira dok se bilokoji subproces ne završi, vraća njegov `pid`.
`Process.wait2` vraća `pid` i `exit_code` završenog subprocesa.
`Process.waitall` blokira dok svi subprocesi ne završe.

Ukoliko se subproces završi prije nego parent pozove `wait`, kernel će tu informaciju queuati i bit će dostupna kada parent konačno pozove `wait`. Tako se izbjegavaju bilo kakvi race conditioni.

Ukoliko parent ne čeka svoj subproces da završi, njegov exit status će ostati queuean i zauzimati resurse - postat će zombie proces. Stoga, ako ne koristiš `Process.wait(pid)`, koristi `Process.detach(pid)` koji interno stvara novi thread čiji je jedini zadatak da čeka child process i pokupi njegov exit status.

Procesi komuniciraju streamovima preko *pipea* ili porukama preko *socketa*.

`reader, writer = IO.pipe` stvara pipe za jednosmjernu komunikaciju. Proces koji piše učinit će `reader.close` i `writer.write`, a proces koji čita `writer.close` i `reader.read`.

`child, parent = Socket.pair(:UNIX, :DGRAM, 0)` stvara par Unix socketa za dvosmjernu komunikaciju. Child proces koji piše učinit će `parent.close`, primati poruke s `child.recv(maxlen)` i slati ih s `child.send(msg, 0)`. Parent radi isto, samo radi `child.close` i komunicira s `parent` socketom.

## Pozivanje vanjskih procesa

`exec("ls")` ako želiš prekinuti trenutni Ruby proces i zamjeniti ga zadanom naredbom.

``ls`` ili `%x{ls}` pokreće novi proces i blokira postojeći dok se ovaj ne dovrši. Vraćaju `stdout` vanjskog procesa kao rezultat, a status možeš dohvatiti iz `$CHILD_STATUS.success?`. Exceptioni se prosljeđuju u Ruby proces.

`system("ls")` pokreće novi proces i blokira postojeći. Vraća exit status (`true` ili `false`) kao rezultat, a output se printa na `stdout`. **Exceptioni ne dolaze** do Ruby procesa.

`Open3.popen3('grep nikola') do |stdin, stdout, stderr, wait_thr|` pokreće neblokirajući proces. U bloku koristi `stdin`, `stdout`, `stderr` io objekte za streamanje inputa i outputa u vanjski proces. Exit status dobiješ iz `wait_thr.value`.

`Open3.pipeline('sort', 'uniq -c', in: 'file.txt', out: 'count.txt')` omogućuje ulančavanje više vanjskih procesa s pipelinima.

## Signals

U slučaju da se multithreaded procesu pošalje signal, kernel će nasumično odabrati jedan thread kojem će prenijeti taj signal. Kako bi izbjegao probleme koje to može izazvati, MRI (i druge Ruby implementacije) pri pokretanju stvaraju poseban thread koji je zadužen za handlanje signala i pipeom ih šalje main threadu.

`Process.kill("INT", pid)` šalje signal procesu s danim `pid`. `INT` će uzrokovati isti signal kao da si stisnuo `CTRL+C` u terminalu.

`Signal.trap("INT") do ... end` definira globalno handlanje signala u
trenutnom procesu. Moguće je definirati samo jedan handler po signalu, pa pripazi da ne overridaš handler koji je neki gem dodao.

`Signal.trap("INT", "IGNORE")` definira ignoriranje `INT` signala u trenutnom procesu.

Neuhvaćeni i neignorirani signali podižu `SignalException`, pa ako želiš handlati signal samo u određenom dijelu koda možeš koristiti `rescue`. Npr. `rescue Interrupt` za handlanje `INT` signala.

Imaj na umu da je dojava signala nepouzdana. Npr. ako proces handla `INT` signal dok mu se šalje drugi `INT` signal, ovaj drugi signal možda neće ni primiti.
