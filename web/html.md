# HTML

## Viewport meta tag
Kad mobile browser otvara stranicu, pretpostavlja da želimo vidjeti čitavu u desktop veličini,
pa postavlja viewport width na 960px. I onda sve izgleda sitno i odzumirano.

`<meta name="viewport" content="width=320">` postavlja tu širinu na 320px.
`<meta name="viewport" content="width=device-width">` još bolje, postavlja da prati širinu uređaja.
`<meta name="viewport" content="initial-scale=1">` postavlja početni scale na 1:1 - nema zoomiranja.
`<meta name="viewport" content="maximum-scale=1">` disabla zoom.


## <details> i <summary>
```
<details>
  <summary> More </summary>
  Blah blah
</details>
```
Chrome, Safari i Android nativno prikažu exapandable "> More". Firefox i IE, još ne :/
