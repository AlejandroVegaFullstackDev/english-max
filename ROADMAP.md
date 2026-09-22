# Roadmap

Registro de decisiones. `context.md` no se toca; los cambios respecto a la idea original se anotan
aquí, con fecha y motivo.

---

## Decidido

**2026-09-21 · El objetivo es B2, no C1.**
La idea original apuntaba a C1. El nivel real de partida es B1 certificado. C1 sigue siendo el
destino, pero no orienta las decisiones del día a día: lo que las orienta es sostener una llamada
técnica de 30 minutos en inglés.

**2026-09-21 · Nada de currículo todavía.**
Los directorios `curriculum/` y `sessions/` están creados pero vacíos a propósito. Escribir
ejercicios antes de saber dónde se rompe el inglés hablado es exactamente el error que la idea
original decía querer evitar.

**2026-09-21 · El primer entregable es una línea base, no una lección.**
Una sesión hablada de la que salgan errores reales. Con eso se llena `mistakes/` y recién ahí se
diseña el currículo.

**2026-09-21 · Reglas de ingeniería adoptadas.**
`CLAUDE.md` (backend, agnóstico de lenguaje) y `CLAUDE_FRONTEND.md`. Venían escritas
para Flask y para microfrontends federados; se generalizaron conservando la intención. Lo pospuesto
del documento de frontend está listado con su criterio de activación en su sección 10.

**2026-09-21 · Contenido y progreso se separan.**
Contenido del curso en Astro Content Collections si el frontend es Astro:
Markdown tipado, validado en build, servido desde CDN, coste cero. Progreso en JSON detrás de una
interfaz. Ver `ARCHITECTURE.md`.

**2026-09-21 · JSON, no Parquet, no comprimido.**
Parquet es columnar y sirve para leer una columna de millones de filas; aquí se lee y reescribe un
documento pequeño muchas veces al día, que es su peor caso. Comprimir kilobytes no ahorra nada y
añade un paso a cada lectura.

**2026-09-21 · La IA trabaja entre sesiones, no durante.**
La web sirve ejercicios pre-generados. Cada dos o tres días se corre un comando que entrega el
progreso, y el agente genera el siguiente tramo de currículo y lo commitea. Eso es lo que mantiene
el coste en cero.

**2026-09-21 · El repositorio es open source; los datos personales no.**
El motor —reglas, arquitectura, currículo, herramientas— es público bajo MIT para que cualquiera
monte su propio tutor. El perfil, el progreso, los errores y las grabaciones viven en `private/`,
fuera del control de versiones. Ver `SECURITY.md`.

**2026-09-21 · Evaluación de pronunciación a nivel de fonema.**
[OpenPronounce](https://github.com/Halleck45/OpenPronounce): MIT, wav2vec2 + DTW, local, CPU. Un
transcriptor normal no sirve — Whisper escucha *"eschema"* y transcribe `schema`, porque su trabajo
es entender, no juzgar. OpenPronounce devuelve el fonema realmente pronunciado en IPA, que es lo
único que expone la vocal epentética ante grupos con s-.
Limitación aceptada: ~10% de error fonémico incluso con voz nativa limpia, y peor con acento
marcado. Sirve para tendencia y patrones repetidos, no para puntuaciones absolutas.

---

## Pendiente de decidir

### 1. ¿Módulo del portafolio o proyecto aparte?

Como módulo de un sitio existente: reutiliza dominio, deploy y configuración. Aparte: no arriesga
el sitio público.

En cualquier caso, **detrás de autenticación**. Son datos personales de aprendizaje.

### 2. ¿Publicar el tutor como *agent skill* instalable?

Existe una especificación formal y una plantilla en
[anthropics/skills](https://github.com/anthropics/skills) (Apache-2.0). Empaquetar el tutor como
skill lo haría instalable en un comando, y no encontré ninguna skill pública que mantenga estado
sobre el usuario a lo largo del tiempo.

Decidir si este repo **es** la skill o si la skill es un envoltorio que apunta a él.

### 3. Escribir progreso desde el móvil

Si el progreso vive en Git, escribir desde el iPhone exige un commit vía API de GitHub. Funciona,
pero es raro. La alternativa es una base de datos en tier gratuito, con la dependencia que implica.

### 4. Repetición espaciada

La regla `Input → Output → Correction → Repetition` exige saber qué toca repasar y cuándo. Hace
falta decidir el algoritmo y dónde vive el calendario.

### 5. Piper o Kokoro para TTS

Piper es GPL-3.0: irrelevante para uso personal, relevante si esto llega a ser negocio. Kokoro es
Apache-2.0. Decidir antes de acoplarse a uno.

### 6. Revisar intentos previos

Si ya existió un proyecto parecido, mirar qué funcionó y por qué se abandonó vale más que cualquier
decisión de arquitectura tomada en frío.

---

## Siguiente paso

Una sesión hablada de 20-30 minutos para tener línea base. Sin eso, todo lo demás es diseñar a
ciegas.
