# TA1-TIC-UTP

<p align="center">Automatización en <b>n8n</b> dentro de un contenedor <b>Docker</b>, desarrollada de forma colaborativa mediante <b>GitHub Flow</b>.</p>

---

## 📌 Descripción del proyecto

Este proyecto construye un flujo de automatización funcional en [n8n](https://n8n.io) corriendo en un contenedor de Docker con persistencia de datos. El flujo recibe datos (como si vinieran de un formulario externo), los procesa y envía una notificación por correo.

El código, los archivos JSON del flujo y la documentación se gestionan de forma colaborativa a través de GitHub usando **ramas individuales por integrante** y Pull Requests revisados entre compañeros.

### Nodos del flujo (4 nodos)

1. **Webhook** — trigger que escucha peticiones `POST` (entrada de datos).
2. **Edit Fields (Set)** — extrae y formatea `nombre` y `email` del body.
3. **If** — filtra el flujo: solo continúa si el `email` no está vacío.
4. **Gmail (Send a message)** — envía la notificación con los datos procesados.

> Detalle técnico del flujo en [`docs/WORKFLOW.md`](docs/WORKFLOW.md).

---

## 🧱 Stack

| Tecnología | Uso |
|---|---|
| **n8n** | Motor de automatización (4 nodos) |
| **Docker** | Contenedor de la instancia n8n (`docker-compose.yml`) |
| **GitHub Flow** | Estrategia de ramas: `main` protegida + rama por alumno |
| **n8n JSON** | Exportación del flujo en `workflows/workflow.json` |

---

## 🚀 Puesta en marcha (resumen)

1. Clonar el repositorio:
   ```bash
   git clone git@github.com:AlexGutty/TA1-TIC-UTP.git
   cd TA1-TIC-UTP
   ```
2. Configurar variables y levantar el contenedor:
   ```bash
   cp .env.example .env   # completa N8N_HOST, WEBHOOK_URL, etc.
   docker compose up -d
   ```
3. Abrir el editor en el navegador (puerto por defecto `5679`).
4. Importar `workflows/workflow.json` y probar con `curl`.

> Pasos completos para Docker en [`docs/DOCKER.md`](docs/DOCKER.md). Cómo probar el flujo y las capturas en [`docs/PRUEBAS.md`](docs/PRUEBAS.md).

---

## 📚 Documentación

| Documento | Contenido |
|---|---|
| [`README.md`](README.md) | **Documento principal** — descripción, stack, guía rápida y estructura |
| [`docs/WORKFLOW.md`](docs/WORKFLOW.md) | Diseño de los nodos del flujo n8n |
| [`docs/DOCKER.md`](docs/DOCKER.md) | Instalación y configuración local de Docker |
| [`docs/PRUEBAS.md`](docs/PRUEBAS.md) | Pruebas ejecutadas, capturas de cada integrante y datos de prueba |
| [`docs/INCIDENCIAS.md`](docs/INCIDENCIAS.md) | **Matriz de errores y soluciones** |
| [`docs/PLAN_COLABORACION.md`](docs/PLAN_COLABORACION.md) | Plan de ramas, commits y Pull Requests del equipo |

---

## 🗂️ Estructura del repositorio

```
.
├── README.md              # Documentación principal (raíz)
├── docs/                  # Documentación técnica y de proceso
│   ├── WORKFLOW.md        # Diseño del flujo n8n
│   ├── DOCKER.md          # Guía de instalación Docker
│   ├── PRUEBAS.md         # Pruebas y capturas
│   ├── INCIDENCIAS.md     # Matriz de errores y soluciones
│   └── PLAN_COLABORACION.md  # Plan de estrategia GitHub Flow
├── docker-compose.yml     # Servicio n8n con persistencia
├── .env.example           # Plantilla de variables de entorno
├── .gitignore             # Excluye .env, volúmenes y backups
└── workflows/
    └── workflow.json      # Flujo n8n exportado (4 nodos)
```

---

## 👥 Roles del equipo

| Integrante | Componente | Rama |
|---|---|---|
| **Rodrigo** | Infraestructura Docker (`docker-compose.yml`, `.gitignore`, instalación) | `feature/carlos-rodrigo` |
| **Marcio** | Diseño del workflow n8n (JSON del flujo en etapas) | `feature/marcio4lex` |
| **Darwin** | Pruebas y documentación (README, capturas, matriz de incidencias) | `feature/darwin` |
| **Gabriel** | Administración del repo, revisión/merge de PRs y apoyo en Docker y documentación | `feature/gabriel-alexander` |

---

## 📸 Evidencias de trabajo en equipo

- Aprobación de Pull Requests entre compañeros (mínimo 2 por integrante).
- Capturas de cada integrante trabajando en [`docs/PRUEBAS.md`](docs/PRUEBAS.md).
- Matriz de errores resueltos en [`docs/INCIDENCIAS.md`](docs/INCIDENCIAS.md).

---

## ⚖️ Nota sobre la autenticación

n8n **no guarda las credenciales dentro del JSON** del flujo por seguridad. Para ejecutar la notificación por Gmail, cada integrante debe autorizar su propia cuenta OAuth2 en el nodo (pasos completos en [`docs/WORKFLOW.md`](docs/WORKFLOW.md)).
