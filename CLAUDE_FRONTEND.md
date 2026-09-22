# CLAUDE_FRONTEND.md — Reglas de frontend

Define cómo debe comportarse Claude Code al trabajar en la interfaz de este proyecto.

> **Sobre este documento.** Alejandro lo trajo escrito para una arquitectura de **microfrontends con
> varios equipos en paralelo** (Module Federation, dominios extraíbles a repos independientes,
> "minimizar conflictos de integración"). Este proyecto tiene **un usuario y un desarrollador**.
>
> Las **reglas de calidad** de ese documento se conservan íntegras: son buenas y aplican a cualquier
> tamaño. La **maquinaria de federación** queda pospuesta, no descartada — está en la sección final
> con el criterio exacto para activarla.
>
> El propio documento original ordena aplicar **YAGNI**. Montar microfrontends federados para un
> usuario es el ejemplo de manual de lo que YAGNI prohíbe. Se respeta el documento respetando su
> principio, no su organigrama.

---

## 1 · Objetivo

Trabajar como *frontend engineer* con estas prioridades, **en este orden**:

1. **Accesibilidad y UX por diseño**
2. **Mantenibilidad**
3. **Simplicidad**
4. **Rendimiento**
5. **Escalabilidad funcional**
6. **Consistencia con el sistema de diseño existente**

Principios: KISS · DRY · SOLID adaptado a UI · YAGNI · Atomic Design · separación
presentación/dominio/infraestructura · DDD ligero donde aporte valor.

**Preferir el cambio seguro más pequeño que resuelva la tarea.**

### Prioridad cero, específica de este proyecto

La app se usa **en el iPhone, de pie, en el bus, con siete minutos y poca batería de atención**.
Ese es el caso principal, no el escritorio. Una pantalla que necesita dos manos, conexión estable o
concentración plena **ha fallado**, por muy bien construida que esté.

---

## 2 · Política de edición

1. **Inspeccionar el patrón existente** en el módulo afectado y seguirlo, salvo que viole
   accesibilidad, mantenimiento o rendimiento.
2. Cambios estructurales **incrementales**; nada de migraciones masivas de un golpe.
3. Cada cambio se acompaña de un resumen breve y de evidencia de que se probó.
4. Nada de *force-push* sobre ramas protegidas.

---

## 3 · Reglas de trabajo obligatorias

- **Leer los archivos relevantes** antes de proponer o editar.
- Explicar la intención del cambio en el commit.
- Cambios **pequeños, localizados y reversibles**.
- Sin dependencias nuevas salvo necesidad estricta y justificada.
- No reescribir módulos no relacionados.
- No hardcodear *feature flags*, claves, rutas ni endpoints.
- Sin `console.*`, código muerto ni comentarios temporales.
- Cumplir lint y formato antes de dar nada por terminado.
- Preservar compatibilidad hacia atrás salvo *breaking change* explícito.

---

## 4 · Estructura

El documento original propone `app/ · config/ · modules/ · shared/` con flujo de dependencias
`app → modules → shared → config`. **Ese modelo se adopta**, porque separa bien y no cuesta nada.
Lo que no se adopta todavía es la federación entre repos.

```
src/
├── app/        # composición raíz, rutas, providers
├── config/     # entorno tipado, endpoints, feature flags
├── modules/    # dominios: practice/, progress/, curriculum/, speaking/
└── shared/     # ui/, hooks/, utils/, types/, guards/
```

Cada módulo mantiene junto lo suyo — páginas, componentes, servicios, hooks y tipos — de modo que
**pueda extraerse después sin cirugía**. Esa es la parte de la arquitectura de microfrontends que sí
vale desde el día uno: la cohesión. La federación es lo que se pospone.

**Flujo de dependencias estricto:** `app → modules → shared → config`. Un módulo **nunca** importa
de otro módulo. Si dos necesitan lo mismo, sube a `shared/`.

### Adaptación al stack

Si esto vive dentro del portafolio (Astro 5 + React 18 + Tailwind), entonces:

- Las páginas son `.astro` y renderizan del lado del servidor por defecto.
- React se usa **solo en islas** que de verdad necesitan interactividad: el reproductor, el grabador
  de voz, el formulario de respuesta. Una pantalla de lectura no lleva React.
- Los endpoints viven en `src/pages/api/`, siguiendo las reglas de `CLAUDE.md`.
- Tailwind ya está configurado con `applyBaseStyles: false`; respetar esa decisión.

---

## 5 · Convenciones

| Elemento | Convención |
|---|---|
| Componentes | `PascalCase` |
| Hooks | `use` + `camelCase` |
| Servicios | `*.service.ts` |
| Constantes | `UPPER_SNAKE_CASE` |
| Tests | `*.test.tsx`, co-ubicados |
| Estilos | Tailwind (el repo ya lo usa) |

Rutas tipadas. Reglas de accesibilidad de `eslint-plugin-jsx-a11y` activas.

---

## 6 · Accesibilidad, rendimiento y seguridad

- **Accesibilidad:** WAI-ARIA y contraste AA mínimo. Objetivos táctiles de 44×44 px o más: la app se
  usa caminando.
- **Seguridad:** validar y escapar entrada susceptible de XSS, CSP estricta.
- **Rendimiento:** carga diferida, memoización, imágenes optimizadas. Lighthouse ≥ 90 en
  rendimiento y accesibilidad.
- **Offline:** el caso "estoy en el bus" implica red intermitente. Los ejercicios pre-generados
  deben poder servirse desde caché; una pérdida de red no puede borrar una respuesta a medio
  escribir.

### El micrófono está bloqueado ahora mismo

`vercel.json` del portafolio incluye:

```
Permissions-Policy: camera=(), microphone=(), geolocation=(), interest-cohort=()
```

Eso **desactiva el micrófono en todo el sitio**. Para practicar habla hay que permitirlo
explícitamente, y **solo en la ruta que lo necesite** — no relajarlo globalmente. El resto de
cabeceras de seguridad se dejan como están.

---

## 7 · Validación y entrega

Tras cada cambio:

1. Resumen breve
2. Archivos tocados
3. Supuestos y riesgos
4. Evidencia de que se probó
5. **Lo que quedó sin verificar**

---

## 8 · Lo que se debe evitar

Valores sensibles hardcodeados · *God components* de más de 400 líneas · mezclar presentación con
dominio · comentarios redundantes · `console.*` en producción · dependencias circulares · refactors
masivos no pedidos · estado global sin justificación (**no meter Redux ni Zustand en una app de un
usuario hasta que el estado local demuestre no alcanzar**) · CSS global no desacoplado.

---

## 9 · Mentalidad

**Simple** sobre ingeniosa · **explícita** sobre mágica · **performante** sobre conveniente ·
**mantenible** sobre sobre-ingeniada · **mínima** sobre especulativa.

---

## 10 · Lo pospuesto, y cuándo activarlo

Del documento original, esto queda guardado, no descartado:

| Capacidad | Se activa cuando |
|---|---|
| **Module Federation** | Haya un segundo dominio con ciclo de despliegue propio |
| **Extraer módulos a repos independientes** | Trabaje más de una persona en paralelo |
| **Storybook** | Haya componentes compartidos que se reutilicen en sitios distintos |
| **Gestión de estado global** | El estado local y el servidor demuestren no alcanzar, con un caso concreto |
| **Suite de tests visuales** | Exista una interfaz estable que merezca protegerse de regresiones |

Mientras tanto, la cohesión por módulos del punto 4 mantiene la puerta abierta a todo esto sin
pagar el coste hoy.
