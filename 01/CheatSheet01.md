📄 Cheat Sheet: SuperCollider - Sesión 1
Conceptos básicos, atajos y sintaxis para sobrevivir (y hacer ruido) en el primer día.

⌨️ 1. Atajos de Teclado Esenciales (¡Tu salvavidas!)
Encender el Servidor: Ctrl + B (Windows/Linux) o Cmd + B (Mac). También puedes escribir s.boot;

Evaluar Código (Ejecutar): Shift + Enter (evalúa una línea) o Ctrl/Cmd + Enter (evalúa un bloque completo entre paréntesis).

🚨 ¡DETENER TODO EL SONIDO!: Ctrl + . o Cmd + . (Control + Punto). Apréndete este atajo, lo usarás mucho.

Limpiar la consola (Post Window): Ctrl/Cmd + Shift + P.

🧠 2. Conceptos Clave del Entorno
Cliente (sclang): Es el editor de texto donde escribimos nuestro código. Es el "cerebro".

Servidor (scsynth o s): Es el motor de audio. Es el "músculo" que genera el sonido. ¡No hay sonido si el servidor no está encendido!

UGen (Unit Generator): Son las piezas de lego de SuperCollider. Osciladores (SinOsc), filtros, ruido (WhiteNoise), etc.

.ar (Audio Rate): Se usa en los UGens que generan sonido audible (alta resolución, usualmente 44100 valores por segundo).

.kr (Control Rate): Se usa en los UGens que generan señales de control (como LFOs o moduladores). Usan menos recursos de tu computadora.

🔤 3. Sintaxis y Reglas Básicas
Punto y coma (;): Todas las instrucciones en SuperCollider deben terminar con un punto y coma.

Comentarios: El código que la computadora ignora. Se usa para tomar notas.

// Comentario de una sola línea

/_ Comentario de múltiples líneas _/

Mensajes y Métodos: Se escriben como objeto.mensaje. Ej: s.boot; (al servidor s le decimos que arranque boot).

Variables Globales: Llevan una tilde ~ al principio. Viven para siempre mientras SC esté abierto. Ej: ~miFrecuencia = 440;

Variables Locales: Se declaran con la palabra var. Solo viven dentro de un bloque de código { } o ( ). Ej: var volumen = 0.5;

🔊 4. Nuestra primera onda (Generando Sonido)
La forma más rápida de probar un sonido es envolver un UGen entre llaves { } y decirle .play.

Supercollider
// Estructura básica de un oscilador
{ SinOsc.ar(freq, phase, mul) }.play;

// Ejemplo práctico:
{ SinOsc.ar(440, 0, 0.1) }.play;
// 440 = Frecuencia (Hz)
// 0 = Fase
// 0.1 = Volumen (mul). ⚠️ ¡NUNCA USES 1.0 PARA EMPEZAR!
🎹 5. SynthDef: La Receta Profesional
Para hacer música o arte sonoro en serio, no usamos {}.play, sino que creamos recetas de sintetizadores (SynthDef) y luego los invocamos (Synth).

Paso A: Crear la receta y guardarla en el servidor (.add)

Supercollider
(
SynthDef(\mi_sinte, {
// 1. Argumentos (lo que podré cambiar en vivo)
arg freq = 440, amp = 0.2;

    // 2. Variables (nuestros cables internos)
    var señal;

    // 3. Generación del sonido
    señal = SinOsc.ar(freq) * amp;

    // 4. Salida de audio (Canal 0, señal estéreo)
    Out.ar(0, señal ! 2);

}).add;
)
Paso B: Usar la receta en vivo

Supercollider
// Crear un sonido usando nuestra receta
x = Synth(\mi_sinte);

// Cambiar parámetros en tiempo real
x.set(\freq, 880);
x.set(\amp, 0.5);

// Apagar este sonido específico
x.free;
🎛️ 6. Tipos de Modulación Básica
AM (Amplitud): Cambiamos el volumen de un sonido usando otro sonido. Crea efectos de trémolo. (Matemáticamente: se multiplica \*).

FM (Frecuencia): Cambiamos la afinación de un sonido usando otro sonido. Crea vibratos o timbres metálicos y complejos. (Matemáticamente: se suma + al argumento de frecuencia).

💡 Consejo de Oro: Si alguna vez te pierdes con un UGen, haz clic sobre la palabra (ej: SinOsc) y presiona Ctrl/Cmd + D para abrir la documentación oficial con ejemplos.
