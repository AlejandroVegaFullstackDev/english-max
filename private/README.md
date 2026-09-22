# private/

**Nada de esta carpeta se publica**, salvo este archivo. Está en `.gitignore`.

Aquí vive todo lo que describe a una persona concreta:

| Archivo | Qué contiene |
|---|---|
| `profile.md` | Nivel real, debilidades, objetivo, restricciones de tiempo |
| `progress.json` | Qué se domina, qué falla, cuándo toca repasar |
| `mistakes.md` | Errores recurrentes observados |
| `recordings/` | Audio de práctica |

## Por qué está separado

Este repositorio es open source para que cualquiera pueda montar su propio tutor. Eso significa que
el **motor** —reglas, currículo, formatos, herramientas— es público, y los **datos de la persona**
no lo son nunca.

No es solo privacidad. Un perfil de aprendizaje dice en qué eres flojo, y un repo público es para
siempre.

## Para empezar

Copia la plantilla y rellénala:

```bash
cp config/profile.example.md private/profile.md
```

## Qué nunca va aquí

Ni aquí ni en ningún sitio del repo: contraseñas, claves de API, tokens, cadenas de conexión. Esos
van en `.env` (también ignorado) o en el gestor de secretos de la plataforma.

Si necesitas versionar tu progreso —y tiene sentido, para ver la evolución— usa un **repositorio
privado aparte** y clónalo dentro de `private/`. Nunca mezcles los dos.
