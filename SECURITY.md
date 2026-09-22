# Seguridad y privacidad

Este repositorio es público. Los datos de aprendizaje de una persona no lo son.

---

## Lo que nunca entra al repositorio

**Secretos.** Contraseñas, claves de API, tokens, cadenas de conexión, certificados. Van en `.env`
(ignorado) o en el gestor de secretos de la plataforma. Nunca en el código, ni siquiera "temporal
para probar": queda en el historial de git para siempre.

**Datos personales de aprendizaje.** Nivel, errores, progreso, planes. Viven en `private/`.

**Grabaciones de voz.** Son datos biométricos. Toda la carpeta y todas las extensiones de audio
están ignoradas. El análisis de pronunciación corre **en local**; el audio no sale de tu máquina.

**Datos de terceros.** Si practicas describiendo tu trabajo, cuidado con nombres de clientes,
arquitecturas internas, credenciales de tu empresa. Un ejercicio de inglés no justifica filtrar
información de tu empleador.

---

## Antes del primer push

Si clonas esto y lo haces público con tus datos dentro, ya es tarde: el historial de git conserva
lo que se borró después.

```bash
# Ver qué se subiría de verdad
git ls-files

# Buscar secretos antes de publicar
git grep -niE "api[_-]?key|secret|password|token|BEGIN .*PRIVATE KEY"
```

Si aparece algo, **no basta con borrar el archivo y commitear encima.** Hay que reescribir el
historial (`git filter-repo`) o, si el repo es nuevo y pequeño, rehacerlo desde cero. Y si ya se
publicó, **rota la credencial**: asume que está comprometida.

---

## Si añades una carpeta con datos de usuario

Añádela a `.gitignore` **antes** del primer commit que la toque. El orden importa.

---

## Reportar un problema

Si encuentras un secreto filtrado o un fallo de privacidad en este repositorio, abre un issue **sin
incluir el secreto en el texto**. Basta con señalar el archivo y la línea.
