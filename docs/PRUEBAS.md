# Pruebas del Flujo

Documento de **pruebas y evidencias**: cómo se probó el flujo n8n, los datos de test utilizados y las capturas de cada integrante trabajando.

> Es el soporte de la sección *Documentación y Troubleshooting* (capturas + datos de prueba) de la tarea.

---

## 1. Prerrequisitos

Para poder probar el flujo es necesario tener el entorno levantado:

- Instancia de n8n corriendo en Docker (ver [`DOCKER.md`](DOCKER.md)).
- La cuenta de Gmail autorizada en el nodo de correo (ver [`WORKFLOW.md`](WORKFLOW.md)).
- `curl` instalado en la máquina (o Postman).

---

## 2. Cómo probar el flujo (paso a paso)

1. Abrir el editor de n8n en el navegador (puerto externo por defecto `5679`).
2. Menú `⋯` → **Import from File** → cargar `workflows/workflow.json`.
3. Verificar que aparecen los 4 nodos conectados: **Webhook → Edit Fields → If → Gmail**.
4. Conectar la credencial de Gmail en el nodo **Send a message** (si aún no está autorizada).
5. Dar clic en **Execute workflow** para que el Webhook quede escuchando.
6. Enviar una petición de prueba con `curl`.

### Datos de prueba (payload)

Se recomienda probar con dos escenarios: uno válido (con email) y uno inválido (sin email) para corroborar el filtro del nodo **If**.

**Escenario A — válido (debe enviar el correo):**

```bash
curl -X POST "http://<host>:5678/webhook-test/<webhookId>" \
  -H "Content-Type: application/json" \
  -d '{"nombre":"Marcio","email":"marcio@test.com"}'
```

**Escenario B — inválido (email vacío, NO debe enviar correo):**

```bash
curl -X POST "http://<host>:5678/webhook-test/<webhookId>" \
  -H "Content-Type: application/json" \
  -d '{"nombre":"Marcio","email":""}'
```

**Escenario C — campos faltantes (sin `email`):**

```bash
curl -X POST "http://<host>:5678/webhook-test/<webhookId>" \
  -H "Content-Type: application/json" \
  -d '{"nombre":"Darwin"}'
```

> **Nota:** `webhookId` es el identificador único de tu webhook (en este flujo es
> `646b5598-d01f-4f3b-8bda-d625f3bec4c4`, pero cambia si reimportas el JSON).
> `host` es el dominio o IP pública configurado en `N8N_HOST`.

---

## 3. Resultados esperados

| Escenario | Payload | Nodo If | Resultado esperado |
|---|---|---|---|
| A | `{nombre, email}` válidos | Continúa | 4 nodos en verde + correo enviado |
| B | `{nombre, email:""}` | Corta el flujo | No envía correo (se detiene en `If`) |
| C | `{nombre}` sin `email` | Corta el flujo | No envía correo (se detiene en `If`) |

---

### Comandos para capturar evidencias

```bash
# Ver el contenedor corriendo
docker compose ps

# Logs de n8n si algo falla
docker compose logs -f n8n
```

---

## 4. Resultado de la prueba real

Se probó el flujo ejecutándolo en modo **test** y enviando una petición de prueba con `curl` (Escenario A). El flujo se ejecutó correctamente: los **4 nodos quedaron en verde** y se recibió el correo de notificación esperado en la bandeja configurada.

---

## 5 Enlaces de interés

- Detalle de los nodos: [`WORKFLOW.md`](WORKFLOW.md)
- Errores encontrados durante las pruebas: [`INCIDENCIAS.md`](INCIDENCIAS.md)
- Puesta en marcha de Docker: [`DOCKER.md`](DOCKER.md)
