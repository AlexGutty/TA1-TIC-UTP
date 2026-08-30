# PLAN DE TRABAJO COLABORATIVO — TA1-TIC-UTP

> Estrategia: **GitHub Flow** (ramas por alumno + `main` protegida + PRs con revisión entre compañeros).
> Documento de trabajo para organizar ramas, commits y Pull Requests de los 4 integrantes.

---

## 1. Distribución de roles

| # | Rol (según tarea) | Responsable | Rama |
|---|---|---|---|
| 1 | Infraestructura Docker (`docker-compose.yml`, `.gitignore`, doc. instalación) | **Rodrigo** | `feature/rodrigo-docker` |
| 2 | Diseño del Workflow n8n (exportar `.json` progresivos) | **Marcio** | `feature/marcio4lex` |
| 3 | Pruebas y Documentación (`README.md`, capturas, datos de prueba, matriz de incidencias) | **Darwin** | `feature/darwin` |
| — | Administración del repo (proteger `main`, revisar y mergear PRs) + apoyo en roles 1 y 3 | **Gabriel** (creador/admin) | `feature/gabriel-alexander` |

> Regla de oro: **1 integrante → 1 componente → 1 rama**. Cada quien aporta solo a su rama y su PR al final lo aprueba un compañero.

---

## 2. Ramas

### Protección de `main`
- `main` protegida: nadie hace `push` directo, solo se integra por **Pull Request**.
- Requisitos para mergear: mínimo **1 revisión aprobada** de otro compañero y CI en verde (si se configura).

### Ramas por colaborador
| Rama | Creador | Propósito |
|---|---|---|
| `main` | Gabriel | Rama oficial estable / solo merge de PRs |
| `feature/rodrigo-docker` | Rodrigo | `docker-compose.yml`, `.gitignore`, guía de instalación local |
| `feature/marcio4lex` | Marcio | `workflows/*.json` del flujo n8n en sus etapas |
| `feature/darwin` | Darwin | `README.md`, capturas, datos de prueba, matriz de incidencias |
| `feature/gabriel-alexander` | Gabriel | Documentación general, ajustes solicitados en clase, apoyo roles 1 y 3 |

---

## 3. Plan de commits progresivos por integrante

> Cada commit debe ser **pequeño, atómico y descriptivo** (un cambio → un commit). Mínimo 3 commits por persona para evidenciar trabajo real.

### Rodrigo — Infraestructura Docker
- [ ] `docs: agregar guía de instalación local de Docker`
- [ ] `feat(docker): crear docker-compose.yml con persistencia de datos`
- [ ] `chore: configurar .gitignore para excluir volúmenes y secretos`
- [ ] `fix(docker): resolver conflicto de puerto 5678 y agregar variables N8N_HOST/WEBHOOK_URL`

### Marcio — Diseño del Workflow n8n (commits progresivos por etapas)
- [ ] `feat(n8n): esqueleto del flujo - nodo Webhook (trigger)`
- [ ] `feat(n8n): agregar nodo Edit Fields (Set) para formatear datos`
- [ ] `feat(n8n): agregar nodo If para filtrar email vacío`
- [ ] `feat(n8n): conectar nodo Gmail y completar flujo de 4 nodos`
- [ ] `test(n8n): exportar JSON del flujo y probar con petición curl`

### Darwin — Pruebas y Documentación
- [ ] `docs: crear README.md con descripción del proyecto y stack`
- [ ] `docs: documentar los 4 nodos del flujo y cómo probarlo con curl`
- [ ] `docs: agregar capturas de ejecución de cada integrante`
- [ ] `docs: crear matriz de errores y soluciones (mínimo 2 incidencias)`

### Gabriel — Admin + apoyo (roles 1 y 3)
- [ ] `chore: configurar protección de main y directrices de PR`
- [ ] `docs: integrar README completo y validar estructura final`
- [ ] `fix: ajuste solicitado en clase (cambio de texto de notificación/variable)`

---

## 4. Pull Requests (Peer Review)

> **Objetivo mínimo:** 2 PRs aprobados por compañeros por cada integrante.
> Cada PR se revisa con la plantilla obligatoria de la tarea (ver sección 5).
> **Base de todos los PRs:** `main`. **Tipo:** rama por alumno.

### 🏗️ Rodrigo — Infraestructura Docker

#### PR-D1 — `feature/rodrigo-docker` → `main`
- **Título:** `feat(docker): levantar n8n con docker-compose y persistencia de datos`
- **Descripción de la sección del PR:**
  > - Se agrega `docker-compose.yml` con el servicio `n8n` (imagen `n8nio/n8n`), mapeo de puerto `5678:5678`, volumen para persistir datos (`~/.n8n`) y variables de entorno básicas.
  > - Se agrega `.gitignore` para excluir `node_modules/`, volúmenes locales, archivos `.env` y secreto/generado de n8n.
  > - Registro la evidencia en `docs/instalacion-local.md` con los pasos para levantar el contenedor.
- **Archivos que toca:** `docker-compose.yml`, `.gitignore`, `docs/instalacion-local.md`
- **Cómo probar:** copiar el repo, correr `docker compose up -d` y verificar que n8n responde en `http://localhost:5678`.
- **Aprobación:** revisar con la plantilla y ver la captura de n8n corriendo.

#### PR-D2 — `feature/rodrigo-docker` → `main`
- **Título:** `fix(docker): persistencer volúmenes y resolver conflicto de puerto + variables de host`
- **Descripción de la sección del PR:**
  > - Ajusta variables `N8N_HOST`, `N8N_EDITOR_BASE_URL` y `WEBHOOK_URL` (con dominio `nip.io`) para que n8n genere webhooks y OAuth válidos.
  > - Documenta la solución al error de puerto `5678` ocupado y a `permission denied` del grupo `docker`.
- **Archivos que toca:** `docker-compose.yml`, `docs/instalacion-local.md`, `README.md` (sección Docker)
- **Cómo probar:** reiniciar contenedor y confirmar que el webhook recibe la petición de prueba.
- **Aprobación:** verificar que los cambios de credenciales/host están aplicados y documentados.

### 🔄 Marcio — Diseño del Workflow n8n

#### PR-M1 — `feature/marcio4lex` → `main`
- **Título:** `feat(n8n): esqueleto del flujo - Webhook trigger + Edit Fields (Set)`
- **Descripción de la sección del PR:**
  > - Exporto la primera etapa del flujo: nodo **Webhook** (trigger, método POST) que recibe `{nombre, email}`.
  > - Nodo **Edit Fields (Set)** que extrae/formatea `nombre` y `email` del body.
  > - Archivo exportado en `workflows/workflow-etapa1.json` (commit progresivo de una etapa).
- **Archivos que toca:** `workflows/workflow-etapa1.json`
- **Cómo probar:** importar el `.json` en n8n modo test y enviar un `curl -X POST .../webhook-test/<id>`.
- **Aprobación:** el JSON importa sin errores y los 2 primeros nodos se ven en verde.

#### PR-M2 — `feature/marcio4lex` → `main`
- **Título:** `feat(n8n): completar flujo de 4 nodos - filtro If + notificación Gmail`
- **Descripción de la sección del PR:**
  > - Agrego nodo **If** que filtra y solo continúa si `email` no está vacío.
  > - Conecto nodo **Gmail (Send a message)** que envía la notificación con los datos procesados.
  > - Actualizo `workflows/workflow.json` con el flujo completo y documento la prueba con `curl` en `README.md`.
- **Archivos que toca:** `workflows/workflow.json` (+ `README.md`)
- **Cómo probar:** ejecutar el flujo completo y verificar que los 4 nodos quedan en verde y llega el correo.
- **Aprobación:** el flujo de 3–5 nodos corre en el contenedor y la notificación llega al destino.

### 📄 Darwin — Pruebas y Documentación

#### PR-Dw1 — `feature/darwin` → `main`
- **Título:** `docs: crear README.md con descripción del proyecto, stack y nodos del flujo`
- **Descripción de la sección del PR:**
  > - README con objetivo del proyecto, stack (n8n + Docker + GitHub Flow) y cómo levantar el entorno.
  > - Documento los 4 nodos del flujo y cómo probarlo con `curl`.
- **Archivos que toca:** `README.md`
- **Cómo probar:** revisar que el README se renderiza bien en GitHub y cubre descripción + stack + nodos.
- **Aprobación:** contenido claro, coherente con lo implementado y con la estructura del repo.

#### PR-Dw2 — `feature/darwin` → `main`
- **Título:** `docs: agregar capturas de ejecución, datos de prueba y matriz de incidencias`
- **Descripción de la sección del PR:**
  > - Se agregan capturas de cada integrante trabajando (carpeta `docs/capturas/`) y datos de prueba.
  > - **Matriz de errores y soluciones** con mínimo 2 incidencias (puerto ocupado, URI OAuth, permisos docker, dubious ownership) explicadas con sus propias palabras.
- **Archivos que toca:** `docs/capturas/*.png`, `README.md` (sección incidencias), `docs/datos-prueba/*.json`
- **Cómo probar:** verificar que las imágenes se ven en GitHub y que la matriz explica causa + solución.
- **Aprobación:** al menos 2 incidencias bien resueltas y capturas visibles de todos los integrantes.

### 👑 Gabriel — Admin + apoyo (roles 1 y 3)

#### PR-G1 — `feature/gabriel-alexander` → `main`
- **Título:** `docs: directrices de colaboración y protección de main`
- **Descripción de la sección del PR:**
  > - Guía de colaboración (ramas, commits, reglas de PR) para el equipo.
  > - Documento `PLAN_COLABORACION.md` y configuración de la estrategia GitHub Flow.
- **Archivos que toca:** `PLAN_COLABORACION.md`, `README.md` (sección de colaboración)
- **Cómo probar:** revisar que las reglas se entienden y que explican cómo contribuye cada rol.
- **Aprobación:** el equipo entenderá cómo colaborar y las reglas están claras.

#### PR-G2 — `feature/gabriel-alexander` → `main`
- **Título:** `fix: ajuste solicitado en clase (texto de notificación / variable)`
- **Descripción de la sección del PR:**
  > - Modificación menor pedida por el docente en la sustentación: cambio de texto de la notificación o de un valor de variable en el flujo.
  > - `git log` evidencia el cambio en vivo durante la clase.
- **Archivos que toca:** `workflows/workflow.json` (o variable del nodo), `README.md`
- **Cómo probar:** ejecutar de nuevo el flujo y confirmar que el correo/usuario sale con el nuevo texto.
- **Aprobación:** el cambio pedido quedó aplicado y se muestra el ajuste en vivo.

---
> **Regla:** el autor del PR **nunca se aprueba su propio PR**. Cada tablero exige el resumen de qué cambió y cómo probarlo.

### Tabla resumen de PRs

| # | PR | Base ← Rama | Autor | Revisor | Área | Estado |
|---|---|---|---|---|---|---|
| 1 | PR-D1 | `main` ← `feature/rodrigo-docker` | Rodrigo | Marcio | Docker | 🔲 |
| 2 | PR-D2 | `main` ← `feature/rodrigo-docker` | Rodrigo | Darwin | Docker | 🔲 |
| 3 | PR-M1 | `main` ← `feature/marcio4lex` | Marcio | Rodrigo | n8n | 🔲 |
| 4 | PR-M2 | `main` ← `feature/marcio4lex` | Marcio | Darwin | n8n | 🔲 |
| 5 | PR-Dw1 | `main` ← `feature/darwin` | Darwin | Rodrigo | Docs | 🔲 |
| 6 | PR-Dw2 | `main` ← `feature/darwin` | Darwin | Marcio | Docs | 🔲 |
| 7 | PR-G1 | `main` ← `feature/gabriel-alexander` | Gabriel | Rodrigo | Admin | 🔲 |
| 8 | PR-G2 | `main` ← `feature/gabriel-alexander` | Gabriel | Marcio | Admin | 🔲 |

---

## 5. Plantilla obligatoria de revisión (semilla del comentario)

Al revisar el PR de un compañero, **copiar y pegar** este bloque en el comentario de GitHub:

```
¿Probé la configuración en mi máquina local? [Sí / No]
¿Qué funcionó correctamente? [Breve explicación]
¿Encontré algún detalle o fallo? [Descripción corta]
```

---

## 6. Orden de implementación sugerido

1. **Rodrigo** levanta n8n con `docker-compose.yml` y publica base en `main` (PR #1).
2. **Marcio** crea el flujo de 4 nodos sobre ese entorno y exporta los `.json` en su rama.
3. **Darwin** y **Gabriel** documentan el flujo, toman capturas y arman el `README.md` + matriz de incidencias.
4. Todos aprueban PRs entre sí con la plantilla (mínimo 2 by each).
5. **Gabriel** (admin) mergea los PRs aprobados y protege `main`.

---

## 7. Matriz de Errores y Soluciones (mínimo 2 por README)

> Incluir en `README.md` con la propia explicación. Candidatos ya registrados:
> - Conflicto de puerto en Docker (5678 ocupado).
> - Google rechaza la URI de redirección OAuth (usar `nip.io`).
> - Permisos del grupo `docker` al correr `docker compose up`.
> - `detected dubious ownership` en Git.

---

## 8. Checklist de evidencias

- [ ] `main` protegida (sin push directo).
- [ ] 1 rama por alumno (`feature/nombre-alumno`).
- [ ] ≥ 3 commits visibles por integrante.
- [ ] ≥ 2 PRs aprobados por cada integrante (con plantilla respondida).
- [ ] `docker-compose.yml` con persistencia (volumes) y sin Dockerfiles complejos.
- [ ] Flujo n8n funcional de 3–5 nodos en el contenedor.
- [ ] `README.md` con capturas de cada integrante + matriz de errores resueltos.
- [ ] Ajuste en vivo solicitado por el docente aplicado en clase.
