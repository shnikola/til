# Stripe

## Push vs Pull

Spremljena kreditna kartica ili bankovni račun su pull metode: plaćanja se izvršavaju tako da Stripe povlači novce iz njih.

Wire i bank transfers su push metode: customer mora poslati novce u Stripe.

## Vrste plaćanja

**Credit card** radi instantno - čim dobiješ podatke možeš napraviti charge ili platiti subscription invoice.

**ACH** je način bankovnog plaćenja za US. Korisnik šalje podatke o svom bankovnom računu (account number, routing number, ime vlasnika), i onda obavlja verifikaciju: to može instantno obaviti Plaid (vanjski servis koji se plaća), ili Stripe uplaćuje dva iznosa na korisnikov račun (problem je što to može trajati i par dana), pa korisnik mora unijeti koji su to iznosi u tvoju formu, što šalješ Stripeu. Stripe zatim uzima novac s korisnikovog računa, i to opet može trajati do 7 dana.

**ACH Credit Transfer** je jednostavniji - Stripe korisniku prikaže podatke za bankovnu uplatu, korisnik to obavi (npr. sa stranice svoje banke), i onda se novci dodaju u `Source` koji si prethodno stvorio. Taj `Source` objekt možeš koristiti za charge ili subscription. Koristiš `source.chargeable` event za prepoznati kad su novci stigli.

## Versioning

Stripe ima verziju API-ja koja se podešava u dashboardu (ili konfiguraciji gema), a zasebno ima i verziju gema.

## Timeouts and Idempotency

Scenarij koji se znao dogoditi: stripe gem napravi request da naplati customeru neki iznos. Taj request traje drugo (npr. zbog validacije kartice) i dogodi se timeout. Gem napravi retry requesta, ali zbog idempotent keya kojeg automatski uključuje kao odgovor dobije `409`, "There is currently another in-progress request using this Idempotent Key".

Stare verzije librarija bi ovaj odgovor protumačile kao error, i premda bi se prvi request s vremenom izvršio, charge ne bi bio zapisan u aplikacijsku bazu a klijentu bi se prikazala greška. Nakon toga bi korisnik opet pokušao napraviti purchase, on bi uspio, i dvaput bi mu bio naplaćen iznos.

Novije verzije librarija rade retry na `409`, pa se ovo rjeđe događa, ali i dalje je moguće. Zato je bitno napraviti webhookove koji će provjeriti je li naplaćen iznos doista zapisan u aplikacijskoj bazi, i ako nije, obavijestiti administratora.
