# IO

`IO` streamovi su dvosmjerni kanali između Ruby programa i nekog resourca (filea, socketa ili unix streama).

Streamovi se ne loadaju u memoriju, učitavaju se samo linije na kojima radiš. Zato možeš bez problema raditi na velikim fileovima, beskonačnim streamovima i socketima.

Standardni sistemski streamovi dostupni su u globalnim `$stdin`, `$stdout` i `$stderr`. `Kernel#puts` i `Kernel#gets` su zapravo aliasi na `$stdout.puts` i `$stdin.gets`.

Otvaranje streama:
* `IO.open(fd, "r")`, fd je file descriptor (int) koji se dobije od `IO.sysopen('file.txt')` ili `IO.sysopen('/dev/null')`. Potrebno je ručno zatvoriti stream s `io.close` nakon korištenja
* `IO.open(fd, "r") do` s blokom automatski zatvara stream na kraju bloka.

Mode može biti za:
* čitanje (`r`)
* pisanje (`w` piše preko postojećeg, `a` dodaje na kraj)
* čitanje + pisanje (`r+` piše preko postojećeg, `w+` briše sve i piše ispočetka, `a+` dodaje na kraj)

Svaki stream ima `external_encoding` (encoding teksta zapisanog u streamu) i `internal_encoding` (encoding stringa zapisanog u Rubyju). Možeš ih definirati pri otvaranju streama u modu: `w:ext_enc:int_enc`.

Čitanje streama:
* `io.getbyte` dohvaća idući byte. `io.getchar` za char (može imati više bytova). `io.each_byte` iterator po bytovima.
* `io.gets` dohvaća iduću liniju. `io.each_line` iterator po linijama. Linija će imati `\n` na kraju, pa koristi `chomp!`
* `io.read` dohvaća sve bytove do EOF.

Pisanje u stream
* `io.putc` zapisuje byte.
* `io.puts` zapisuje string + `\n` (ako ga već nema na kraju).
* `io.write` zapisuje string.

`io.flush` gura buffer u stream. `io.sync = true` flusha nakon svakog zapisa.
Za random access koristi `io.pos`, `io.eof?`, `io.seek` i `io.rewind`.

`IO.copy_stream(src, dest)` kopira sadržaj jednog IO streama u drugi. Vraća broj kopiranih bytova.

## File < IO

`File` nasljeđuje sve od `IO`, tako da može koristi `File.open`, `file.gets` itd.

Neke pomoćne metode:
* `File.read('file.txt')` otvara file, učita cijelog u string i zatvara ga. Korisno za male fileove.
* `File.readlines('file.txt')` isto kao i gore, samo vraća array s linijama.
* `File.foreach('file.txt')` itererira kroz linije filea bez da ga učita cijelog u memoriju.

## Tempfile

Ako trebaš privremeno stvoriti file (npr. imaš remote url neke slike i želiš je obraditi unix alatom koji prima samo file), koristi `Tempfile.new('some_id')`, s pathom u `tempfile.path`.

Prednost korištenja u odnosu na obični file:
* filename će uvijek biti unique i thread safe, pa se ne moraš brinuti o koliziji.
* obrisat će se automatski pri garbage collectionu ili izlasku iz programa.

## Sockets < IO

Socketi su najniža razina mrežne komunikacije u Rubyju. *UDP* šalje datagrame bez stalne konekcije, korisno kada trebaš brzu komunikaciju gdje redoslijed nije bitan. *TCP* stvara konekciju i garantira primitak svih paketa istim redoslijedom.

Osnovna komunikacija:
* `TCPSocket.open(host, port) do { |s| while line = s.gets ...}` otvara klijentsku stranu socketa.
* `TCPServer.new(port)` stvara server, `client = server.accept` čeka klijenta da se spoji i vraća socket za komunikaciju.

## Non-blocking IO

Metode za pisanje u IO stream su blokirajuće, tj. čekat će dok stream ne postane dostupan:
* `io.read(length)` čita `length` bytova čekajući dok svi nisu pročitani.
* `io.readpartial(length)` čeka dok stream ne bude dostupan za čitanje, i onda pročita do `length` bytova.
* `io.write(string)` čeka dok stream ne bude dostupan za pisanje, i onda šalje string.

Blokirajuće metode su ok ako imaš samo jedan stream, ali ako ih handlaš više, ne želiš čekati na jednome ako je drugi već spreman. U tom slučaju koristi neblokirajuće metode:
* `io.read_nonblock(length)` ako je stream dostupan, čita do `length` bytova. Ako nije dostupan, baca `IO::WaitReadable` exception. Ako ne voliš exceptione, `exception: false` opcija umjesto raisanja vraća `:wait_writable`.
* `io.write_nonblock(string)` ako je stream dostupan, zapisuje koliko uspije bytova. Ako nije, baca `IO::WaitWritable`.

Druga opcija je koristiti `IO.select(read_ios, write_ios)` koji blokira poziv dok jedan ili više danih streamova ne postane dostupno, onda vraća arraye tih koji su readable i writable . Radi na razini os kernela, pa je puno bolji nego loop po streamovima.

Za ozbiljan asinkroni I/O handling koristi frameworke poput `EventMachine` i `Celluloid`.

Za lightweight nadogradnju, `nio4r` nudi stateful `select` (ne moraš mu prosljeđivati array streamova svaki put), mogućnost timeouta, te lakše monitoriranje socketa s `readable?` i `writable?`.
