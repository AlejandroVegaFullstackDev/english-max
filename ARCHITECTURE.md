# Arquitectura

Decisiones y por qué. Restricciones que mandan sobre todo lo demás:

- **Un solo usuario por instalación.** No hay multi-tenencia, ni la habrá.
- **Presupuesto objetivo: $0.**
- **Móvil primero**, pero utilizable desde cualquier navegador sin instalar nada.
- **Objetivo del caso de referencia: B1 → B2**, medido por sostener una conversación técnica de 30
  minutos.

---

## Los dos tipos de datos

Se confunden fácilmente y tienen requisitos opuestos.

| | **Contenido** (el curso) | **Progreso** (tú) |
|---|---|---|
| Qué es | Lecciones, ejercicios, textos, audio | Nivel, errores, qué dominas, qué repetir |
| Tamaño | Megas, y creciendo | Kilobytes al año |
| Quién escribe | El agente, al generar currículo | La app, en cada sesión |
| Frecuencia de escritura | Rara | Constante |
| ¿Versionado? | Sí, interesa el historial | Sí, interesa la evolución |
| ¿Sensible? | No | **Sí** |

### Contenido → Astro Content Collections

Si el frontend es Astro, este patrón ya viene resuelto y es exactamente lo que hace falta:

- Ficheros Markdown o JSON en el repo, con *frontmatter* tipado y **validado por esquema en build**.
- Se sirven desde el CDN de Vercel. Coste cero, latencia mínima, funciona sin backend.
- Versionados en Git: se ve cómo evolucionó el currículo y se puede revertir.
- **Y lo más importante: pre-generados.** El ejercicio ya existe cuando lo abres; no hay llamada a
  ninguna IA para servirlo. Ese es el principio que mantiene el coste en cero.

Un ejercicio es un fichero con metadatos: nivel, habilidad, duración, requisitos previos, y si
necesita micrófono o solo audio. Eso es lo que permite responder a *"tengo 7 minutos y no puedo
hablar"* — es un filtro sobre metadatos, no magia.

### Progreso → JSON

**No Parquet.** Parquet es columnar, pensado para leer una columna de millones de filas. Lo tuyo es
un documento pequeño que se lee entero y se reescribe entero muchas veces al día: el peor caso para
Parquet y el mejor para JSON.

**Tampoco comprimido.** Comprimir kilobytes añade un paso de descompresión en cada lectura para
ahorrar nada. Cuando el historial pese de verdad —años— se archiva lo viejo y se comprime *eso*,
que sí se lee poco.

Reglas de escritura en `CLAUDE.md`: escritura atómica a temporal y renombrado, validación de esquema
**al leer**, versión del esquema dentro del fichero, y todo detrás de una interfaz para poder
cambiar a una base de datos sin tocar el resto.

---

## Dónde vive cada cosa

```
Navegador (móvil o escritorio)
        │
        ▼
Astro en Vercel  ─────────────►  Content Collections   (contenido, CDN, gratis)
  · páginas .astro (SSR)
  · islas React solo donde hay interacción
  · endpoints en src/pages/api/
        │
        ▼
Almacén de progreso  (JSON detrás de una interfaz)
        │
        ▼
Agente, cada 2-3 días  ───────►  lee progreso, genera plan y ejercicios nuevos
```

**El ciclo:** la web tiene ejercicios listos y registra cómo te va. Cada dos o tres días corres un
comando que le pasa al agente el progreso —cuántas fallaste, qué te cuesta, qué evitas— y el agente
genera el siguiente tramo de currículo y lo commitea como contenido nuevo. La IA trabaja **entre
sesiones**, no durante. Por eso cuesta cero.

---

## Voz

Aquí es donde está lo interesante, y no es TTS clásico.

### El problema real

Un ejemplo concreto, típico de hispanohablantes: la **vocal epentética antes de grupos con s-** —
*"eschema"*, *"estack"*, *"esscheduling"*. Es muy audible para un oído nativo, se corrige de forma
consciente, y quien la comete no la oye en su propia voz.

Un transcriptor normal **no sirve** para detectarla: Whisper escucha *"eschema"* y transcribe
`schema`, porque su trabajo es entenderte, no juzgarte. Te da la razón y no aprendes nada. Lo mismo
pasa con cualquier error sistemático que no impida la comprensión — que son justo los que fosilizan.

### La pieza clave: OpenPronounce

[OpenPronounce](https://github.com/Halleck45/OpenPronounce) — evaluación de pronunciación **a nivel
de fonema**, MIT, autoalojada, alternativa libre a Azure Pronunciation Assessment.

Devuelve:

- Puntuación 0-100
- **Por palabra: el fonema esperado contra el que realmente pronunciaste, en notación IPA**
- Tasa de error fonémico y de palabra
- Curvas de tono y energía (prosodia)

Usa `wav2vec2` afinado con etiquetas de fonemas más DTW para alinear. Corre **en CPU**, descarga
~1,2 GB de pesos la primera vez, y se puede usar como librería de Python, por línea de comandos o
en Docker.

Que devuelva el fonema que *de verdad* dijiste es justo lo que hace falta para tu problema: verías
literalmente una `/ɪ/` insertada donde no va.

**Limitación honesta:** ~10% de error fonémico incluso con voz nativa limpia, y empeora con acento
marcado — que es precisamente el caso de quien va a usarlo. Sirve para **detectar patrones
repetidos y ver tendencia**,
no para tomarse un número absoluto en serio. Si un día dice 72 y otro 78, eso no significa nada; que
marque la misma inserción veinte veces sí.

### El resto del stack de voz

| Pieza | Qué hace | Coste | Licencia |
|---|---|---|---|
| [**Transformers.js**](https://github.com/huggingface/transformers.js) v4.3 | Whisper y TTS **dentro del navegador** vía WASM/WebGPU, sin servidor | $0 | Apache-2.0 |
| **Web Speech API** | Dictado y voz nativos del navegador. iOS Safari 14.5+ lo soporta (parcial) | $0 | — |
| [**Piper**](https://github.com/OHF-Voice/piper1-gpl) | TTS neuronal local y offline, para pre-generar audio | $0 | **GPL-3.0** |
| **Kokoro** | TTS ligero, alternativa a Piper con licencia más permisiva | $0 | Apache-2.0 |
| **OpenPronounce** | Evaluación fonémica | $0 | MIT |

**Sobre la licencia de Piper:** es GPL-3.0. Para uso personal da igual. Si esto llega a ser el
negocio que mencionaste, GPL obliga a liberar el código derivado — por eso vale la pena mirar
Kokoro, que es Apache-2.0, antes de acoplarse a Piper.

### Cómo se reparte entre dispositivos

- **En el móvil, en la calle:** escuchar textos con la voz del navegador o audio pre-generado, y
  grabarte con `MediaRecorder`. Sin análisis en el momento: solo captura.
- **En el PC:** el audio grabado pasa por OpenPronounce en local cuando corres el comando. Análisis
  fonémico completo, gratis, sin que tu voz salga a ningún servidor.
- **Transformers.js** permite transcribir en el navegador del PC sin servidor, útil para respuesta
  inmediata cuando estés en casa.

Esa separación es lo que mantiene el coste en cero **y** tus grabaciones en tus máquinas.

---

## El micrófono está bloqueado hoy

Un `vercel.json` con cabeceras de seguridad razonables suele incluir:

```
Permissions-Policy: camera=(), microphone=(), geolocation=(), interest-cohort=()
```

Eso desactiva el micrófono en todo el sitio. Hay que permitirlo **solo en la ruta de práctica**,
nunca globalmente, y dejar el resto de cabeceras como están.

---

## Lo que queda sin decidir

1. **¿Módulo de un sitio existente o proyecto aparte?** Como módulo reutilizas dominio, deploy y
   configuración. Aparte, no arriesgas el sitio público. En cualquier caso, **detrás de
   autenticación**: son datos personales de aprendizaje.
2. **Escribir progreso desde el móvil.** Si el progreso vive en Git, escribir desde el iPhone exige
   un commit vía API de GitHub. Funciona, pero es raro. La alternativa es una base de datos en tier
   gratuito, con la dependencia que eso implica.
3. **Revisar intentos previos.** Si ya construiste algo parecido antes, mirar qué funcionó, qué te
   aburrió y por qué lo dejaste vale más que cualquier decisión de arquitectura tomada en frío.
