# Random

## Behaviour profiling
Vjerovao ili ne, ali način na koji tipkaš i mičeš miša može se prilično precizno koristiti za autentifikaciju.
Postoje firme koje razvijaju to za security (npr. `BehavioSec`), ali problem je što se to jednako lako može koristiti za narušavanje privatnosti i trackanje korisnika. Stoga je ovaj lik napravio neku ekstenziju koja sjebe te prepoznavače.

## Pasting in password fields
https://www.troyhunt.com/the-cobra-effect-that-is-disabling
Jedini sigurni password je onaj kojeg ne možeš zapamtiti. Zato je nužno omogućiti paste iz nekog password managera.
Nijedan argument protiv pasteanja ne drži vodu:
 - Brute force napadi se jednako lako mogu izvesti bez pasteanja.
 - Malware koji ti krade clipboard ti može jednako pratiti koje tipke ukucavaš.
 - Zabraniti paste zbog limita duljine - nema smisla ograničavati duljinu passworda. Password bilo koje duljine uvijek će se pretvoriti u hash iste duljine.
