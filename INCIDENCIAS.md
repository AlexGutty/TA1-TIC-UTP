# Matriz de Incidencias Técnicas

Registro de los errores que surgieron durante el taller, explicados **con nuestras propias palabras** (causa y solución). Es un insumo directo de la sección de *Documentación y Troubleshooting* de la tarea.

> La tarea pide documentar al menos **2 errores**. Aquí registramos los que realmente nos ocurrieron y sus soluciones.

---

## Resumen de incidencias

| # | Error | Área | Severidad | Estado |
|---|---|---|---|---|
| 1 | `fatal: detected dubious ownership in repository` | Git | Alta | ✅ Resuelto |
| 2 | `permission denied ... docker API` | Docker | Alta | ✅ Resuelto |
| 3 | Google rechaza redirect URI OAuth (IP no válida) | n8n / Credenciales | Alta | ✅ Resuelto |
| 4 | Puerto `5678` ocupado en el servidor compartido | Docker | Media | ✅ Resuelto |
| 5 | `N8N_HOST` / `WEBHOOK_URL` mal configurados (webhooks a `localhost`) | n8n | Alta | ✅ Resuelto |
| 6 | Flujo no se puena activar / URLs de webhook desincronizadas | n8n | Media | ✅ Resuelto |

---

## Detalle por incidencia

### Incidencia 1 — `fatal: detected dubious ownership in repository`

- **Síntoma:** al hacer `git checkout`, Git se negaba a operar sobre el repositorio.
- **Causa:** el repositorio se había clonado con `sudo` (usuario root), por lo que Git no reconocía al usuario normal como dueño de los archivos.
- **Solución:**
  ```bash
  sudo chown -R usuario:usuario <ruta>
  git config --global --add safe.directory <ruta>
  ```

---

### Incidencia 2 — `permission denied while trying to connect to the docker API`

- **Síntoma:** al correr `docker compose up` aparecía el mensaje de permisos denegados.
- **Causa:** el usuario normal no pertenecía al grupo `docker`, así que no tenía permisos para hablar con el daemon de Docker.
- **Solución:**
  ```bash
  sudo usermod -aG docker $USER
  # reiniciar sesión para que surta efecto
  ```

---

### Incidencia 3 — Google rechaza la URI de redirección OAuth

- **Síntoma:** al conectar la credencial de Gmail en n8n, Google mostraba:
  `Redireccionamiento no válido: debe terminar con un dominio público de nivel superior`.
- **Causa:** Google **no permite usar una dirección IP directa** como redirect URI en credenciales OAuth2; exige dominios válidos.
- **Solución:** usar el servicio gratuito **nip.io**, que convierte una IP en un subdominio funcional (`172.16.30.128` → `172.16.30.128.nip.io`), y apuntar `N8N_HOST`, `WEBHOOK_URL` y `N8N_EDITOR_BASE_URL` a ese dominio en el `docker-compose.yml`.

  > En nuestro caso el entorno se expone vía dominio ya reservado (`n8n.scca.site`), lo que da el mismo efecto de un dominio público válido.

---

### Incidencia 4 — Puerto `5678` ocupado

- **Síntoma:** el contenedor de n8n no podía levantarse porque el puerto interno `5678` ya estaba en uso.
- **Causa:** en un servidor compartido, otros servicios suelen quedar escuchando en `5678`.
- **Solución:** se **separó el puerto interno del externo** en el `docker-compose.yml`. El interno queda fijo en `5678` y el externo es configurable:
  ```yaml
  ports:
    - "${N8N_HOST_PORT:-5679}:5678"
  ```
  Así basta cambiar `N8N_HOST_PORT` en el `.env` sin tocar el `compose`.

---

### Incidencia 5 — `N8N_HOST` / `WEBHOOK_URL` mal configurados

- **Síntoma:** los webhooks de los workflows apuntaban a `localhost` en lugar del dominio/IP público, y dejaban de responder ante llamadas externas.
- **Causa:** las variables `N8N_HOST` y `WEBHOOK_URL` no se configuraron con la dirección pública real de la instancia.
- **Solución:** definir correctamente `N8N_HOST`, `WEBHOOK_URL` y `N8N_EDITOR_BASE_URL` en el `.env` con el dominio/IP accesible (ver [`DOCKER.md`](DOCKER.md)).

---

### Incidencia 6 — Webhook no queda escuchando tras la importación

- **Síntoma:** después de importar el flujo, el webhook no respondía a la petición de prueba.
- **Causa:** el workflow se copió, pero el toggle de *Active* no estaba encendido (o la URL de *Production* no estaba habilitada).
- **Solución:** activar el workflow desde el editor (toggle superior derecho) tras importarlo, y usar la **Test URL** al hacer la prueba con `curl` (ver [`PRUEBAS.md`](PRUEBAS.md)).

---

## Lección aprendida

La mayoría de los errores del taller no fueron de lógica del flujo, sino de **entorno**: permisos del sistema (Git/Docker) y configuración de host/credenciales en n8n. Por eso la documentación de `docker.md` y estas notas intentan dejar claras esas dos variables que más problemas causan: la **dirección pública** (`N8N_HOST`/`WEBHOOK_URL`) y la **clave de cifrado** (`N8N_ENCRYPTION_KEY`).
