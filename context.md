# Contexto: idea original

> **Origen:** conversación con ChatGPT, 21 de septiembre de 2026.
> **Estado:** idea en bruto, sin pulir. Se guarda **literal**, sin editar, para no perder la intención
> original mientras el diseño real evoluciona.
>
> Lo que está aquí **no es la especificación del proyecto**. Es el punto de partida. Cuando algo de
> esta idea se adopte, se contradiga o se descarte, queda registrado en `ROADMAP.md`, no aquí.
> Este archivo no se edita.

---

Sí, y de hecho **esa idea puede funcionar muchísimo mejor que simplemente "estudiar inglés 1 hora al día"**.

Si quieres llegar de tu B1/B2 actual a **B2 sólido → C1**, yo diseñaría el repo como un **sistema de entrenamiento adaptativo**, no como un curso tradicional.

### La idea que haría con Claude

Un repo tipo:

```text
english-max/
├── README.md
├── ROADMAP.md
├── config/
│   ├── profile.md
│   └── goals.md
├── assessments/
│   ├── initial-test.md
│   └── progress.md
├── curriculum/
│   ├── b1-b2/
│   ├── b2/
│   └── b2-c1/
├── sessions/
│   ├── 5min/
│   ├── 15min/
│   ├── 30min/
│   └── 60min/
├── vocabulary/
├── grammar/
├── listening/
├── speaking/
├── writing/
├── mistakes/
└── prompts/
    ├── tutor.md
    ├── conversation.md
    ├── correction.md
    └── evaluation.md
```

La clave sería que **no tengas que sentarte a "estudiar inglés"**.

Abres el repo y dices:

> `Tengo 7 minutos.`

Y el sistema te responde con una actividad de 7 minutos.

Otro día:

> `Tengo 45 minutos y estoy cansado.`

Te da algo diferente.

Y otro:

> `Estoy en el bus, puedo escuchar pero no escribir.`

→ listening + shadowing.

---

### Lo más importante: medir qué necesitas

Claude debería mantener algo como:

```yaml
level:
  overall: B1-B2
  grammar: B2
  reading: B2
  listening: B1+
  speaking: B1+
  writing: B2-

weaknesses:
  - phrasal verbs
  - spontaneous speaking
  - listening speed
  - articles

current_goal: B2
target: C1
```

Así no terminas haciendo **ejercicios de presente simple durante tres meses cuando ya sabes presente simple** 💀.

El sistema debería detectar:

**"Esto ya lo sabes → siguiente."**

**"Esto lo entiendes pero no lo produces → practicar."**

**"Esto lo sigues fallando → volver a trabajarlo."**

---

### Y haría un sistema de sesiones por tiempo

| Tiempo     | Objetivo                |
| ---------- | ----------------------- |
| ⚡ 2–5 min  | Microentrenamiento      |
| 🟢 10 min  | Una habilidad           |
| 🟡 20 min  | Sesión completa         |
| 🟠 30 min  | Entrenamiento intensivo |
| 🔴 60 min  | Deep session            |
| 🧠 90+ min | Inmersión               |

Así puedes avanzar **literalmente cualquier día**, incluso cuando no tengas ganas.

Y algo importante: **no todas las sesiones deberían sentirse como estudiar**.

Por ejemplo:

> 🎧 10 min → escuchar un fragmento y explicar qué entendiste
> 🗣️ 10 min → hablar conmigo exclusivamente en inglés
> 📖 15 min → leer un artículo y extraer expresiones
> ✍️ 10 min → escribir una opinión
> 🎮 20 min → describir lo que ocurre en un videojuego en inglés

Eso hace que acumules horas de contacto con el idioma sin que todo sea *"Complete the following sentence..."* 😭.

### Para maximizar velocidad

Yo establecería una regla:

**Input → Output → Correction → Repetition**

Por ejemplo:

1. Aprendes `to figure out`.
2. La escuchas en contexto.
3. La utilizas hablando.
4. Claude corrige tu uso.
5. Dos días después tienes que usarla espontáneamente.
6. Si fallas → vuelve al ciclo.

Eso es mucho más potente que simplemente meter 30 palabras nuevas en Anki.

---

Y podríamos incluso hacer que el repo tenga un **"English OS"**, donde cada vez que abras Claude le pases algo como:

```text
Read /config/profile.md
Read /progress/current.md
Read /mistakes/recent.md

Act as my English coach.

My available time: 17 minutes.
My energy: low.
My current goal: B2.
Do not teach me things I already demonstrate correctly.

Give me the highest-value activity I can do right now.
Correct my English after I finish, not before.
Update my progress and identify recurring mistakes.
```

Eso convertiría a Claude en algo parecido a un **tutor personal persistente**, y el repo sería su memoria + currículo + sistema de evaluación.

Si lo hacemos bien, incluso podemos diseñarlo específicamente para que **B1/B2 → B2 sólido → C1 sea el objetivo principal**, en vez de intentar cubrir "todo el inglés".

---

## Notas de Alejandro sobre esta idea

<!-- Escribe aquí lo que quieres conservar, cambiar o descartar. Es tu espacio. -->

- [ ] Pendiente de pulir.

---

## Lo que ya sabemos que hay que corregir de esta idea

Anotado el 2026-09-21, contrastando la idea con datos reales que esa conversación no tenía:

1. **El nivel de partida es una suposición.** La idea asume "B1/B2 actual" sin haber medido nada.
   El punto de partida real hay que certificarlo con un test que **incluya habla**, no solo lectura
   y gramática. Un certificado de comprensión puntúa más alto y no predice cómo te irá hablando.

2. **Las debilidades listadas son genéricas.** "Phrasal verbs, articles" es lo que suele fallar a
   ese nivel, no lo que le falla a una persona concreta. Las debilidades reales salen de uso
   observado y viven en `private/`, no de una lista de manual.

3. **Falta el motivo.** La idea no dice para qué. Sin una situación concreta que superar, el
   currículo se dispersa en "mejorar inglés en general". Con ella, las prioridades se ordenan solas.

4. **Falta el mecanismo de honestidad.** Un tutor que adula no sirve. El sistema tiene que negarse
   a inflar el nivel y a responder exámenes cuyo resultado se entrega a un tercero.

5. **Falta la separación público/privado.** La idea pone perfil y progreso dentro del repo. Si el
   repo es open source, eso publica el historial de debilidades de una persona. Ver `SECURITY.md`.

6. **El almacenamiento propuesto no se discute.** La idea no dice dónde vive el contenido, que pesa
   mucho más que el progreso y tiene requisitos opuestos. Ver `ARCHITECTURE.md`.
