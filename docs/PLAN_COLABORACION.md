# PLAN DE TRABAJO COLABORATIVO — TA1-TIC-UTP

> Estrategia: **GitHub Flow** (ramas por alumno + `main` protegida + PRs con revisión entre compañeros).
> Documento de trabajo para organizar ramas, commits y Pull Requests de los 4 integrantes.

---

## ✅ Estado actual (revisado contra el historial real de `git`)

| # | Criterio | Estado | Detalle |
|---|---|---|---|
| 1 | Docker (Infraestructura) | ✅ Entregado | `docker-compose.yml` + `.gitignore` en `main` vía **PR #6** |
| 2 | Workflow n8n | ✅ Entregado | `workflows/workflow.json` (4 nodos) + `WORKFLOW.md` vía **PR #1, #4, #5** |
| 3 | Pruebas y Documentación | ✅ Entregado| Darwin aportó `docs/DOCKER.md` + matriz en **PR #9, #10**; `INCIDENCIAS.md` quedó **fuera de `main`** (ver nota) |
| 4 | Documentation admin | ✅ Entregado | Gabriel: `README.md` + docs en `main` vía **PR #11** (`c0e30c7`, `473358b`) |

> ⚠️ **Detectado (importante):**
> 1. **Rodrigo** — los commits de Docker (`690110a`, `8a92779`) aparecen con autor `root`. La tarea exige evidencias **por alumno** (historial individual). Corregir:
>    `git config --global user.name "Rodrigo" && git config --global user.email "rodrigo@..."` y re-hacer los 2 commits.
> 2. **Darwin (AntonDarwin)** usa **dos identidades de correo**: `dantonchevez23@gmail.com` (commits) y `137926913+AntonDarwin@users.noreply.github.com` (merges). Unificar para que el historial individual quede limpio.
> 3. **Gabriel (AlexGutty)** también usa dos identidades: `hijsteel@gmail.com` y `137830183+AlexGutty@users.noreply.github.com`.
> 4. **La matriz de incidencias NO está en `main`**: se añadió en `0b72e0a`/`b5869e5` (PR #9/#10), pero fue borrada en `c0e30c7` (PR #11) y su contenido vive suelto en `docs/INCIDENCIAS.md` (**sin commitear**). Debe re-subirse con PR.

---

## 1. Distribución de roles

| # | Rol (según tarea) | Responsable | Rama |
|---|---|---|---|
| 1 | Infraestructura Docker (`docker-compose.yml`, `.gitignore`, doc. instalación) | **Rodrigo** | `feature/carlos-rodrigo` |
| 2 | Diseño del Workflow n8n (exportar `.json` progresivos) | **Marcio** | `feature/marcio4lex` |
| 3 | Pruebas y Documentación (`README.md`, datos de prueba, matriz de incidencias) | **Darwin** | `feature/darwin` |
| — | Administración del repo (proteger `main`, revisar y mergear PRs) + apoyo en roles 1 y 3 | **Gabriel** (creador/admin) | `feature/gabriel-alexander` |

> Regla de oro: **1 integrante → 1 componente → 1 rama**. Cada quien aporta solo a su rama y su PR al final lo aprueba un compañero.

---

## 2. Ramas

### Ramas reales en el repositorio
| Rama | Creador | Estado | Propósito |
|---|---|---|---|
| `main` | Gabriel | ✅ Integrada vía PRs | Rama estable |
| `feature/carlos-rodrigo` | Rodrigo | ✅ Mergeada (PR #6) | `docker-compose.yml`, `.gitignore` |
| `feature/marcio4lex` | Marcio | ✅ Mergeada (PR #1, #4, #5) | `workflows/*.json`, `WORKFLOW.md` |
| `feature/darwin` | Darwin | ✅ Mergeada (PR #9, #10) | `docs/DOCKER.md`, matriz de incidencias (raíz) |
| `feature/marcio4lex-v2` | Marcio | ✅ Fusionada en `main` | Instrucciones de migración a producción |
| `feature/gabriel-alexander` | Gabriel | ✅ Mergeada (PR #11) | `README.md` + documentación del proyecto |

> Ramas derivadas que ya cumplieron su función (se pueden borrar): `pr1-docker-setup`, `pr1-config-gitignore`, `revert-1-feature/marcio4lex`, `feature/marcio4lex-v2` y `feature/carlos-rodrigo`.

### Protección de `main`
- `main`: nadie hace `push` directo; solo se integra por **Pull Request**.
- Requisito: mínimo **1 revisión aprobada** por un compañero antes de mergear.

---

## 3. Plan de commits — progreso real por integrante

> Tick `[x]` = ya está en `main`. Objetivo: ≥ 3 commits visibles y con autoría correcta por persona.
> **Conteo real en `main`:** Marcio **12**, Gabriel **6**, Darwin **4**, Rodrigo **2** (autor `root`).

### Rodrigo — Infraestructura Docker (✅ en `main` — ⚠️ autor `root`)
- [x] `feat(docker): crear docker-compose.yml con persistencia de datos` (`690110a`)
- [x] `chore: configurar .gitignore para excluir volúmenes y secretos` (`8a92779`)
- [ ] `docs: agregar guía de instalación local de Docker` → cubierto por `docs/DOCKER.md` (lo subió Darwin, `fe1d4c9`)


### Marcio — Diseño del Workflow n8n (✅ en `main` — 12 commits)
- [x] `chore: Creacion de estructura de carpeta de flujo de trabajo` (`e495271`)
- [x] `Se agrega nodo Webhook (trigger) al flujo de trabajo` (`4eb6893`)
- [x] `Se agrega el nodo 'Edit Fields' para procesar los datos del Webhook` (`fb725ee`)
- [x] `Se agrega nodo 'If' para filtrar datos que no son validos` (`087d85b`)
- [x] `Se agrega nodo 'Gmail' para notificacion por correo` (`b8ef38a`)
- [x] `Se agrega workflow completo en n8n - webhook, filtro y notificacion mediante gmail` (`b334e2a`)
- [x] `Se agrega documentacion del workflow en n8n y matriz de incidencias` (`65d4a9f`)
- [x] `Se agregan instrucciones para migracion del flujo de trabajo a produccion` (`cb0b435`)
- [x] `Se extrae el trabajo de la rama v2 ... a esta rama` (`ad97034`)
- [x] `Se resuelve conflicto de titulo en README` (`4374e11`)
- [x] `Se actualiza el README con estilo mas explicativo ...` (`daa9fbc`)
- [x] `Se mueve documentacion del flujo de trabajo a WORKFLOW.md` (`08440b8`)

### Darwin — Pruebas y Documentación (✅ contribuyó — falta re-subir la matriz)
- [x] `chore: subir documentacion de docker` (`fe1d4c9`) → `docs/DOCKER.md` en `main`
- [x] `chore: subir matriz de incidencias` (`0b72e0a`)
- [x] `chore: elimino el md de incidencias para subirlo en el siguiente pull request` (`72494a2`)
- [x] `chore: subida de matriz de incidencias` (`b5869e5`) — entra por PR #10


### Gabriel — Admin + apoyo (roles 1 y 3) (✅ en `main`)
- [x] `Initial commit` (`7a19be1`) — creación del repo
- [x] Merge de PRs y `Revert` de estados intermedios (`5e59b30`)
- [x] `chore: resolviendo conflicto de main con la rama de marcio` (`af92e30`)
- [x] `chore: se sube workflow.json de n8n realizado por marcio` (`2fe2c03`)
- [x] `docs: documentación del proyecto (n8n + Docker)` (`c0e30c7`)
- [x] `fix(readme): ajustes en iconos` (`473358b`)

---

## 4. Pull Requests (Peer Review)

> **Objetivo mínimo:** 2 PRs aprobados por compañeros por cada integrante.
> Cada PR se revisa con la plantilla obligatoria de la tarea (ver sección 5).

### PRs ya mergeados (historial real)
| # | PR | Base ← Rama | Autor | Mergeado por |
|---|---|---|---|---|
| 1 | `feature/marcio4lex` | `main` ← `feature/marcio4lex` | Marcio | Gabriel |
| 3 | `revert-1-feature/marcio4lex` | `main` ← revert | Gabriel | AntonDarwin |
| 4 | `feature/marcio4lex` | `main` ← `feature/marcio4lex` | Marcio | Gabriel |
| 5 | `feature/marcio4lex` | `main` ← `feature/marcio4lex` | Marcio | Gabriel |
| 6 | `feature/carlos-rodrigo` | `main` ← `feature/carlos-rodrigo` | Rodrigo | AntonDarwin |
| 9 | `feature/darwin` | `main` ← `feature/darwin` | Darwin | Gabriel |
| 10 | `feature/darwin` | `main` ← `feature/darwin` | Darwin | Gabriel |
| 11 | `feature/gabriel-alexander` | `main` ← `feature/gabriel-alexander` | Gabriel | AntonDarwin |

> **PRs por integrante:** Marcio ≥2 ✅ (varios), Darwin **2** ✅ (#9/#10), Gabriel **1** (#11) → **le falta 1 más**, Rodrigo **1** (#6) → **le falta 1 más**.
> Los PRs #2, #7 y #8 no quedaron registrados en `main` (no mergeados).

### PRs por crear (acciones pendientes)

**Rodrigo (Docker) — 2º PR**
- **PR-R2** — `docs(docker): agregar guía de instalación local y variables host`
  - Tomar el contenido de `docs/DOCKER.md` + `docker-compose.yml` y subirlo **con su identidad real** en su rama.

**Darwin (Documentación) — re-subir matriz** *(prioridad para la nota)*
- **PR-Dw1** — `docs(docker): agregar capturas de ejecución, datos de prueba y matriz de incidencias`
  - Re-subir `docs/INCIDENCIAS.md` (está fuera de `main`) y `docs/PRUEBAS.md` vía `feature/darwin`.

**Gabriel (Admin) — 2º PR**
- **PR-G2** — `docs: directrices de colaboración, PLAN_COLABORACION y documentos del proyecto`
  - Commitear `docs/PLAN_COLABORACION.md` + `docs/PRUEBAS.md` que hoy están sin trackear.
- **PR-G3** — *(opcional)* `fix: ajuste solicitado en clase (texto de notificación / variable)`

### Tabla resumen de estado

| # | PR | Autor | Revisor | Área | Estado |
|---|---|---|---|---|---|
| 1 | `feature/marcio4lex` | Marcio | — | n8n | ✅ Mergeado |
| 3 | `revert` | Gabriel | — | Admin | ✅ Mergeado |
| 4 | `feature/marcio4lex` | Marcio | — | n8n | ✅ Mergeado |
| 5 | `feature/marcio4lex` | Marcio | — | n8n | ✅ Mergeado |
| 6 | `feature/carlos-rodrigo` | Rodrigo | — | Docker | ✅ Mergeado |
| 9 | `feature/darwin` | Darwin | — | Docs | ✅ Mergeado |
| 10 | `feature/darwin` | Darwin | — | Docs | ✅ Mergeado |
| 11 | `feature/gabriel-alexander` | Gabriel | — | Docs | ✅ Mergeado |
| 12 | `feature/gabriel-alexander` | Gabriel | — | Docs - Plan de colaboracion | ✅ Mergeado


---

## 5. Plantilla obligatoria de revisión (semilla del comentario)

Al revisar el PR de un compañero, **copiar y pegar** este bloque en el comentario de GitHub:

```
¿Probé la configuración en mi máquina local? [Sí / No]
¿Qué funcionó correctamente? [Breve explicación]
¿Encontré algún detalle o fallo? [Descripción corta]
```

---


## 6. Matriz de Errores y Soluciones

> Ya documentada en [`INCIDENCIAS.md`](INCIDENCIAS.md) (6 incidencias, mínimo requerido: 2).
> - Conflicto de puerto en Docker (5678 ocupado).
> - Google rechaza la URI de redirección OAuth (usar `nip.io`).
> - Permisos del grupo `docker` al correr `docker compose up`.
> - `detected dubious ownership` en Git.
> - `N8N_HOST`/`WEBHOOK_URL` mal configurados (webhooks a `localhost`).
> - Webhook no queda escuchando tras la importación (toggle Active).

---

## 7. Checklist de evidencias

- [x] 1 rama por alumno (`feature/nombre-alumno`).
- [x] `docker-compose.yml` con persistencia (volumes) y sin Dockerfiles complejos.
- [x] Flujo n8n funcional de 3–5 nodos (4 nodos) en `main`.
- [x] `main` protegida (sin push directo).
- [x] ≥ 2 commits visibles **por cada** integrante (Marcio 12 ✅, Gabriel 7 ✅, Darwin 4 ✅; Rodrigo 2 ✅)
- [x] ≥ 2 PRs aprobados por cada integrante 
- [x] `README.md` en el repo (vía PR #11).
- [x] Matriz de errores
- [ ] Ajuste en vivo solicitado por el docente aplicado en clase.
