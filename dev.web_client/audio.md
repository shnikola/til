# Web Audio API

`const context = new AudioContext()` stvara audio kontekst spojen na zvučnike uređaja. Potreban ti je samo jedan za cijeli dokument.

## From file

`fetch('sound.mp4')` dohvaća audio file.
`.then(response => response.arrayBuffer)` učitava ga u memoriju kao ArrayBuffer.
`.then(arrayBuffer => context.decodeAudioData(arrayBuffer)` pretvori ga iz mp3 u raw audio data.
`.then(audioBuffer => ...)` audio data koji možemo koristiti.

## Playing

Za sviranje AudioBuffera:
`const source = context.createBufferSource()` stvara izvor.
`source.buffer = audioBuffer` šalje audio data koji smo loadali.
`source.connect(context.destination)` spaja izvor na zvučnike.
`source.start()` pušta audio.
`source.stop()` ga zaustavlja.

