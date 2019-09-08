# Operacijski sustavi

## Povijest

Prvi programi su se ručno unosili u računalo, karticu po karticu. Uskoro su računala postala brža i unos je postao bottleneck. Zato su izumljeni prvi operacijski sustavi, sa zadatkom da slijedno učitavaju i izvršavaju batcheve programa.

S pojavom različitih periferalsa, postao je problem raditi programe koji rade sa svakim mogućim printerom. Operacijski sustavi su tu došli kao posrednik između korisničkog softwarea i hardwarea, pružajući apstrakcije kroz *device drivere*. Kada su računala postala dovoljno brza, printeri su postali prespori. Da se ne bi gubilo vrijeme na čekanje, više različith programa se vrtilo istovremo na istom procesoru, koristeći scheduling.

To dovodi do problema s memorijom. Ako jedan program promijeni memoriju, ne smije utjecati na drugi koji se istovremeno vrti. Zato svaki program dobija svoj blok memorije. Program može zahtjevati dodatnu memoriju prilikom rada, ali može se dogoditi da dobije nesekvencijalne segmente, što čini programiranje nezgodnim. Da bi sakrili ovu kompleksnost, operacijski sustavi virtualiziraju memoriju, tako da svaki program počinje od adrese 0, dok su prave fizičke adrese skrivene. Ovo omogućuje programima da imaju fleksibilnu veličinu memorije "dynamic memory allocation". Dodatna prednost je što su programi memorijski izolirani, pa jedan program s greškom ne može stvarati probleme ostalima - memory protection.

1970-ih računala su dovoljno brza da podržavaju više korisnika odjednom spojenih preko terminala - ekrana i tipkovnici koje nemaju procesor, nego su spojeni na veliko računalo. Najutjecajniji od takvih ranih timehsaring OS-ova bio je Multics, koji je bio prvi OS dizajniran da bude siguran po defaultu. Ali bio je prekompliciran i zahtjevao previše resursa, pa Dennis Ritchie i Ken Thompson stvaraju Unix. Podijelili su OS na dva dijela: Kernel koji se bavi hardwareom i schedulingom, i programska podrška s korisnim programima poput compilera i word processora.

