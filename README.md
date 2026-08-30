# TA1-TIC-UTP


## Diseño del Workflow (n8n)

El flujo consta de 4 nodos:

1. **Webhook** (Trigger, método POST) — punto de entrada que recibe los datos simulando un formulario externo.
2. **Edit Fields (Set)** — extrae y formatea los campos `nombre` y `email` del body recibido.
3. **If** — filtra el flujo, permitiendo continuar solo si el campo `email` no está vacío.
4. **Gmail (Send a message)** — envía una notificación por correo con los datos procesados.

El archivo exportado se encuentra en `workflows/workflow.json`.

### Prueba del flujo

Se probó ejecutando el workflow en modo test y enviando una petición de prueba con `curl`:

```bash
curl -X POST "http://<host>:5678/webhook-test/<id>" \
  -H "Content-Type: application/json" \
  -d '{"nombre":"Marcio","email":"marcio@test.com"}'
```

El flujo se ejecutó correctamente (los 4 nodos en verde) y se recibió el correo de notificación esperado.

## Matriz de Incidencias Técnicas

| # | Error encontrado | Causa | Solución |
|---|---|---|---|
| 1 | `fatal: detected dubious ownership in repository` al hacer `git checkout` | El repositorio se había clonado usando `sudo` (usuario root), por lo que Git no reconocía al usuario normal como dueño de los archivos | Se corrigió el dueño de la carpeta con `sudo chown -R usuario:usuario <ruta>` y se agregó una excepción con `git config --global --add safe.directory <ruta>` |
| 2 | `permission denied while trying to connect to the docker API` al correr `docker compose up` | El usuario normal no pertenecía al grupo `docker`, por lo que no tenía permisos para hablar con el servicio de Docker | Se agregó el usuario al grupo con `sudo usermod -aG docker $USER` y se reinició la sesión |
| 3 | Google rechazaba la URI de redirección OAuth (`Redireccionamiento no válido: debe terminar con un dominio público de nivel superior`) | Google no permite usar una dirección IP directa como redirect URI en credenciales OAuth2, solo dominios válidos | Se utilizó el servicio gratuito **nip.io**, que convierte una IP en un subdominio válido (ej. `172.16.30.128.nip.io`), y se configuraron las variables `N8N_HOST`, `WEBHOOK_URL` y `N8N_EDITOR_BASE_URL` en el `docker-compose.yml` para usar ese dominio en vez de la IP directa |


## Cómo importar este workflow en otro entorno (producción)

El archivo `workflows/workflow.json` es portable y puede importarse en cualquier instancia de n8n corriendo en Docker. Pasos:

1. **Traer el archivo**: hacer `git pull` sobre `main` para obtener `workflows/workflow.json`.

2. **Importar en n8n**: dentro del editor de n8n, ir al menú `⋯` (arriba, junto al nombre del workflow) → **"Import from File"** → seleccionar `workflows/workflow.json`.

3. **Reconfigurar credenciales**: por seguridad, n8n **no exporta las credenciales** dentro del JSON. El nodo `Gmail` aparecerá marcado con un error de credencial faltante. Es necesario:
   - Crear un proyecto en [Google Cloud Console](https://console.cloud.google.com) (o usar uno existente).
   - Habilitar la Gmail API.
   - Configurar la pantalla de consentimiento OAuth (tipo "External", agregando el correo de destino como "Test user").
   - Crear un **OAuth Client ID** (tipo "Web application"), usando como *Authorized redirect URI* la que muestre n8n en el formulario de credenciales (formato: `http://<host>:5678/rest/oauth2-credential/callback`).
   - Pegar el **Client ID** y **Client Secret** generados en la credencial de n8n y autorizar con la cuenta de Google correspondiente.

4. **Ajustar el Webhook**: la **Production URL** del nodo Webhook cambia según el host donde se despliegue. Cuando ya se haya importado, hay que **activar el workflow** (toggle superior derecho) para que dicha URL quede escuchando en modo producción.

> **Nota:** si el entorno de producción se accede por dirección IP (en vez de un dominio con nombre), Google no acepta IPs "normales o planas" en el *redirect URI* de OAuth. Se recomienda usar el servicio gratuito [nip.io](https://nip.io) para convertir la IP en un subdominio válido (ej. `192.168.1.10` → `192.168.1.10.nip.io`), configurando las variables de entorno `N8N_HOST`, `WEBHOOK_URL` y `N8N_EDITOR_BASE_URL` en el `docker-compose.yml` para que apunten a ese dominio.
