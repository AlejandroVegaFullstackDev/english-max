# packages/contracts

Los tipos que comparten el front y el back. **La única dependencia entre ambos.**

## Por qué existe

Si el back cambia la forma de un dato y el front no se entera, el fallo aparece en producción, en
el móvil, a mitad de una sesión. Con los tipos aquí, aparece al compilar.

## Qué va

- La forma del progreso
- La forma de un ejercicio y sus metadatos
- Los tipos de petición y respuesta de cada endpoint
- Los estados de un ítem: `unseen`, `taught`, `practiced`, `mastered`

## Qué no va

- Lógica. Es un contrato, no una librería compartida.
- Nada que solo use uno de los dos lados.
- Utilidades "por si acaso". Se añade cuando hace falta, no antes.

## Alineación con el tutor

Estos tipos tienen que coincidir con el esquema de `progress/tracker.json` del repositorio del
tutor. Si divergen, la sincronización se rompe en silencio.

Cuando cambie el esquema allí, cambia aquí, y la versión de esquema sube en los dos sitios.
