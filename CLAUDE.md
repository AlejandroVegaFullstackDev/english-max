# CLAUDE.md — Reglas de ingeniería

Define cómo debe comportarse cualquier agente de código al trabajar en este repositorio.

> **Sobre este documento.** Alejandro lo trajo escrito para Flask + SQLAlchemy y pidió generalizarlo,
> con razón: *"si lo piensas es lo mismo, debe ser para cualquier acto de programación e
> infraestructura"*. Las reglas de abajo son las suyas, reformuladas para que apliquen al lenguaje
> y al almacenamiento que use cada parte del proyecto. Donde una regla original nombraba una
> tecnología, se conserva la **intención** y se indica cómo se cumple en el stack actual.

---

## Objetivo

Trabajar como ingeniero con estas prioridades, **en este orden**:

1. Seguridad por diseño
2. Mantenibilidad
3. Simplicidad
4. Escalabilidad
5. Consistencia con la arquitectura existente

Principios que aplican siempre: **KISS, DRY, SOLID, YAGNI, 12-Factor, Clean Architecture**, y DDD
ligero solo donde aporte valor real.

**Preferir el cambio seguro más pequeño que resuelva la tarea correctamente.**

### YAGNI tiene prioridad en este proyecto

Este repo tiene **un usuario** y presupuesto **cero**. Cuando una regla de arquitectura empresarial
choque con eso, gana la simplicidad, y se deja anotado en `ROADMAP.md` qué se pospuso y por qué.
Construir para una escala que no existe es el error más caro que se puede cometer aquí.

---

## Política de edición

Antes de escribir código, **inspecciona el patrón existente** en el módulo afectado y síguelo, salvo
que viole seguridad, mantenibilidad o corrección.

Al proponer estructura nueva, **adaptarse al código actual de forma incremental** en vez de forzar
una migración completa de una sola vez.

---

## Reglas de trabajo obligatorias

- **Leer los archivos relevantes** antes de proponer o editar.
- Explicar brevemente la intención del cambio antes de aplicarlo.
- Cambios **pequeños, localizados y reversibles**.
- No introducir dependencias nuevas salvo necesidad estricta. En este proyecto, **cada dependencia
  nueva se justifica por escrito**: una dependencia es una superficie de ataque y una deuda de
  mantenimiento para alguien que programa esto en ratos libres.
- No reescribir módulos no relacionados.
- **No hardcodear** valores de negocio, credenciales, secretos, endpoints, tokens ni configuración.
- Las constantes compartidas o repetidas viven en el módulo de utilidades o de configuración según
  su propósito. Una constante puede quedarse privada en su archivo solo si es local, estable y no se
  reutiliza.
- **Nunca construir consultas concatenando cadenas.** Todo acceso a datos usa la capa de acceso del
  proyecto con parámetros ligados o su equivalente seguro, sea un ORM, un query builder o lectura de
  ficheros estructurados.
- Sin comentarios innecesarios, loggers ruidosos ni código decorativo.
- Sin prints de depuración, código temporal ni ramas muertas.
- Preservar compatibilidad hacia atrás salvo que la tarea pida explícitamente romperla.

---

## Arquitectura: separación por responsabilidad

Las capas son conceptuales y aplican en cualquier lenguaje. Los nombres de carpeta se adaptan a la
convención del stack; lo que no se negocia es **qué puede hacer cada capa**.

| Capa | Responsabilidad | Prohibido |
|---|---|---|
| **Rutas / endpoints** | Definir la ruta y el método, extraer entrada, delegar, devolver respuestas consistentes | Reglas de negocio, persistencia directa |
| **Controladores** | Orquestar casos de uso, coordinar DTOs, servicios y mapeo de respuesta | Detalles de infraestructura |
| **Servicios** | Integraciones externas, operaciones de dominio reutilizables, coordinación de acceso a datos | Conocer el transporte HTTP |
| **Implementaciones** | Concreción de una abstracción, **solo cuando hay más de una implementación real** o hay que aislar un integrador externo | Existir "por si acaso" |
| **Modelos / entidades** | Estructura de datos y relaciones | Lógica de transporte |
| **DTOs** | Validar y transformar entrada y salida | Exponer campos internos sin querer |
| **Utilidades** | Helpers puros y constantes compartidas | Orquestación de negocio |
| **Configuración** | Punto único de entrada de config y entorno | Valores sensibles en el código |
| **Pruebas** | Unitarias, integración y contrato | — |
| **Infraestructura** | Despliegue, plataforma, runtime | — |

Si el repositorio actual difiere de esta estructura, **alinear incrementalmente**, no reescribir.

**No crear parejas interfaz/implementación sin beneficio claro.** Una abstracción con una sola
implementación es código muerto con pasos extra.

---

## Inyección de dependencias

- Los controladores reciben servicios por constructor.
- Los servicios reciben repositorios, adaptadores o clientes externos por constructor.
- Instanciar dependencias **solo en los límites de composición**.
- Sin globales ocultos, sin estado implícito, sin *service locator*.

---

## Configuración

- Centralizada en un único punto de entrada.
- Valores sensibles desde variables de entorno o gestor de secretos, **nunca en el código**.
- En local se permite `.env`; en producción, el almacén de secretos de la plataforma.
- Defaults seguros y mínimos.

---

## Seguridad

Mínimos en todo cambio:

- **Validar toda entrada externa** y rechazar temprano lo malformado.
- Mínimo privilegio en credenciales e integraciones.
- CORS restringido a orígenes explícitos.
- **No exponer trazas ni detalles internos** en las respuestas de la API.
- JWT o API keys solo a través de la configuración y validación centralizadas.
- Proteger campos sensibles de serialización accidental.
- Nada de interpolación de cadenas para consultas, comandos u operaciones sensibles.
- Evitar deserialización insegura, captura silenciosa de excepciones amplias y manejo débil de
  tokens.
- Preferir operaciones idempotentes y explícitas.

### Específico de este proyecto

- Los datos de progreso son **personales**: nivel, errores, grabaciones de voz. El repositorio es
  público; esos datos viven en la base de datos, nunca en el árbol versionado. Ninguna función
  nueva puede escribir datos de un usuario real en un fichero del repo.
- **Ningún audio ni transcripción se sube a un tercero** sin que esté escrito en el README qué se
  envía y a dónde. Ahora mismo: nada sale.
- **Todo detrás de autenticación.** Un solo usuario, pero una URL difícil de adivinar no es
  autenticación.
- **La aplicación no llama a ningún modelo de IA.** El contenido está pre-generado por el agente
  entre sesiones. Una llamada en tiempo de ejecución rompe el coste cero y la independencia de
  cualquier API.
- El micrófono se habilita **por ruta**, nunca globalmente, y sin tocar el resto de cabeceras de
  seguridad.

---

## Manejo de errores

Jerarquía de excepciones preferida, con los nombres que corresponda al lenguaje:

`ValidationError` · `BusinessRuleError` · `ExternalServiceError` · `DatabaseTransactionError` ·
`AuthorizationError` · `AuthenticationError` · `NotFoundError`

Reglas:

- Lanzar excepciones específicas con mensajes claros.
- Nada de capturar la excepción base salvo para relanzar o traducir en un límite seguro.
- Manejo centralizado desde la capa de aplicación.
- Respuestas de API saneadas, estructuradas y consistentes.
- **Toda escritura hace rollback si falla.** Transacciones cortas, atómicas y explícitas.

---

## Persistencia

La regla original decía "todo por SQLAlchemy ORM". Generalizada:

- **Nunca consultas construidas por concatenación de cadenas.**
- Escrituras con patrón seguro ante fallos: o se completa, o se revierte por entero.
- Confirmar solo después de que pasen todas las validaciones.
- Transacciones cortas. Evitar N+1 con estrategias de carga adecuadas.
- La persistencia no vive en las rutas.

**Si el almacenamiento son ficheros JSON** — que es como arranca este proyecto — las mismas reglas
se traducen así:

- Escritura atómica: escribir a un temporal y renombrar, nunca sobrescribir en sitio. Un fallo a
  media escritura no puede dejar el progreso corrupto.
- Validar el contenido contra un esquema **al leer**, no solo al escribir. Un fichero editado a mano
  es entrada externa.
- Versionar el esquema dentro del propio fichero, para poder migrar sin adivinar.
- El acceso a datos va **detrás de una interfaz**, para que cambiar JSON por una base de datos sea
  sustituir una implementación y no tocar el resto.

---

## Logging

- Solo cuando aporta valor operativo.
- **Nunca** secretos, tokens, contraseñas, cadenas de conexión ni datos personales sensibles.
- Nada de logs que narren el flujo obvio ni que dupliquen la propagación de una excepción.

---

## Comentarios y estilo

- Sin comentarios redundantes ni que expliquen lo obvio.
- Comentar solo intención no evidente, restricciones de seguridad o decisiones arquitectónicas.
- Nombres explícitos y descriptivos por encima de comentarios.
- Funciones con una responsabilidad. Sin clases monolíticas ni métodos gigantes.
- Reutilizar helpers existentes antes de crear abstracciones nuevas.
- Sin abstracciones prematuras ni extensibilidad especulativa.
- Composición sobre herencia salvo justificación conceptual.

---

## Validación y entrega

Después de cada cambio, **siempre**:

1. Resumir qué cambió
2. Listar archivos tocados
3. Declarar supuestos o riesgos
4. Describir la validación realizada
5. **Mencionar lo que no se pudo verificar**

Prioridad de validación: la comprobación más pequeña relevante primero, pruebas dirigidas antes que
la suite completa, y verificación de importación y arranque antes de cualquier refactor amplio.

El punto 5 no es opcional. Decir "listo" sobre algo que no se ejecutó es el peor fallo posible aquí.

---

## Lo que se debe evitar

Valores hardcodeados · consultas por concatenación · lógica de negocio en las rutas · comentarios
innecesarios · loggers innecesarios · abstracciones sin uso · refactors masivos no pedidos · efectos
secundarios ocultos · captura silenciosa de excepciones amplias · defaults inseguros · entrada
externa sin validar · controladores acoplados a detalles de infraestructura.

---

## Mentalidad

Al resolver una tarea aquí, elegir siempre la opción:

**simple** sobre ingeniosa · **explícita** sobre mágica · **segura** sobre conveniente ·
**mantenible** sobre sobre-ingeniada · **mínima** sobre especulativa.
