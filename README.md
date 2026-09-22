# english-max

Un tutor adaptativo que vive en un repositorio y funciona con un agente de código.

No es un curso ni una app de flashcards. Es un **esqueleto**: el agente lee tu perfil, mira en qué
fallas, y decide qué practicas hoy con el tiempo y la energía que tengas. El contenido se
pre-genera y se versiona; la IA trabaja **entre sesiones**, no durante cada ejercicio.

La implementación de referencia es inglés B1 → B2. La estructura sirve para cualquier materia que
se aprenda produciendo y recibiendo corrección.

> **Estado: día 1.** Están las reglas, la arquitectura y la estructura. No hay currículo todavía, y
> es deliberado — ver [ROADMAP.md](ROADMAP.md).

---

## Qué problema resuelve

Las apps de idiomas te hacen repasar presente simple durante meses porque no tienen forma de saber
que ya lo dominas. Y un curso lineal asume que tienes una hora libre y ganas.

Aquí la unidad no es la lección, es **el hueco que tengas**:

```
Tengo 7 minutos.
Estoy en el bus, puedo escuchar pero no hablar.
Tengo 40 minutos pero estoy fundido.
```

El agente responde con **una** actividad, elegida según tu nivel real, tus errores recientes y esas
condiciones. Cada ejercicio lleva metadatos —habilidad, duración, si necesita micrófono— así que
elegir es filtrar, no improvisar.

### El ciclo

```
Input  →  Output  →  Correction  →  Repetition
```

Aprender algo no cuenta hasta que lo produces espontáneamente días después. Si falla, vuelve al
ciclo. Eso es lo que separa esto de meter tarjetas en Anki.

---

## Por qué cuesta cero

Llamar a un modelo en cada ejercicio es caro y lento. Aquí la IA trabaja **entre sesiones**:

1. Practicas con ejercicios que ya existen en el repo. Sin red, sin API, instantáneo.
2. Cada pocos días le pasas tu progreso al agente.
3. El agente analiza dónde fallas y **genera el siguiente tramo de currículo**, que se commitea como
   contenido nuevo.

El único coste recurrente es tu suscripción al agente, que ya tienes si programas con uno.

---

## Privacidad: qué es público y qué no

Este repositorio es open source **para que copies el motor, no para que veas el progreso de nadie.**

| Público | Privado (`private/`, ignorado por git) |
|---|---|
| Reglas de ingeniería | Tu nivel y tus debilidades |
| Arquitectura | Tu progreso y errores |
| Plantilla de perfil | Tus grabaciones de voz |
| Currículo y ejercicios | Tu plan personal |

Un perfil de aprendizaje dice en qué eres flojo. Un repositorio público es para siempre. Ver
[`private/README.md`](private/README.md) y [SECURITY.md](SECURITY.md).

**Las grabaciones de voz son datos biométricos.** El análisis de pronunciación corre **en tu
máquina**; tu audio no sale de ahí.

---

## Empezar

```bash
git clone <este-repo> && cd english-max
cp config/profile.example.md private/profile.md
```

Rellena `private/profile.md` con tu nivel **real y con fuente**, no con el que te gustaría tener.
Después abre tu agente y dile cuánto tiempo tienes.

Si no sabes tu nivel, haz un test que **mida el habla** antes de empezar. Un test de lectura y
gramática puntúa más alto y no predice cómo te irá en una conversación.

---

## Voz

Todo local y gratuito:

| Pieza | Para qué | Licencia |
|---|---|---|
| [OpenPronounce](https://github.com/Halleck45/OpenPronounce) | Evaluación **fonema a fonema**: te dice qué sonido dijiste de verdad, en IPA | MIT |
| [Transformers.js](https://github.com/huggingface/transformers.js) | Whisper y TTS dentro del navegador, sin servidor | Apache-2.0 |
| [Piper](https://github.com/OHF-Voice/piper1-gpl) · Kokoro | TTS neuronal offline para pre-generar audio | GPL-3.0 · Apache-2.0 |
| Web Speech API | Dictado y voz nativos. iOS Safari 14.5+ (parcial) | — |

**Por qué OpenPronounce y no solo Whisper:** un transcriptor te *entiende*. Dices *"eschool"* y
escribe `school`, porque su trabajo es adivinar tu intención. Te da la razón y no aprendes nada.
OpenPronounce devuelve el fonema que realmente pronunciaste, que es lo único que expone ese tipo de
error.

Limitación honesta: ~10% de error fonémico incluso con voz nativa limpia, y peor con acento marcado.
Sirve para **tendencia y patrones repetidos**, no para tomarse un número absoluto en serio.

---

## Estructura

```
config/        plantilla de perfil, objetivos
private/       tus datos — ignorado por git
curriculum/    contenido generado, por nivel
sessions/      actividades por duración
mistakes/      errores recurrentes (tuyos: ignorado)
prompts/       instrucciones para el agente
vocabulary/ grammar/ listening/ speaking/ writing/
```

Reglas de desarrollo: [CLAUDE.md](CLAUDE.md) (backend, agnóstico de lenguaje) y
[CLAUDE_FRONTEND.md](CLAUDE_FRONTEND.md). Decisiones y su porqué: [ARCHITECTURE.md](ARCHITECTURE.md).

---

## Cómo se relaciona con las colecciones de skills que ya existen

Hay catálogos grandes de *agent skills* — [anthropics/skills](https://github.com/anthropics/skills)
(oficial, Apache-2.0, con especificación y plantilla),
[VoltAgent/awesome-agent-skills](https://github.com/VoltAgent/awesome-agent-skills) (1000+),
[ComposioHQ](https://github.com/ComposioHQ/awesome-claude-skills),
[hesreallyhim/awesome-claude-code](https://github.com/hesreallyhim/awesome-claude-code).

Casi todas enseñan al agente **a usar una herramienta**. Existe también el patrón de convertir un
libro técnico en una skill para consultarlo.

Lo que no encontré en ninguna: un tutor que **mantiene estado sobre ti a lo largo del tiempo** —
qué dominas, qué fallas, cuándo toca repasar— y adapta el plan a partir de eso. Ese es el hueco que
este repo intenta llenar.

---

## Licencia

MIT. Haz lo que quieras con el motor.

Si usas Piper para TTS, ojo: Piper es GPL-3.0 y eso afecta al código derivado. Kokoro (Apache-2.0)
evita ese problema.
