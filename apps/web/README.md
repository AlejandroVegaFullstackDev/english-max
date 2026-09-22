# apps/web — el front

Lo que se ve. Móvil primero.

**Vacío a propósito.** Ver [ROADMAP.md](../../ROADMAP.md): faltan dos decisiones que determinan la
forma del código.

## Qué tiene que hacer

1. Preguntar cuánto tiempo hay y en qué condiciones.
2. Servir **un** ejercicio que encaje, filtrando por los metadatos del contenido.
3. Registrar cómo fue y mandarlo al back.
4. Reproducir audio pre-generado, y grabar cuando la ruta lo permita.

Nada más. Todo lo que parezca inteligencia vive en el otro repositorio.

## Restricciones

- **iPhone en Safari es el caso principal.** Lo que no funcione ahí, no funciona.
- **Debe servir sin poder hablar.** La mitad de los huecos reales son en público.
- Estático y desde CDN siempre que se pueda: el contenido son ficheros.
- Sin estado global hasta que haya un problema concreto que lo exija.

Reglas: [CLAUDE_FRONTEND.md](../../CLAUDE_FRONTEND.md).
