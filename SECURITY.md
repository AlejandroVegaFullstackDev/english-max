# Seguridad y privacidad

Este repositorio es público. Los datos de aprendizaje de una persona no lo son.

---

## Lo que nunca entra al repositorio

**Secretos.** Contraseñas, claves de API, tokens, cadenas de conexión, certificados. Van en `.env`
(ignorado) o en el gestor de secretos de Vercel. Nunca en el código, ni siquiera "temporal para
probar": queda en el historial de git para siempre.

**Datos personales de aprendizaje.** Nivel, errores, progreso. Viven en la base de datos, no en el
repositorio.

**Grabaciones de voz.** Son datos biométricos. Toda la carpeta y todas las extensiones de audio
están ignoradas. El análisis de pronunciación corre **en local**; el audio no sale de la máquina
del usuario.

**Datos de terceros.** Si se practica describiendo trabajo real, cuidado con nombres de clientes,
arquitecturas internas y credenciales del empleador. Un ejercicio de inglés no justifica filtrar
información de una empresa.

---

## Reglas de la aplicación

**Todo detrás de autenticación.** Un solo usuario, pero son datos personales. Una URL difícil de
adivinar no es autenticación: se indexa, se comparte por accidente y queda en el historial de
cualquier navegador que la abra.

**El micrófono se habilita por ruta.** Las cabeceras de seguridad razonables lo desactivan en todo
el sitio, y así debe quedarse salvo en la ruta de práctica. Nunca globalmente.

**Ningún audio ni transcripción se sube a un tercero** sin que esté escrito en el README qué se
envía y a dónde. Ahora mismo: nada sale.

**Nada de trazas ni detalles internos en las respuestas de la API.** Un stack trace en producción
es un mapa del sistema.

---

## Antes del primer push de un fork

```bash
# Ver que se subiria de verdad
git ls-files

# Buscar secretos antes de publicar
git grep -niE "api[_-]?key|secret|password|token|BEGIN .*PRIVATE KEY"
```

Si aparece algo, **no basta con borrar el archivo y commitear encima.** Hay que reescribir el
historial (`git filter-repo`) o rehacer el repo. Y si ya se publicó, **rota la credencial**: asume
que está comprometida.

---

## Si añades una carpeta con datos de usuario

Añádela a `.gitignore` **antes** del primer commit que la toque. Un fichero ya rastreado sigue
rastreado aunque después lo ignores.

---

## Reportar un problema

Si encuentras un secreto filtrado o un fallo de privacidad, abre un issue **sin incluir el secreto
en el texto**. Basta con señalar el archivo y la línea.
