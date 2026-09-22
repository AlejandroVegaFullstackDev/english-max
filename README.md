# english-max

La aplicación web. Un back y un front, desplegados en Vercel.

> **Estado: esqueleto.** Están las reglas de ingeniería, la arquitectura y la estructura de
> carpetas. No hay código todavía, y es deliberado — ver [ROADMAP.md](ROADMAP.md).

---

## Qué es, y qué no

Esto es **la interfaz**. El tutor —la lógica de qué enseñar, cómo corregir y qué recordar— vive en
otro repositorio: [ai-skill-coach](https://github.com/AlejandroVegaFullstackDev/ai-skill-coach),
que funciona con cualquier agente de código y no necesita esta web para nada.

| | ai-skill-coach | english-max (esto) |
|---|---|---|
| Qué es | El tutor. Comandos y reglas para un agente | La app para practicar desde el móvil |
| Dónde corre | En tu terminal, con tu agente | En Vercel, desde cualquier navegador |
| Estado | Ficheros en el repo | Base de datos |
| Para qué | Generar currículo, evaluar, planificar | Hacer los ejercicios en los huecos del día |

**La separación importa.** El agente genera el contenido entre sesiones; la web solo lo sirve y
registra cómo fue. Por eso la web puede ser barata y rápida: no llama a ningún modelo.

---

## Por qué existe

El tutor en el repositorio funciona en el PC. Pero los huecos reales —el bus, la cola del banco,
diez minutos entre reuniones— ocurren con el teléfono en la mano.

Requisitos que salen de ahí:

- **Móvil primero.** iPhone, sin instalar nada.
- **Accesible desde cualquier dispositivo**: PC del trabajo, PC de casa, Android.
- **Detrás de autenticación.** Son datos personales de aprendizaje.
- **Coste objetivo: cero.** Tier gratuito o no se hace.
- **Funciona sin llamar a ninguna IA.** El contenido ya existe cuando lo abres.

---

## Arquitectura

```
apps/web/         front — lo que se ve
apps/api/         back  — endpoints y acceso a datos
packages/contracts/  tipos compartidos entre los dos
docs/             decisiones y su porqué
```

El detalle, y el razonamiento detrás de cada decisión, en
[docs/ARCHITECTURE.md](docs/ARCHITECTURE.md).

### Los dos tipos de datos

No se pueden mezclar: tienen requisitos opuestos.

| | **Contenido** (los ejercicios) | **Progreso** (tú) |
|---|---|---|
| Tamaño | Megas, y creciendo | Kilobytes al año |
| Quién escribe | El agente, entre sesiones | La app, en cada sesión |
| Frecuencia de escritura | Rara | Constante |
| ¿Sensible? | No | **Sí** |
| Dónde | Ficheros versionados, servidos desde CDN | Base de datos |

---

## Reglas de desarrollo

Si abres esto con un agente, empieza por [AGENTS.md](AGENTS.md).

- [CLAUDE.md](CLAUDE.md) — reglas de backend, agnósticas de lenguaje
- [CLAUDE_FRONTEND.md](CLAUDE_FRONTEND.md) — reglas de interfaz
- [docs/ARCHITECTURE.md](docs/ARCHITECTURE.md) — qué se decidió y por qué
- [ROADMAP.md](ROADMAP.md) — lo decidido y lo que sigue abierto
- [SECURITY.md](SECURITY.md) — qué nunca entra al control de versiones

---

## Lo que aún no está decidido

Está en [ROADMAP.md](ROADMAP.md), pero lo importante:

1. **Qué base de datos.** Tiene que ser tier gratuito y aguantar escrituras pequeñas y frecuentes.
2. **Cómo se sincroniza con el repo del tutor.** El agente genera contenido en ficheros; la web
   tiene que servirlo. Y el progreso que registra la web tiene que volver al agente.
3. **Autenticación.** Un solo usuario, pero detrás de login igualmente.
4. **El micrófono.** Las cabeceras de seguridad razonables lo desactivan en todo el sitio. Hay que
   permitirlo **solo en la ruta de práctica**.

---

## Licencia

MIT.
