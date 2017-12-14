# Security

## Security vs Integrity

Security znači da nitko ne može doći do tvojih podataka.

Integrity znači da nitko ne može uništiti tvoje podatke.

## URI validation

https://blog.steve.fi/If_your_code_accepts_URIs_as_input__.html

Dopusti samo `http://` i `https://` inpute. Inače ti napadač može poslati `file:///etc/passwd`.

## Reflections on Trusting Trust by Ken Thompson

Trojanski konj je program koji glumi koristan program. Npr. unixov `login` koji osim što prihvati tvoju lozinku, šalje je i napadaču email. Ukoliko je promijenjen samo binary naredbe `login`, dovoljno ju je rekompilirati da uklonio zlonamjerni kod.

Ali ako napadač u compiler doda kod koji prepoznaje da se kompilira naredba `login` i doda joj zlonamjerni kod, trojanac će se održati čak i ako u potpunosti reinstaliramo `login` iz bilo kojeg sourca.

Ovo je moguće napraviti čak i bez da se ostavi trag u sourceu compilera. Prvo se u source compilera doda prepoznavanje kompiliranja `logina`, ali i prepoznavanje kompiliranja samog compilera. Zatim se taj binary spremi kao službeni C kompiler, a iz sourcea se izbriše spomenuti zlonamjerni kod. Svaka buduća reinstalacija kompilera će umetnuti ovaj samogenerirajući kodu binary kompilera, iz kojeg god sourca se on kompilirao.

## Literatura

* https://www.owasp.org/index.php/Category:Attack

## Tools

* https://github.com/rapid7/metasploit-framework - penetration testing, napisan u Rubyju.
