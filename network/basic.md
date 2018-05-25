# Basic

## Slojevi mrežnog modela

Mreža je podijeljena u slojeve prema teoretskom OSI modelu od 7 layera. U praksi se koristi TCP/IP model koji je pojednostavljena implementacija OSI-ja.

* Link layer (1, 2): struja, frekvencije, ethernet, MAC adrese
* Network layer (3): IP, ICMP, ARP
* Transport layer (4): TCP, UDP, portovi
* Application layer (5, 6, 7): HTTP, FTP, DNS

Ako je neki alat "L3", znači da zna samo za IP adrese, a ignorira sve više protokole. Slično, L7 load balancer koristi HTTP headere (npr. Host).

## Network Addressing

*MAC adresa* je hardverski identifikator kojeg pri proizvodnji dobije svaka mrežna komponenta (npr. mrežna ili wifi kartica). Nepromjenjiva je.

*IP adresa* je softverski identifikator kojeg svaki uređaj (npr. računalo ili mobitel) dobije pri spajanju na mrežu. Može se mijenjati.

*Hostname* je varijanta IP adrese čitljiva ljudima (npr. `utorkom.com` umjesto `192.12.41.4`)

## Packet Anatomy

U mreži se komunicira pomoću paketa koji su građeni slojevito:
1. Aplikacija želi poslati payload, npr. HTTP request s headerima i bodijem.
2. Transportni sloj dijeli taj payload u manje pakete (segmente), a u primitku ih spaja ispravnim redom. U svaki segment zapisuje source i destination port kako bi se znalo na koji servis se šalju (npr. HTTP je port 80).
3. Network sloj uzima segment i oko njega dodaje svoju IP adresu i IP adresu odredišta. Na taj način paket zna odakle je došao i na koji server ide.
4. Link sloj enkapsulira paket u frame kojem dodaje svoju MAC adresu, i MAC adresu idućeg računala, što se ponavlja dok frame ne stigne do odredišta.

## Lifecycle HTTP requesta

1. Unesemo url wikipedia.org/login u browser.
2. Browser DNS-om dohvaća IP adresu za "wikipedia.org".
3. Browser šalje request na IP: URL, tko sam (User-Agent), što želim (Accept, Accept-Encoding), da održi TCP vezu (Connection), i stanje klijenta (Cookies)
4. Server odgovara s `301 Moved Permanently`, `Location: https://www.wikipedia.org/`
5. Browser šalje isti request na novu lokaciju.
6. Server generira HTML i vrati ga nazad.
7. Browser počinje renderirati HTML, radeći dodatne requestove pri nailasku na objekte koje treba dohvatiti (images, css, js). Svaki od tih dodatnih requestova prolazi kroz ovaj isti proces (uključujući i DNS). Ali statični fileovi se često serviraju iz browser cachea, koristeći Expires (datum) i ETag (verzija) headere.

