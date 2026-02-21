# Sesión 5: Cierre y Exhibición

La Sesión 5 es el momento en que los alumnos consolidan lo aprendido, pierden el miedo a mostrar su trabajo y se dan cuenta de que ya son parte de una comunidad de creadores.

> Como esta sesión es de exhibición, en lugar de introducir conceptos técnicos nuevos, se propone una estructura de dinámicas y una plantilla de código para organizar la obra final.

---

## Dinámica Propuesta

Te sugiero dividir esta última clase en cuatro bloques para mantener la energía alta y asegurarte de que todos participen:

### 1. Clínica de Código (Primeros 30 minutos)

Antes de empezar la muestra, siempre hay nervios y "bugs" de última hora.

- Abre un espacio para que los alumnos pregunten: *"¿Por qué mi código no suena?"* o *"¿Cómo hacía para que este patrón fuera más rápido?"*.
- Fomenta que los mismos compañeros ayuden a encontrar el error. Esto refuerza el aprendizaje colectivo.

### 2. La Muestra de Trabajos (Audición — 60 minutos)

Cada alumno (o grupo) tendrá unos minutos para presentar su creación.

- **Regla de oro:** No juzgamos la complejidad técnica, sino la exploración creativa. Un ruido blanco modulado con intención artística es tan válido como un algoritmo complejo.
- Pídeles que proyecten su código en la pantalla mientras suena. Ver el código de otros es una de las mejores formas de aprender.

### 3. Rúbrica de Feedback (Charla abierta)

Tras cada presentación, haz dos preguntas sencillas al creador y al grupo:

- **Al creador:** ¿Qué fue lo que más te costó programar y cómo lo resolviste?
- **Al grupo:** ¿Qué sensación visual o emocional les transmitió este sonido?

### 4. El Día Después (Recursos para seguir aprendiendo)

SuperCollider tiene una curva de aprendizaje infinita. Entrégales esta lista de recursos para que no se sientan solos al terminar el taller:

| Recurso | Descripción |
|---|---|
| [sccode.org](https://sccode.org) | El "Instagram" de SuperCollider. La comunidad sube códigos e instrumentos para descargar y modificar. |
| [Eli Fieldsteel (YouTube)](https://www.youtube.com/@eloufieldsteel) | La biblioteca de tutoriales en video más completa (en inglés). |
| [scsynth.org](https://scsynth.org) | Foro oficial, ideal para preguntas técnicas avanzadas. |

---

## Plantilla: Estructura de un Proyecto Final

Para ayudarles a preparar su presentación, te recomiendo entregarles esta plantilla durante la Sesión 4 o al inicio de la 5. Les enseñará a organizar su código como profesionales, dividiéndolo en bloques lógicos.

```supercollider
// ==========================================
// TÍTULO DE LA OBRA: [Nombre de su obra]
// AUTOR: [Nombre del alumno]
// ==========================================

// ------------------------------------------
// BLOQUE 1: INICIALIZACIÓN
// (Ejecutar esto primero)
// ------------------------------------------
s.boot; // Encender el servidor

// Limpiar todo por si acaso (opcional pero recomendado)
s.freeAll;
Window.closeAll;

// ------------------------------------------
// BLOQUE 2: CARGA DE MATERIALES (Sintetizadores y Audios)
// (Ejecutar este bloque completo con Ctrl/Cmd + Enter)
// ------------------------------------------
(
// 1. Receta del Sintetizador principal
SynthDef(\mi_instrumento, { arg freq = 440, amp = 0.5, pan = 0;
    var señal, env;
    env = EnvGen.kr(Env.perc(0.05, 2), doneAction: 2);
    señal = LFTri.ar(freq) * env;
    Out.ar(0, Pan2.ar(señal, pan, amp));
}).add;

// 2. Carga de buffers (si usan samples)
// ~miAudio = Buffer.read(s, "ruta/del/archivo.wav");

"Materiales cargados con éxito".postln;
)

// ------------------------------------------
// BLOQUE 3: SECUENCIACIÓN Y EJECUCIÓN (¡La Obra!)
// (Ejecutar para que comience la música/sonido)
// ------------------------------------------
(
~miObra = Pbind(
    \instrument, \mi_instrumento,
    \degree, Pseq([0, 2, 4, 7, 6, 4], inf), // Melodía
    \dur, Pwhite(0.2, 0.5, inf),             // Ritmo aleatorio
    \pan, Pseq([-0.8, 0, 0.8, 0], inf),     // Paneo dinámico
    \amp, 0.3
).play;
)

// ------------------------------------------
// BLOQUE 4: CONTROLES EN VIVO (Live Coding)
// (Ejecutar líneas individuales para alterar la obra en tiempo real)
// ------------------------------------------

// Cambiar la escala sobre la marcha
~miObra.stream = Pbind(\degree, Pseq([-2, 0, 1, 3], inf)).asStream;

// ------------------------------------------
// BLOQUE 5: APAGADO DE EMERGENCIA
// ------------------------------------------
~miObra.stop; // Detiene la secuencia suavemente
// O presionar Ctrl + .
```
