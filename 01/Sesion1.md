Siempre debemos encender el motor de audio (el servidor) antes de hacer ruido. Para evaluar código, enséñales el atajo Ctrl + Enter (Windows/Linux) o Cmd + Enter (Mac).

Ejercicio 1: Encender motores y nuestro primer mensaje
El objetivo aquí es entender la sintaxis básica, cómo imprimir mensajes en la consola y cómo comunicarnos con el servidor.

Supercollider
// 1. Encender el servidor (scsynth)
s.boot;

// 2. Imprimir un mensaje en la consola (Post window)
"¡Hola, mundo sonoro!".postln;

// 3. Métodos y mensajes simples: hacer una operación matemática básica
10.squared; // El receptor es '10', el mensaje es 'squared' (al cuadrado)
Ejercicio 2: Variables y operaciones básicas
Aquí introducimos el uso de variables globales (con la tilde ~) para almacenar datos que luego podremos reutilizar.

Supercollider
// Guardamos una frecuencia en una variable global
~miFrecuencia = 440;
~miVolumen = 0.2;

// Podemos realizar operaciones matemáticas con estas variables
~frecuenciaDoble = ~miFrecuencia \* 2;
~frecuenciaDoble.postln; // Debería imprimir 880 en la consola
Ejercicio 3: ¡A hacer ruido! (Producción de sonido)
Vamos a crear una función simple que genere sonido usando un oscilador de onda senoidal (SinOsc).

Supercollider
// Para detener el sonido en cualquier momento, presionar Ctrl+. o Cmd+.

// Creamos una función de audio usando llaves {} y le enviamos el mensaje .play
{ SinOsc.ar(440, 0, 0.2) }.play;

// Explicación de los argumentos de SinOsc.ar(freq, phase, mul):
// ar = Audio Rate (se ejecuta a velocidad de audio, ej. 44100 veces por segundo)
// 440 = Frecuencia en Hertz (La 4)
// 0 = Fase
// 0.2 = Multiplicador (funciona como el volumen, de 0.0 a 1.0. ¡Nunca empezar en 1!)
Ejercicio 4: Modulación de Señales (AM y FM)
Aquí la cosa se pone interesante. Vamos a usar un oscilador para controlar (modular) a otro oscilador.

Síntesis AM (Modulación de Amplitud): Modulamos el volumen. Da un efecto de Trémolo.

Supercollider
{
var portadora = SinOsc.ar(440); // Genera el tono principal
var moduladora = SinOsc.kr(4); // kr = Control Rate (oscila lento, 4 veces por seg)

    // Multiplicamos la portadora por la moduladora
    portadora * moduladora * 0.2;

}.play;
Síntesis FM (Modulación de Frecuencia): Modulamos la afinación. Da un efecto de Vibrato (o timbres complejos a altas velocidades).

Supercollider
{
var moduladora = SinOsc.kr(5, 0, 50); // Oscila 5 veces por seg, mueve la afinación 50 Hz

    // Sumamos la moduladora directamente al argumento de frecuencia de la portadora
    var portadora = SinOsc.ar(440 + moduladora);

    portadora * 0.2;

}.play;
Ejercicio 5: Creación de Sintetizadores (SynthDef)
Las funciones {}.play son rápidas, pero ineficientes. Lo correcto es crear "recetas" de sonido (SynthDef) y guardarlas en el servidor para llamarlas cuando queramos.

Supercollider
// 1. Definimos y guardamos el sintetizador (receta)
(
SynthDef(\mi_primer_sinte, {
// Declaramos argumentos. Esto nos permitirá cambiarlos después
arg freq = 440, amp = 0.2;

    var sonido;
    sonido = SinOsc.ar(freq) * amp;

    // Out.ar envía el sonido a los altavoces (el canal 0 es el izquierdo/derecho)
    Out.ar(0, sonido ! 2); // "! 2" duplica la señal para que suene en estéreo

}).add; // .add lo envía al servidor
)

// 2. Ahora "instanciamos" (cocinamos) el sonido usando la receta
x = Synth(\mi_primer_sinte);

// 3. Podemos cambiar sus parámetros en tiempo real gracias a los argumentos
x.set(\freq, 880); // Cambiamos a 880 Hz
x.set(\amp, 0.05); // Bajamos el volumen
x.set(\freq, 220); // Bajamos a 220 Hz

// 4. Liberamos/apagamos el sintetizador
x.free;
