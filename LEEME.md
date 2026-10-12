# Prisma — juego de memoria PWA

## Probar en computadora
1. Descomprime `memoria_cromatica_pwa.zip`.
2. Entra a la carpeta desde una terminal y ejecuta un servidor local, por ejemplo:
   `python3 -m http.server 8000`
3. Abre `http://localhost:8000`. El service worker necesita `localhost` o HTTPS; abrir `index.html` directamente no activa el modo offline.

## Instalar en el celular
- Sube la carpeta a un hosting estático con HTTPS (por ejemplo, GitHub Pages).
- En iPhone: abre la URL en Safari → Compartir → Añadir a pantalla de inicio.
- En Android: abre la URL en Chrome → menú → Instalar aplicación / Añadir a pantalla de inicio.
- Abre una vez la aplicación con conexión para que el service worker guarde los archivos. Después podrá abrirse sin conexión.

## Mecánica
- Memoriza la secuencia dentro del tiempo elegido; tocar «Empezar ronda» inicia el contador.
- Cuando termina el tiempo, toca los bloques del banco para llenar las posiciones. Toca una posición ocupada para quitar el bloque.
- «Pista» consume una medalla y fija la primera posición que no coincide con la secuencia original.
- Se gana una medalla por ronda superada; al fallar, la ronda vuelve al tamaño inicial.
- La secuencia admite colores repetidos y llega a un máximo de 12 bloques.

## Archivos
- `index.html`: estructura y controles.
- `styles.css`: diseño adaptable, modo oscuro/claro y movimiento reducido.
- `app.js`: estado, reglas, sonidos y eventos.
- `manifest.webmanifest`: metadatos para instalación.
- `sw.js`: caché offline.
- `icons/`: iconos de instalación.

## Conceptos clave
- **Estado:** datos que describen la partida ahora. En `app.js`, `phase` indica si estás memorizando, respondiendo o viendo el resultado; `sequence` conserva el orden correcto.
- **Condicional:** permite tomar decisiones. `if (medals < 1) return` impide gastar una medalla que no existe.
- **Evento:** acción del usuario o del sistema. `startBtn.addEventListener('click', startRound)` ejecuta la ronda cuando se toca el botón.
- **Renderizado:** funciones como `renderAnswer()` actualizan lo que aparece en pantalla usando el estado actual.
- **Persistencia:** `localStorage` guarda ajustes, récord y medallas en el dispositivo.
- **PWA:** el manifiesto describe la app instalable y el service worker conserva los archivos para usarlos sin conexión.

## Dos ejemplos
1. `if (answers.some(x => x === null))` comprueba si falta alguna posición. Si falta, el botón de comprobar permanece desactivado.
2. `answers[i] = sequence[i]` en `useHint()` coloca el color correcto en la primera posición que no coincide y la añade a `fixed`, por lo que no se puede retirar.

## Limitaciones conocidas
- El sonido se genera con Web Audio, sin archivos externos. En iPhone puede requerir la primera interacción del usuario para habilitar audio.
- El soporte de vibración depende del navegador y del dispositivo; algunos iPhone no exponen la API de vibración web.
- Los datos se guardan localmente en el navegador y no se sincronizan entre dispositivos.
