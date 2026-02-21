Gemini ha dicho
¡Llegamos a la Sesión 4! Esta es, sin duda, una de las clases más emocionantes. Aquí es donde SuperCollider brilla con luz propia frente a otros programas de audio: vamos a enseñarle a la computadora a "tocar sola" creando secuencias, patrones rítmicos y algoritmos generativos. Además, abriremos los micrófonos para procesar el mundo real.

Aquí tienes los ejercicios prácticos paso a paso para esta cuarta sesión.

💻 Ejercicios Prácticos: Sesión 4
Recordatorio de rigor: ¡s.boot; para encender el motor antes de empezar!

Ejercicio 1: Iteración (Hacer que la máquina trabaje por nosotros)
La iteración nos permite repetir un proceso muchas veces con muy pocas líneas de código. Usaremos el método .do.

Supercollider
// 1. Repetir una acción simple 5 veces
5.do { "Hola, esto es una iteración".postln; };

// 2. Iteración con un "contador" (argumento 'i')
// La letra 'i' tomará el valor de 0, 1, 2, 3... en cada vuelta
10.do { arg i;
("Esta es la vuelta número: " ++ i).postln;
};

// 3. Aplicación sonora: Crear 5 sintetizadores al mismo tiempo con frecuencias aleatorias
5.do {
{ SinOsc.ar(rrand(200, 1000)) _ EnvGen.kr(Env.perc(0.01, 1), doneAction: 2) _ 0.1 ! 2 }.play;
};
Ejercicio 2: Rutinas y el control del Tiempo
Para hacer música, necesitamos que los eventos ocurran a lo largo del tiempo, no todos a la vez. Las Rutinas (Routine o fork) nos permiten pausar la ejecución del código usando el comando .wait.

Supercollider
(
// 'fork' es una forma rápida de crear y reproducir una Rutina
fork {
"Comenzando secuencia...".postln;

    // Bucle que se repetirá 4 veces
    4.do { arg i;
        // Hacemos sonar un "beep"
        { SinOsc.ar(440 + (i * 110)) * EnvGen.kr(Env.perc(0.01, 0.5), doneAction: 2) * 0.2 ! 2 }.play;

        "Sonido disparado. Esperando medio segundo...".postln;

        // PAUSAMOS la rutina por 0.5 segundos (120 BPM)
        0.5.wait;
    };

    "¡Secuencia terminada!".postln;

};
)
Ejercicio 3: Patrones (Patterns) y Pbind
Los Patterns son la herramienta definitiva de SuperCollider para la composición algorítmica. Un Pbind crea un flujo de eventos musicales (como una partitura virtual) y se los envía automáticamente a un sintetizador.

Supercollider
// SuperCollider trae un instrumento por defecto (\default) que usaremos aquí.

(
p = Pbind(
\instrument, \default, // Usamos el sinte por defecto

    // Pseq (Pattern Sequence): Reproduce los valores en orden.
    // El 'inf' significa que se repetirá infinitamente.
    \degree, Pseq([0, 2, 4, 7], inf), // Grados de la escala (Do, Mi, Sol, Do octava arriba)

    // Duración de cada nota en "beats" (pulsos)
    \dur, Pseq([0.5, 0.5, 0.25, 0.25], inf),

    // Amplitud (volumen)
    \amp, 0.3

).play; // ¡Le damos a play!
)

// Para detener el patrón sin detener el servidor entero:
p.stop;
Ejercicio 4: Creación de una pequeña obra Generativa
Vamos a combinar diferentes tipos de patrones para que la computadora tome decisiones musicales (aleatoriedad controlada).

Supercollider
(
// Pbindef es genial porque nos permite cambiar la secuencia MIENTRAS está sonando
Pbindef(\mi_generativo,
\instrument, \default,

    // Prand (Pattern Random): Elige un valor al azar de la lista
    \degree, Prand([0, 2, 3, 5, 7, 8, 10], inf), // Escala menor

    // Pwhite: Elige un número decimal al azar entre dos valores (mínimo y máximo)
    \dur, Pwhite(0.1, 0.5, inf), // Ritmo impredecible

    \pan, Pwhite(-0.8, 0.8, inf), // Paneo aleatorio izquierda-derecha
    \amp, 0.2

).play;
)

// ¡Ejecuta esta línea sin detener el sonido! Cambiaremos la escala en tiempo real:
Pbindef(\mi_generativo, \degree, Pseq([0, 1, 2, 3, 4, 5, 6, 7], inf));

// Detener
Pbindef(\mi_generativo).stop;
Ejercicio 5: Procesamiento de Señales Externas (SoundIn)
Vamos a tomar el sonido del micrófono de la computadora o de una tarjeta de sonido externa y le aplicaremos un efecto de Delay (Eco).

⚠️ ¡CUIDADO CON EL FEEDBACK (Acople)! Pide a los alumnos que usen auriculares para este ejercicio o que bajen el volumen de sus altavoces antes de ejecutarlo.

Supercollider
(
{
var entrada, efectoDelay;

    // 1. Capturamos el audio del micrófono (Canal 0)
    entrada = SoundIn.ar(0);

    // 2. Aplicamos un efecto de Eco (CombN)
    // CombN.ar(señal, tiempoMaximo, tiempoActual, decaimiento)
    efectoDelay = CombN.ar(entrada, 1.0, 0.3, 4.0);

    // 3. Mezclamos la entrada original limpia con el efecto, en estéreo
    (entrada + efectoDelay) ! 2;

}.play;
)
💡 Concepto clave para la clase: Es vital explicar la diferencia entre Patrones (Pseq, Prand) y Arreglos/Arrays ([1, 2, 3]). Un Array es solo una caja con datos. Un Patrón es un motor que sabe cómo ir sacando esos datos uno por uno a lo largo del tiempo.
