<h1 align="center">TA1-TIC-UTP</h1>

## Diseño del Workflow (n8n)

El flujo tiene 4 nodos y sigue una lógica simple: recibir datos, limpiarlos, filtrarlos, y avisar por correo.

Arranca con un **Webhook** que escucha peticiones POST, como si fuera la entrada de un formulario externo. Lo que llega ahí pasa a **Edit Fields**, que toma el body crudo y saca solo lo que necesitamos: `nombre` y `email`. Después viene un nodo **If** que corta el flujo si el email viene vacío — una validación mínima pero necesaria. Y si pasa ese filtro, el último nodo es **Gmail**, que manda la notificación con los datos ya procesados.

El JSON exportado vive en `workflows/workflow.json`.

## Cómo ejecutarlo

Con n8n ya corriendo en Docker (`docker compose up -d`), entra al editor desde el navegador y usa el menú `⋯` → **Import from File** para cargar `workflows/workflow.json`. Vas a ver los 4 nodos conectados tal como se describió arriba.

Antes de probarlo hay un paso obligatorio: conectar la cuenta de Gmail en el nodo correspondiente. n8n nunca guarda credenciales dentro del JSON por seguridad, así que cada quien tiene que autorizar la suya (más abajo están los pasos completos para eso).

Ya con la credencial lista, dale a **Execute workflow** — el Webhook queda escuchando. Para simular el envío de datos, basta un `curl`:

```bash
curl -X POST "http://<host>:5678/webhook-test/<id>" \
  -H "Content-Type: application/json" \
  -d '{"nombre":"Marcio","email":"marcio@test.com"}'
```

Si todo va bien, los 4 nodos se ponen verdes en cadena y el correo llega en segundos. Si alguno queda en rojo, ese es el punto exacto donde hay que mirar — probablemente algo parecido ya está en la matriz de errores de abajo.

## Pasar de desarrollo a producción

El workflow es portable, así que migrarlo es cuestión de repetir la importación en otro entorno. Los puntos que sí cambian:

**Credenciales.** Como no viajan en el JSON, hay que crear una nueva desde cero: un proyecto en [Google Cloud Console](https://console.cloud.google.com), la Gmail API habilitada, la pantalla de consentimiento OAuth configurada como "External" (con el correo destino agregado en "Test users"), y un Client ID tipo "Web application". El redirect URI tiene que coincidir exactamente con lo que muestra n8n al crear la credencial — algo como `http://<host>:5678/rest/oauth2-credential/callback`.

**La URL del Webhook.** Cambia según el host donde quede desplegado. Una vez importado el workflow hay que activarlo (el toggle de arriba a la derecha) para que la Production URL quede escuchando de verdad.

**Un detalle que puede trabar todo:** si el entorno de producción se accede por IP en vez de un dominio, Google directamente rechaza esa IP como redirect URI válido. La salida rápida es usar [nip.io](https://nip.io), que convierte cualquier IP en un subdominio funcional (`192.168.1.10` se vuelve `192.168.1.10.nip.io`) — solo hay que apuntar `N8N_HOST`, `WEBHOOK_URL` y `N8N_EDITOR_BASE_URL` a ese dominio en el `docker-compose.yml`.

## Matriz de Incidencias Técnicas

| # | Error | Causa | Solución |
|---|---|---|---|
| 1 | `dubious ownership in repository` al hacer `git checkout` | El repo se clonó con `sudo`, así que Git no reconocía al usuario normal como dueño | `sudo chown -R usuario:usuario <ruta>` + `git config --global --add safe.directory <ruta>` |
| 2 | `permission denied` al conectar con la API de Docker | El usuario no estaba en el grupo `docker` | `sudo usermod -aG docker $USER` y reiniciar sesión |
| 3 | Google rechazaba el redirect URI de OAuth por no ser un dominio válido | Google no acepta IPs crudas en las credenciales OAuth2 | Usar nip.io para convertir la IP en subdominio, y ajustar las variables de host en el `docker-compose.yml` |
