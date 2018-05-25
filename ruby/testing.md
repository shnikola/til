# Testing

## Factories

Želiš testirati postavljanje imena novom korisniku:

`User.create(first_name: "Nikola")`
`expect(user.first_name).to eq "Nikola"`

Međutim, `User` model može zahtjevati validaciju prezimena, pa u `User.create` moraš dodati i `last_name:` koji nema veze s trenutnim testom. Kako bi to izbjegao, koristi factory koji ispunjava defaultne vrijednosti atributa koji se validiraju:

`user = create(:user, first_name: "Nikola")`

U većini slučajeva, ne treba ti factory. Koristi ga samo u slučaju required fieldova i validacija. U suprotnom samo kreiraj objekt na uobičajen način.
