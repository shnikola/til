# Random

## Ubercookie Fingerprint
http://ubercookie.robinlinus.com/
Koristi `AudioContext` i `getClientRects` da fingerprinta i razlikuje čak i identične uređaje.


## Behaviour profiling
Vjerovao ili ne, ali način na koji tipkaš i mičeš miša može se prilično precizno koristiti za autentifikaciju.
Postoje firme koje razvijaju to za security (npr. `BehavioSec`), ali problem je što se to jednako lako može koristiti za narušavanje privatnosti i trackanje korisnika. Stoga je ovaj lik napravio neku ekstenziju koja sjebe te prepoznavače.


## If your code accepts URIs as input
https://blog.steve.fi/If_your_code_accepts_URIs_as_input__.html
Dopusti samo `http://` i `https://` inpute. Inače ti napadač može poslati `file:///etc/passwd`.
