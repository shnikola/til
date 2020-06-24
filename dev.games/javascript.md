# Javascript

## Matematika

Ograniči `v` na interval `[a, b]`:
`Clamp = (v, a, b) => Math.min(Math.max(v, a), b)`.

Ograniči kut `a` na interval `[-PI, PI]` (vidno polje):
`ClampAngle = (a) => (a+PI) % (2*PI) + (a+PI<0? PI : -PI)`

Linearno transformiraj postotak `p` na interval `[a, b]`:
`Lerp = (p, a, b) => a + Clamp(p, 0, 1) * (b-a)`

Jednostavni random generator, koristi decimalni dio sinusa:
`R = (a=1, b=0) => Lerp((Math.sin(++randSeed)+1)*1e5 % 1, a, b)`

## Codegolf

Za inicijalizaciju varijabli koristi `a = b = c = 0;`
Za funkcije `f=(a,b) => (...)`.

Cjelobrojni dio floata: `number | 0`
Decimalni dio floata: `number % 1`




