# apps/api — el back

Endpoints y acceso a datos. La única superficie con credenciales.

**Vacío a propósito.** Falta decidir la base de datos y la sincronización con el repo del tutor.
Ver [ROADMAP.md](../../ROADMAP.md).

## Qué tiene que hacer

1. Autenticar. Un usuario, pero autenticar.
2. Leer y escribir el progreso.
3. Exponer el progreso al agente, en solo lectura, para que pueda planificar.

## Lo que no hace

- **No llama a ningún modelo.** El contenido está pre-generado.
- **No sirve el contenido.** Eso es CDN.
- No tiene lógica de tutoría. Qué enseñar y cómo corregir vive en el repo del tutor.

## Reglas duras

- Acceso a datos **detrás de una interfaz**. Los tiers gratuitos cambian de política.
- Escrituras atómicas. Si falla a medias, no puede dejar el progreso corrupto.
- Validar el esquema **al leer**, no solo al escribir.
- Versión de esquema dentro del propio dato.
- Nada de trazas ni detalles internos en las respuestas.

Reglas generales: [CLAUDE.md](../../CLAUDE.md).
