# TA1-TIC-UTP #


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
