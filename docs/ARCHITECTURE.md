# Arquitectura

Decisiones y por qué. Restricciones que mandan sobre todo lo demás:

- **Un solo usuario por instalación.** No hay multi-tenencia, ni la habrá.
- **Presupuesto objetivo: $0.**
- **Móvil primero**, pero utilizable desde cualquier navegador sin instalar nada.
- **La app no llama a ningún modelo.** El contenido ya existe cuando se abre.

---

## Dónde encaja esto

```
ai-skill-coach  (otro repo)                english-max  (este)
                                        
  agente + comandos                        apps/web   navegador
  genera ejercicios        --contenido-->  apps/api   endpoints
  lee el progreso          <--progreso---  base de datos
```

El agente trabaja **entre sesiones**: genera el siguiente tramo de currículo y lo deja escrito. La
web solo sirve lo que ya existe y registra cómo fue.

Eso es lo que mantiene el coste en cero. Si la web llamara a un modelo por ejercicio, sería cara y
lenta, y dependería de una API para funcionar.

**La sincronización en ambos sentidos es el problema no resuelto.** Ver ROADMAP.

---

## Los dos tipos de datos

Se confunden fácilmente y tienen requisitos opuestos.

| | **Contenido** (el curso) | **Progreso** (tú) |
|---|---|---|
| Qué es | Lecciones, ejercicios, textos, audio | Nivel, errores, qué dominas, qué repetir |
| Tamaño | Megas, y creciendo | Kilobytes al año |
| Quién escribe | El agente, al generar currículo | La app, en cada sesión |
| Frecuencia de escritura | Rara | Constante |
| ¿Versionado? | Sí, interesa el historial | Sí, interesa la evolución |
| ¿Sensible? | No | **Sí** |

### Contenido → ficheros versionados, servidos desde CDN

Markdown o JSON con metadatos tipados y **validados en build**. Se sirven desde el CDN. Coste cero,
latencia mínima, funciona sin backend.

Lo importante es que están **pre-generados**: el ejercicio ya existe cuando lo abres. No hay
llamada a ninguna IA para servirlo.

Cada ejercicio lleva metadatos: nivel, habilidad, duración, requisitos previos, y si necesita
micrófono o solo audio. Eso es lo que permite responder a *"tengo 7 minutos y no puedo hablar"*:
es un filtro sobre metadatos, no magia.

### Progreso → base de datos

Aquí es donde este proyecto se separa del repo del tutor.

En el repo del tutor el progreso es un JSON, y está bien: lo escribe un agente en un PC, una vez
cada pocos días. Aquí lo escribe un navegador en un móvil, varias veces por sesión, desde
dispositivos distintos. Eso es una base de datos.

Requisitos:

- Tier gratuito real, sin tarjeta.
- Escrituras pequeñas y frecuentes.
- Lectura del documento entero de una vez: el progreso de una persona son kilobytes.

**No Parquet.** Es columnar, pensado para leer una columna de millones de filas. Lo contrario de
esto.

**El acceso a datos va detrás de una interfaz**, para poder cambiar de proveedor sin tocar el
resto. Con tier gratuito esto no es teoría: los proveedores cambian de política.

---

## Back y front separados

Podría ser un framework con rutas de API y un solo despliegue. Se separan por dos razones
concretas, no por purismo:

1. **El front puede ser estático y vivir en CDN.** El contenido son ficheros; servirlos no necesita
   servidor. Eso es gratis y rápido en cualquier sitio del mundo.
2. **El back toca la base de datos y las credenciales.** Aislarlo deja una sola superficie con
   secretos, y hace que el contrato entre los dos sea explícito.

`packages/contracts/` tiene los tipos compartidos. Es la única dependencia entre ambos: si un
cambio en el back rompe el front, tiene que romper ahí primero, en tiempo de compilación.

---

## Voz

No es TTS clásico. El detalle está en
[ai-skill-coach/VOICE.md](https://github.com/AlejandroVegaFullstackDev/ai-skill-coach/blob/main/VOICE.md);
lo que afecta a esta app:

- **En el móvil:** escuchar audio pre-generado y grabarse con `MediaRecorder`. Sin análisis en el
  momento, solo captura.
- **En el PC:** el audio pasa por el analizador fonémico **en local**, cuando se corre el comando
  del agente. Gratis, y la voz no sale de la máquina.

Esa separación mantiene el coste en cero **y** las grabaciones en los dispositivos del usuario.

### El micrófono está bloqueado por defecto

Un `vercel.json` con cabeceras de seguridad razonables suele incluir:

```
Permissions-Policy: camera=(), microphone=(), geolocation=(), interest-cohort=()
```

Eso desactiva el micrófono en todo el sitio. Hay que permitirlo **solo en la ruta de práctica**,
nunca globalmente, y dejar el resto de cabeceras como están.

---

## Autenticación

Un solo usuario, pero **detrás de login igualmente**. Son datos personales de aprendizaje: nivel,
errores, grabaciones.

Una URL difícil de adivinar no es autenticación. Se indexa, se comparte por accidente, y queda en
el historial de cualquier navegador que la abra.
