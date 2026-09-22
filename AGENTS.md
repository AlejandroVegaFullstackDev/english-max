# AGENTS.md

Punto de entrada para cualquier agente de código —Claude, Codex, Cursor, Copilot, Gemini u otro—
que abra este repositorio.

---

## Qué es esto

La aplicación web de un tutor de inglés. **El tutor en sí no está aquí**: vive en
[ai-skill-coach](https://github.com/AlejandroVegaFullstackDev/ai-skill-coach), funciona con
cualquier agente y no necesita esta web.

Esto es la interfaz para practicar desde el móvil. Un back, un front, ambos en Vercel.

## Antes de tocar nada

| Lee esto | Para |
|---|---|
| [`README.md`](README.md) | Qué es y qué no, y cómo se relaciona con el otro repo |
| [`docs/ARCHITECTURE.md`](docs/ARCHITECTURE.md) | Dónde vive cada dato y por qué |
| [`ROADMAP.md`](ROADMAP.md) | Qué ya está decidido y qué sigue abierto |
| [`CLAUDE.md`](CLAUDE.md) | Reglas de ingeniería de backend, agnósticas de lenguaje |
| [`CLAUDE_FRONTEND.md`](CLAUDE_FRONTEND.md) | Reglas de interfaz |
| [`SECURITY.md`](SECURITY.md) | Qué nunca entra al control de versiones |

`CLAUDE.md` y `CLAUDE_FRONTEND.md` se llaman así por la convención de Claude Code, pero su
contenido son reglas de ingeniería normales. Cualquier agente debe seguirlas.

## El estado del proyecto

**No hay código.** Las carpetas de `apps/` están vacías a propósito: faltan dos decisiones —base
de datos y sincronización con el repo del tutor— que determinan la forma del código.

Si te piden implementar algo y esas decisiones siguen abiertas, **dilo antes de escribir**. No las
resuelvas por tu cuenta eligiendo una librería.

## Reglas que no se negocian

1. **La app no llama a ningún modelo de IA.** El contenido está pre-generado por el agente entre
   sesiones. Añadir una llamada en tiempo de ejecución rompe el coste cero y la independencia de
   cualquier API.
2. **Progreso y contenido no se mezclan.** Tienen tamaños, frecuencias de escritura y sensibilidad
   opuestas. Ver `docs/ARCHITECTURE.md`.
3. **El acceso a datos va detrás de una interfaz.** Con proveedores de tier gratuito, poder
   cambiar no es un lujo.
4. **Todo detrás de autenticación.** Un solo usuario, pero son datos personales de aprendizaje.
5. **Las grabaciones de voz son datos biométricos.** El análisis corre en la máquina del usuario.
   No propongas subir audio a ningún servicio sin decirlo explícitamente en el README.
6. **El micrófono se habilita por ruta, nunca globalmente**, y sin tocar el resto de cabeceras de
   seguridad.
7. **Presupuesto cero.** Ninguna dependencia de pago, ni "gratis hasta cierto volumen" sin decirlo.
8. **Cada dependencia nueva se justifica por escrito.** Esto lo mantiene una persona en ratos
   libres; cada dependencia es superficie de ataque y deuda.
