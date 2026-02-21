💻 Ejercicios Prácticos: Sesión 2
Recuerda a los alumnos: ¡Siempre iniciamos encendiendo el servidor! s.boot;

Ejercicio 1: Arrays (Listas de objetos) y Expansión Multicanal
Un Array es simplemente una lista de elementos entre corchetes [ ]. En SuperCollider, si le pasamos un Array a un oscilador, este se "clona" automáticamente para cada valor.

Supercollider
// 1. Un Array básico de números
~misFrecuencias = [440, 880, 1200];
~misFrecuencias.choose.postln; // Elige uno al azar y lo imprime

// 2. Expansión multicanal: Al darle dos frecuencias a SinOsc,
// SuperCollider crea un canal izquierdo (400Hz) y uno derecho (800Hz) automáticamente.
{ SinOsc.ar([400, 800]) \* 0.1 }.play;
Ejercicio 2: Síntesis Aditiva (Sumando sonidos simples)
La síntesis aditiva consiste en sumar formas de onda simples (como senoidales) para crear un sonido más complejo, simulando los armónicos de un instrumento.

Supercollider
(
{
var armonico1 = SinOsc.ar(200) _ 0.5;
var armonico2 = SinOsc.ar(400) _ 0.25; // El doble de frecuencia, mitad de volumen
var armonico3 = SinOsc.ar(600) _ 0.125;
var armonico4 = SinOsc.ar(800) _ 0.06;

    // Sumamos todos los armónicos y los dividimos para que no sature
    var mezcla = (armonico1 + armonico2 + armonico3 + armonico4) / 4;

    // Duplicamos para estéreo
    mezcla ! 2;

}.play;
)

// MODO AVANZADO (Usando Mix y Arrays para escribir menos):
{ Mix(SinOsc.ar([200, 400, 600, 800], 0, [0.5, 0.25, 0.125, 0.06])) ! 2 }.play;
Ejercicio 3: Síntesis Sustractiva (Esculpiendo el sonido)
Aquí hacemos lo contrario: partimos de un sonido muy rico en frecuencias (como el ruido blanco o una onda sierra) y usamos un filtro para quitarle (sustraerle) lo que no queremos. ¡Como un escultor con un bloque de piedra!

Supercollider
// 1. La "piedra": Ruido blanco puro (suena fuerte, ¡cuidado!)
{ WhiteNoise.ar(0.1) ! 2 }.play;

// 2. Esculpiendo: Filtro Pasa-Bajos (LPF - Low Pass Filter)
// Deja pasar los graves y corta los agudos a partir de 800 Hz
{ LPF.ar(WhiteNoise.ar(0.5), 800) ! 2 }.play;

// 3. Modulando el filtro: Hacemos que la frecuencia de corte se mueva sola
{
var ruido = WhiteNoise.ar(0.5);
var moduladorFiltro = SinOsc.kr(0.5).range(200, 2000); // Se mueve entre 200 y 2000 Hz

    // Aplicamos un filtro pasa-banda (BPF) que se mueve con el modulador
    BPF.ar(ruido, moduladorFiltro, 0.2) ! 2;

}.play;
Ejercicio 4: Envolventes (Dándole vida y duración al sonido)
Hasta ahora, nuestros sonidos suenan para siempre hasta que presionamos Ctrl+.. Las envolventes (Env y EnvGen) nos permiten definir cómo nace, se mantiene y muere un sonido.

Supercollider
(
SynthDef(\perc_aditiva, {
var señal, envolvente;

    // 1. Creamos la envolvente. Env.perc tiene un ataque rápido y una caída gradual.
    // doneAction: 2 es SUPER IMPORTANTE. Le dice al servidor:
    // "Cuando termine el sonido, destrúyelo y libera la memoria RAM".
    envolvente = EnvGen.kr(Env.perc(attackTime: 0.01, releaseTime: 1.5), doneAction: 2);

    // 2. Generamos el sonido (una onda triangular)
    señal = LFTri.ar(440);

    // 3. Multiplicamos la señal por la envolvente para darle esa forma de volumen
    señal = señal * envolvente * 0.2;

    Out.ar(0, señal ! 2);

}).add;
)

// ¡Pruébalo! Ejecuta esta línea varias veces seguidas:
Synth(\perc_aditiva);
// Abre el monitor del servidor (abajo a la derecha) y nota cómo los "Synths" aparecen y desaparecen solos gracias al doneAction: 2.
Ejercicio 5: Aleatoriedad (Que el código decida por nosotros)
Para que nuestro sintetizador anterior no suene siempre igual, vamos a añadirle procesos de randomización.

Supercollider
(
SynthDef(\campana_random, {
// Declaramos argumentos que recibiremos desde afuera
arg freq = 440;

    var señal, env;

    // Envolvente percusiva más larga
    env = EnvGen.kr(Env.perc(0.01, 3), doneAction: 2);

    // Generador senoidal
    señal = SinOsc.ar(freq) * env * 0.2;

    Out.ar(0, señal ! 2);

}).add;
)

// Ahora, cada vez que ejecutemos esta línea, elegirá una frecuencia al azar (rrand)
Synth(\campana_random, [\freq, rrand(200, 2000)]);
💡 Concepto clave para la clase: Es muy importante hacer énfasis en el doneAction: 2 de las envolventes. Puedes pedirles a los alumnos que creen un SynthDef sin doneAction: 2, que lo ejecuten 50 veces y que miren cómo los números en su servidor crecen hasta que la computadora colapsa o el sonido se distorsiona. ¡Aprenderán rápidamente por qué es necesario limpiar la memoria!
