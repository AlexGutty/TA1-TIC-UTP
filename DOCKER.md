# n8n en Docker

Setup para levantar una instancia propia de [n8n](https://n8n.io) usando Docker, con persistencia de datos [cite: 2].

## Antes de empezar

Para que esto funcione necesitas tener instalado en el servidor o máquina donde vas a correrlo [cite: 1]:

- **Docker** y **Docker Compose** (`docker --version` y `docker compose version` deben responder sin error; si no los tienes, sigue la sección de instalación más abajo) [cite: 1].
- **Git**, para clonar el repositorio [cite: 1].
- Acceso SSH configurado con GitHub, si el repo es privado [cite: 1].

Con eso listo, el resto son los comandos de abajo [cite: 1].

## Instalar Docker (Linux / Ubuntu)

Si el servidor no tiene Docker todavía [cite: 1]:

```bash
sudo apt-get update
sudo apt-get install -y ca-certificates curl gnupg

sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg

echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt-get update
sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-compose-plugin
```

Dejar el servicio activo tras cada reinicio del servidor [cite: 1]:

```bash
sudo systemctl enable --now docker
```

Verificar que quedó instalado [cite: 1]:

```bash
docker --version
docker compose version
```

## Qué hay en esta rama

```text
.
├── docker-compose.yml   # Servicio de n8n con volumen de persistencia
├── .env.example         # Plantilla de variables de entorno
└── .gitignore           # Excluye .env, volúmenes y backups del repo
```
[cite: 1, 2]

## Paso a paso: qué hacer para dejarlo funcionando

Clonar el repo y entrar a la carpeta del proyecto [cite: 1, 2]:

```bash
git clone git@github.com:AlexGutty/TA1-TIC-UTP.git
cd TA1-TIC-UTP
```

Cambiar a la rama de trabajo [cite: 1]:

```bash
git checkout feature/carlos-rodrigo
```

Copiar la plantilla de variables de entorno y completar los valores propios [cite: 1, 2]:

```bash
cp .env.example .env
nano .env
```

Como mínimo hay que definir [cite: 1]:
- `N8N_HOST` — la IP o dominio desde donde se va a acceder (en este proyecto, `n8n.scca.site`) [cite: 1].
- `N8N_HOST_PORT` — el puerto externo (si el 5679 por defecto ya está ocupado, se puede cambiar aquí sin tocar ningún otro archivo) [cite: 1].
- `WEBHOOK_URL` — debe coincidir con `N8N_HOST` y el puerto elegido, por ejemplo `https://n8n.scca.site/` [cite: 1].
- `N8N_ENCRYPTION_KEY` — una clave propia y aleatoria (una vez generada, no se debe cambiar sin perder acceso a las credenciales guardadas) [cite: 1].

El usuario y contraseña de acceso no se configuran aquí — n8n los pide directamente en el navegador la primera vez que se entra [cite: 1, 2].

Levantar el contenedor en segundo plano [cite: 1, 2]:

```bash
docker compose up -d
```

Confirmar que el contenedor quedó corriendo [cite: 1]:

```bash
docker ps
```
Debe aparecer `n8n` con estado `Up` [cite: 1].

Revisar los logs para verificar que arrancó sin errores [cite: 1, 2]:

```bash
docker compose logs -f n8n
```
Ahí se busca una línea del tipo `Editor is now accessible via: ...` [cite: 1]. `Ctrl+C` cierra la vista de logs sin detener el contenedor [cite: 1].

Probar que responde localmente, desde el propio servidor [cite: 1]:

```bash
curl -I http://localhost:5679
```

Una respuesta `200 OK` confirma que el servicio está arriba [cite: 1]. El paso final es abrir `https://n8n.scca.site` en el navegador, donde n8n pide crear la cuenta owner la primera vez [cite: 1, 2].

Para reiniciar el contenedor después de cambiar algo en el `.env` [cite: 1]:

```bash
docker compose down
docker compose up -d
```

## Sobre el puerto

Por defecto n8n corre internamente en el `5678`, pero ese puerto suele estar ocupado por otros servicios en un servidor compartido [cite: 1, 2]. Por eso el `docker-compose.yml` separa el puerto interno del externo [cite: 1, 2]:

```yaml
ports:
  - "${N8N_HOST_PORT:-5679}:5678"
```
[cite: 1, 2]

Cambiar el puerto de acceso es tan simple como editar `N8N_HOST_PORT` en el `.env` — no hay que tocar el compose [cite: 1, 2].

## Dos variables que sí importan de verdad

`N8N_HOST` y `WEBHOOK_URL` definen la dirección real desde la que se accede a la instancia [cite: 1, 2]. Si quedan mal configuradas, los webhooks de los workflows apuntan a `localhost` en vez de al dominio o IP pública, y dejan de funcionar apenas alguien externo intenta llamarlos [cite: 1, 2].

`N8N_ENCRYPTION_KEY` cifra las credenciales guardadas dentro de n8n (tokens, contraseñas de otras APIs) [cite: 1, 2]. Perder ese valor significa perder acceso a todo lo que esté guardado ahí — conviene generarlo una sola vez y no volver a tocarlo [cite: 1, 2].

## Nginx como proxy inverso

n8n queda expuesto en el puerto interno definido por `N8N_HOST_PORT`, pero en el servidor se accede a través de Nginx, que recibe las peticiones en el puerto 80/443 y las redirige hacia ese puerto interno [cite: 1]. Esto permite usar un dominio propio en vez de `ip:puerto`, y deja la puerta abierta para agregar HTTPS más adelante sin tocar el contenedor de n8n [cite: 1].

Una configuración base se ve así [cite: 1]:

```nginx
server {
    listen 80;
    server_name n8n.scca.site;

    location / {
        proxy_pass http://localhost:5679;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

Con Nginx corriendo, ya no es necesario dejar el puerto de n8n abierto al público — solo Nginx necesita alcanzarlo, y lo hace internamente por `localhost` [cite: 1]. Una vez activo el certificado SSL (Let's Encrypt vía Certbot), la instancia queda disponible en `https://n8n.scca.site` [cite: 1].
