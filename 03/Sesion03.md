# Sesión 3: El Mundo Exterior y los Audios

La Sesión 3 es donde la magia se expande hacia el mundo real. Aquí dejamos de generar sonido desde cero (síntesis pura) para empezar a manipular grabaciones (archivos de audio) y a comunicarnos con otros programas o dispositivos (como un teléfono móvil) a través de la red.

> Para esta sesión, utilizaremos un archivo de audio que ya viene incluido de fábrica en SuperCollider para que todos los alumnos puedan ejecutar el código sin problemas.
>
> Recuerda: ¡`s.boot;` antes de hacer cualquier cosa!

---

## Ejercicio 1: Buffers (Cargando audios a la memoria)

Un **Buffer** es un espacio en la memoria RAM del servidor donde guardamos un archivo de audio para poder reproducirlo o manipularlo rápidamente.

```supercollider
// 1. Cargamos el archivo de audio por defecto de SuperCollider en una variable global.
// (Es un mensaje de voz clásico que dice "a11wlk01")
~miAudio = Buffer.read(s, Platform.resourceDir +/+ "sounds/a11wlk01.wav");

// 2. Podemos pedirle información al buffer para asegurarnos de que cargó bien
~miAudio.numChannels; // ¿Es mono (1) o estéreo (2)?
~miAudio.duration;    // ¿Cuántos segundos dura?
~miAudio.play;        // Reproducción rápida para escuchar qué es
```

---

## Ejercicio 2: El arte del Sampling (PlayBuf)

Para manipular el audio como un "sampler" profesional, usamos el UGen `PlayBuf`. Esto nos permite cambiar la velocidad, la afinación y hacer bucles (loops).

```supercollider
(
{
    var reproductor, velocidad;

    // Usamos el ratón (MouseX) para controlar la velocidad en tiempo real
    // De 0.5 (mitad de velocidad, más grave) a 2.0 (doble velocidad, más agudo)
    velocidad = MouseX.kr(0.5, 2.0);

    // PlayBuf.ar(numCanales, numeroDeBuffer, velocidad, bucle)
    reproductor = PlayBuf.ar(1, ~miAudio.bufnum, rate: BufRateScale.kr(~miAudio) * velocidad, loop: 1);

    reproductor ! 2; // Lo hacemos estéreo

}.play;
)
```

---

## Ejercicio 3: Síntesis Granular Básica (TGrains)

La síntesis granular consiste en picar un audio en pedacitos minúsculos (granos) de milisegundos de duración y reproducirlos como una "nube" o enjambre. Es ideal para texturas ambientales o efectos "glitch".

```supercollider
(
{
    var disparador, posicion, duracionGrano, pan, señal;

    // 1. Disparador (Impulse): Cuántos granos por segundo vamos a generar (ej. 20)
    disparador = Impulse.ar(20);

    // 2. Posición: En qué parte del audio estamos leyendo (usamos el ratón para "scrollear")
    posicion = MouseX.kr(0, BufDur.kr(~miAudio));

    // 3. Duración de cada grano en segundos (ej. 0.1 segundos)
    duracionGrano = 0.1;

    // 4. Paneo aleatorio para cada grano (entre izquierda -1 y derecha 1)
    pan = TRand.ar(-1.0, 1.0, disparador);

    // TGrains.ar(canales, disparador, buffer, rate, posicionLectura, duracion, paneo)
    señal = TGrains.ar(2, disparador, ~miAudio.bufnum, 1, posicion, duracionGrano, pan);

    señal * 0.5; // Bajamos un poco el volumen

}.play;
)
```

> **Nota para la clase:** Pídeles que muevan el ratón de izquierda a derecha lentamente para que escuchen cómo "congelan" el tiempo del audio.

---

## Ejercicio 4: Conectividad y Protocolo OSC (Open Sound Control)

OSC es el lenguaje estándar para conectar software de audio y video. Funciona enviando mensajes a través de una red (como Wi-Fi). Vamos a crear un receptor que "escuche" mensajes.

```supercollider
// 1. Creamos un "Definidor de OSC" (OSCdef) para escuchar un mensaje específico
(
OSCdef(\mi_receptor, { arg msg, time, addr, recvPort;
    // Esto se ejecutará cada vez que llegue el mensaje
    "¡Mensaje recibido!".postln;
    msg.postln; // Imprimimos el contenido del mensaje

    // Hacemos que suene un "beep" cada vez que llega el mensaje
    { EnvGen.kr(Env.perc(0.01, 0.1), doneAction: 2) * SinOsc.ar(880) * 0.1 ! 2 }.play;

}, '/gatillo'); // '/gatillo' es la "dirección" (address) que estamos escuchando
)

// 2. Simulamos enviar un mensaje desde "afuera" hacia nosotros mismos
// En el mundo real, esto vendría de un teléfono con TouchOSC, sensores,
// o de otro programa como Processing/Resolume.
NetAddr.localAddr.sendMsg('/gatillo', "Hola", 123);

// Para apagar o borrar nuestro receptor:
OSCdef(\mi_receptor).free;
```

---

## Ejercicio 5: Uniendo Sampling y OSC

Vamos a crear un sintetizador de sampler y lo vamos a disparar usando código o mensajes OSC.

```supercollider
(
SynthDef(\mi_sampler, { arg buf, rate = 1, amp = 0.5;
    var señal, env;

    // Envolvente para que no haga "click" al terminar
    env = EnvGen.kr(Env.linen(0.01, BufDur.kr(buf) / rate, 0.01), doneAction: 2);

    // Reproducimos el archivo sin loop
    señal = PlayBuf.ar(1, buf, BufRateScale.kr(buf) * rate, loop: 0);

    Out.ar(0, (señal * env * amp) ! 2);

}).add;
)

// Lo tocamos normal desde SuperCollider:
Synth(\mi_sampler, [\buf, ~miAudio, \rate, 1.2]);

// Creamos un OSCdef que lo dispare cuando reciba un mensaje externo:
(
OSCdef(\toca_sampler, { arg msg;
    // msg[1] será el valor de velocidad que le enviemos desde afuera
    Synth(\mi_sampler, [\buf, ~miAudio, \rate, msg[1]]);
}, '/tocar/audio');
)

// Probamos enviarle diferentes velocidades vía OSC:
NetAddr.localAddr.sendMsg('/tocar/audio', 0.8);
NetAddr.localAddr.sendMsg('/tocar/audio', -1); // ¡Velocidad negativa lo reproduce en reversa!
```

> **Concepto clave para la clase:** Es fascinante mostrarles a los alumnos que la velocidad de lectura (`rate`) en `PlayBuf` afecta directamente la afinación (pitch). Reproducir a velocidad 2.0 no solo hace que el audio dure la mitad del tiempo, sino que sube la afinación exactamente una octava (12 semitonos).
>
> Si además descargas una app gratuita en tu teléfono móvil (como **TouchOSC** o **OSCController**) y la conectas al IP de tu computadora durante la clase para disparar los audios, ¡los alumnos se quedarán alucinados!
