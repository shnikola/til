# Devices

Unix omogućuje jednaki način pristupa svim vrstama uređaja (*devices*). Svi uređaji koje OS prepozna imaju svoj file (*device node*) u `/dev` directoriju. Osim imena, device node ima major number (koji driver je zadužen za njega) i minor number (njegov id za taj driver). `ls -l` za više detalja. Device node može predstavljati:
* *block device* (dozvoljava random access, npr. `/dev/sda` za disk)
* *character device* (pristupa mu se slijedno kao streamu, npr. `/dev/fd/0` tj. `/dev/stdout`)
* *pseudo device* (npr. `/dev/null` ili `/dev/random`)

Fizički device može se podijeliti na više logičkih cijelina (*partitions*). Svaka particija će imati svoj device node u `/dev` directoriju. Particije olakšavaju backup i povećavaju sigurnost. Čak i ako jedna ode kvragu (npr. ostane bez slobodnog mjesta), druge će nastaviti raditi.

Da bi se moglo pristupiti fileovima devicea ili particije, potrebno je napraviti *mounting*. Mounting povezuje filesystem na block deviceu s nekim directorijem. Kada se eksterni disk ili DVD spoji na računalo, mounting se dogodi automatski u directory `/mnt` (`/Volumes` za OSX) zahvaljujući `udevd`deamonu. Automatsko mountanje pri bootu može se podesiti u `/etc/fstab` fileu.

`fdisk <device>` kreira novu particiju.
`mkfs -t <type> <device>` stvara filesystem na danom deviceu/particiji. Pritom briše sve podatke s njega.
`mount <device> <directory>` mounta device u dani directory. Ako nije directory nije zadan, koristit će `/etc/fstab` file.
`fsck <device>` provjerava ima li grešaka na deviceu.

`df -h` ispisuje sve mountane uređaje i slobodno mjesto na njima. Dobro za provjeru mjesta na disku.
`findmnt` ispisuje sve mountane filesysteme i njihove opcije.
`lsblk` ispisuje sve block deviceove.
`lsbusb`/`lspci` ispisuje sve USB/PCI busove.

## Swap Memory

Svaki sustav bi trebao imati definiran swap space na disku koji može koristiti kada mu zafali RAMa.

Swap se može držati u swap particiji (`fdisk <device>`) ili u swap fileu (`dd if=/dev/zero of=/extraswap bs=1M count=512`).

`mkswap <device or file>` inicijalizira swap space.
`swapon <device or file>` aktivira swap space i počinje ga koristiti. Za automatsko aktiviranje pri bootu dodaj u `/etc/fstab`.

`cat /proc/swaps` (bez argumenta) ispisuje trenutno aktivne swapove.
