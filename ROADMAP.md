# Roadmap

Registro de decisiones. `docs/idea-original.md` no se toca; los cambios respecto a la idea original
se anotan aquí, con fecha y motivo.

---

## Decidido

**2026-09-22 · La web y el tutor son proyectos separados.**
El tutor vive en [ai-skill-coach](https://github.com/AlejandroVegaFullstackDev/ai-skill-coach):
comandos y reglas para un agente, funciona solo, sin web. Esta app es la interfaz para practicar
desde el móvil. Mezclarlos habría acoplado el motor —que sirve para cualquier habilidad— a una
aplicación concreta de inglés.

**2026-09-22 · Back y front separados, ambos en Vercel.**
El front puede ser estático y vivir en CDN porque el contenido son ficheros. El back toca la base
de datos y las credenciales, y aislarlo deja una sola superficie con secretos.
`packages/contracts/` es la única dependencia entre ambos.

**2026-09-22 · El objetivo es B2, no C1.**
La idea original apuntaba a C1. El nivel real de partida es B1 certificado. C1 sigue siendo el
destino, pero no orienta las decisiones del día a día: lo que las orienta es sostener una llamada
técnica de 30 minutos en inglés.

**2026-09-22 · Contenido y progreso se separan.**
Contenido en ficheros versionados, validados en build, servidos desde CDN, coste cero. Progreso en
base de datos, detrás de una interfaz. Tienen requisitos opuestos. Ver `docs/ARCHITECTURE.md`.

**2026-09-22 · La IA no se llama desde la app.**
El contenido está pre-generado. El agente trabaja entre sesiones. Eso es lo que mantiene el coste
en cero y hace que la app funcione sin depender de ninguna API.

**2026-09-22 · Todo detrás de autenticación.**
Un solo usuario, pero son datos personales de aprendizaje. Una URL difícil de adivinar no es
autenticación.

**2026-09-22 · Nada de código todavía.**
Las carpetas de `apps/` están vacías a propósito. Faltan dos decisiones —base de datos y
sincronización— que determinan la forma del código. Escribirlo antes garantiza reescribirlo.

---

## Pendiente de decidir

### 1. Qué base de datos

Requisitos: tier gratuito real sin tarjeta, escrituras pequeñas y frecuentes, lectura del
documento entero.

Con tier gratuito la portabilidad no es teoría: los proveedores cambian de política. El acceso va
detrás de una interfaz desde el primer día.

### 2. Cómo se sincroniza con el repo del tutor

Es el problema central y no está resuelto.

- El agente genera contenido en ficheros de su repo. La web tiene que servirlo.
- La web registra progreso en su base de datos. El agente tiene que leerlo para planificar.

Opciones a evaluar: la web lee el contenido desde el repo público del tutor; el agente lee el
progreso vía un endpoint de solo lectura; o un export manual que el usuario corre. La tercera es
fea y puede que sea la correcta para empezar.

### 3. Autenticación

Un usuario. Lo más simple que no sea inseguro.

### 4. Micrófono por ruta

Permitirlo solo en la ruta de práctica, sin tocar el resto de cabeceras de seguridad.

### 5. Escribir progreso desde el móvil

Resuelto si el progreso está en base de datos. Vuelve a ser un problema si se decide mantenerlo en
Git.

### 6. Revisar intentos previos

Ya existió un intento parecido (Quickeng). Mirar qué funcionó, qué aburrió y por qué se abandonó
vale más que cualquier decisión de arquitectura tomada en frío.

---

## Siguiente paso

Usar el tutor durante dos o tres semanas **sin web**. Sale gratis, funciona hoy, y al final se
sabrá qué partes se usan de verdad y cuáles sobran.

Construir la web antes de eso es adivinar qué interfaz hace falta.
