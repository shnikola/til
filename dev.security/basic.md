# Security

## Security vs Integrity

Security znači da nitko ne može doći do tvojih podataka.

Integrity znači da nitko ne može uništiti tvoje podatke.

## Reflections on Trusting Trust by Ken Thompson

Trojanski konj je program koji glumi koristan program. Npr. unixov `login` koji osim što prihvati tvoju lozinku, šalje je na napadačev email. Ukoliko je promijenjen samo `login` binary, dovoljno je rekompajlirati iz sourca da bi se uklonio zlonamjerni kod.

Ali ako napadač u compiler doda kod koji prepoznaje da se kompajlira naredba `login` i doda joj zlonamjerni kod, trojanac će se održati čak i ako u potpunosti reinstaliramo `login` iz bilo kojeg sourca.

Ovo je moguće napraviti čak i bez da se ostavi trag u sourceu compilera. Prvo se u source compilera doda prepoznavanje kompiliranja `logina`, ali i prepoznavanje kompiliranja samog compilera. Zatim se taj binary spremi kao službeni C kompiler, a iz sourcea se izbriše spomenuti zlonamjerni kod. Svaka buduća reinstalacija kompilera će umetnuti ovaj samogenerirajući kodu binary kompilera, iz kojeg god sourca se on kompilirao.

## Literatura

* https://www.owasp.org/index.php/Category:Attack
* https://jdow.io/blog/2018/03/18/web-application-penetration-testing-methodology

## Tools

* https://github.com/rapid7/metasploit-framework - penetration testing, napisan u Rubyju.
