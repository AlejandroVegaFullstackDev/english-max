# AGENTS.md

Punto de entrada para cualquier agente de código —Claude, Codex, Cursor, Copilot, Gemini u otro—
que abra este repositorio.

Este proyecto no depende de ninguna IA concreta. Lo único que necesita el agente es poder leer
ficheros del repo y escribir en él.

---

## Qué es esto

Un tutor adaptativo cuyo estado vive en ficheros. El agente lee tu perfil y tus errores, decide qué
practicas hoy según el tiempo y la energía que tengas, y cada pocos días genera el siguiente tramo
de currículo. La materia de referencia es inglés, pero la estructura sirve para cualquier habilidad
que se aprenda **produciendo y recibiendo corrección**.

## Antes de tocar nada

| Lee esto | Para |
|---|---|
| [`README.md`](README.md) | Qué hace el proyecto y por qué cuesta cero |
| [`ARCHITECTURE.md`](ARCHITECTURE.md) | Dónde vive cada dato y por qué |
| [`ROADMAP.md`](ROADMAP.md) | Qué ya está decidido — no lo reabras sin motivo |
| [`CLAUDE.md`](CLAUDE.md) | Reglas de ingeniería de backend, agnósticas de lenguaje |
| [`CLAUDE_FRONTEND.md`](CLAUDE_FRONTEND.md) | Reglas de interfaz |
| [`SECURITY.md`](SECURITY.md) | Qué nunca entra al control de versiones |

Los dos ficheros `CLAUDE*.md` se llaman así por la convención de Claude Code, pero **su contenido no
es específico de Claude**: son reglas de ingeniería normales. Cualquier agente debe seguirlas.

## Reglas que no se negocian

1. **`private/` no se commitea nunca.** Contiene perfil, progreso, errores y grabaciones de una
   persona real. Un repositorio público es para siempre.
2. **Las grabaciones de voz son datos biométricos.** El análisis corre en la máquina del usuario. No
   propongas subir audio a ningún servicio sin decirlo explícitamente en el README.
3. **No infles el nivel del usuario.** Un tutor que adula no sirve. Si no hay evidencia de que algo
   se domina, no se da por dominado.
4. **No respondas exámenes cuyo resultado se entrega a un tercero.** Un certificado obtenido así
   describe a la IA, no a la persona.
5. **La IA trabaja entre sesiones, no durante.** Los ejercicios se pre-generan y se versionan. Eso
   es lo que mantiene el coste en cero; no lo rompas añadiendo llamadas en tiempo de ejecución.

## Cómo se arranca una sesión

El usuario abre su agente y dice cuánto tiempo y qué condiciones tiene:

```
Lee private/profile.md, private/progress.md y private/mistakes.md.

Tengo 17 minutos. Energía baja. No puedo hablar en voz alta.
No me enseñes lo que ya demuestro correctamente.

Dame la actividad de mayor valor que pueda hacer ahora.
Corrígeme al final, no durante.
Actualiza mi progreso y anota los errores que se repitan.
```

Si `private/profile.md` no existe todavía, cópialo de
[`config/profile.example.md`](config/profile.example.md) y pide al usuario que lo rellene con su
nivel **real y con fuente**. No lo rellenes tú a ojo.
