# Dockercraft 🚢🧮

Colección completa de configuraciones Docker Compose para aplicaciones y servicios populares. Cada servicio incluye su propia configuración, variables de entorno y documentación de uso.

## 📋 Tabla de Contenidos

- [Requisitos Previos](#requisitos-previos)
- [Servicios Disponibles](#servicios-disponibles)
  - [Next.js](#nextjs)
  - [PostgreSQL](#postgresql)
  - [Traefik](#traefik)
  - [N8N](#n8n)
  - [Ollama (LLM)](#ollama-llm)
  - [Redis](#redis)
  - [MongoDB](#mongodb)
  - [Nginx](#nginx)
  - [Portainer](#portainer)
  - [Watchtower](#watchtower)
- [Uso General](#uso-general)
- [Contribuir](#contribuir)
- [Licencia](#licencia)

## 🔧 Requisitos Previos

Antes de usar cualquiera de estos servicios, asegúrate de tener instalado:

- **Docker**: Versión 20.10 o superior
- **Docker Compose**: Versión 2.0 o superior

### Instalación de Docker

**Ubuntu/Debian:**
```bash
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
sudo usermod -aG docker $USER
```

**macOS:**
Descarga e instala [Docker Desktop](https://www.docker.com/products/docker-desktop)

**Windows:**
Descarga e instala [Docker Desktop](https://www.docker.com/products/docker-desktop)

## 📦 Servicios Disponibles

### Next.js

Configuración para aplicaciones Next.js con Node.js 18.

**Ubicación:** `nextjs/`

**Características:**
- Node.js 18 Alpine
- Hot reload en desarrollo
- Build optimizado para producción
- Gestión de dependencias automática

**Instalación:**

```bash
cd nextjs
cp .env.example .env
# Edita .env con tus configuraciones
docker-compose up -d
```

**Acceso:**
- Aplicación: http://localhost:3000

**Configuración:**
- Coloca tu aplicación Next.js en el directorio `nextjs/app/`
- Las variables de entorno se configuran en el archivo `.env`

---

### PostgreSQL

Base de datos PostgreSQL 16 con pgAdmin para administración web.

**Ubicación:** `postgresql/`

**Características:**
- PostgreSQL 16 Alpine
- pgAdmin 4 para gestión web
- Volúmenes persistentes
- Scripts de inicialización personalizados
- Health checks configurados

**Instalación:**

```bash
cd postgresql
cp .env.example .env
# Edita .env con tus credenciales
docker-compose up -d
```

**Acceso:**
- PostgreSQL: localhost:5432
- pgAdmin: http://localhost:5050

**Credenciales por defecto:**
- Usuario PostgreSQL: `postgres`
- Contraseña PostgreSQL: `changeme`
- Email pgAdmin: `admin@admin.com`
- Contraseña pgAdmin: `admin`

**Scripts de inicialización:**
- Coloca tus scripts SQL en `postgresql/init-scripts/`
- Se ejecutarán automáticamente al crear la base de datos

---

### Traefik

Proxy reverso y balanceador de carga con soporte para HTTPS automático.

**Ubicación:** `traefik/`

**Características:**
- Traefik v2.10
- Dashboard de administración
- SSL/TLS automático con Let's Encrypt
- Descubrimiento automático de servicios Docker
- Servicio de ejemplo (whoami) incluido

**Instalación:**

```bash
cd traefik
cp .env.example .env
# Configura tu email para Let's Encrypt
docker-compose up -d
```

**Acceso:**
- Dashboard: http://localhost:8080
- HTTP: puerto 80
- HTTPS: puerto 443
- Servicio de prueba: http://whoami.localhost

**Configuración de servicios:**

Para exponer un servicio a través de Traefik, añade estas etiquetas:

```yaml
labels:
  - "traefik.enable=true"
  - "traefik.http.routers.myapp.rule=Host(`myapp.example.com`)"
  - "traefik.http.routers.myapp.entrypoints=websecure"
  - "traefik.http.routers.myapp.tls.certresolver=myresolver"
```

---

### N8N

Plataforma de automatización de flujos de trabajo (alternativa a Zapier).

**Ubicación:** `n8n/`

**Características:**
- N8N última versión
- PostgreSQL incluido para persistencia
- Autenticación básica configurada
- Timezone configurable
- Webhooks habilitados

**Instalación:**

```bash
cd n8n
cp .env.example .env
# Configura usuario y contraseña
docker-compose up -d
```

**Acceso:**
- N8N: http://localhost:5678

**Credenciales por defecto:**
- Usuario: `admin`
- Contraseña: `changeme`

**Configuración de webhooks:**
- La URL base para webhooks es: `http://localhost:5678/`
- Configurable mediante `WEBHOOK_URL` en `.env`

---

### Ollama (LLM)

Servidor de modelos de lenguaje local (LLMs) con interfaz web.

**Ubicación:** `llm-ollama/`

**Características:**
- Ollama server para ejecutar LLMs localmente
- Open WebUI para interacción web
- Soporte para múltiples modelos (Llama 2, Mistral, CodeLlama, Phi, etc.)
- Sin límites de uso
- Privacidad total (todo local)

**Instalación:**

```bash
cd llm-ollama
cp .env.example .env
docker-compose up -d
```

**Acceso:**
- Open WebUI: http://localhost:3001
- Ollama API: http://localhost:11434

**Descargar modelos:**

```bash
# Llama 2 (7B)
docker exec -it ollama ollama pull llama2

# Mistral (7B)
docker exec -it ollama ollama pull mistral

# CodeLlama (para código)
docker exec -it ollama ollama pull codellama

# Phi (modelo pequeño y rápido)
docker exec -it ollama ollama pull phi
```

**Listar modelos instalados:**
```bash
docker exec -it ollama ollama list
```

**Usar desde CLI:**
```bash
docker exec -it ollama ollama run llama2
```

---

### Redis

Cache en memoria y base de datos con interfaz web de administración.

**Ubicación:** `redis/`

**Características:**
- Redis 7 Alpine
- Redis Commander para gestión web
- Persistencia de datos
- Autenticación con contraseña
- Health checks

**Instalación:**

```bash
cd redis
cp .env.example .env
# Configura contraseña segura
docker-compose up -d
```

**Acceso:**
- Redis: localhost:6379
- Redis Commander: http://localhost:8081

**Contraseña por defecto:**
- Password: `redis`

**Conectar desde aplicación:**
```bash
redis-cli -h localhost -p 6379 -a redis
```

---

### MongoDB

Base de datos NoSQL orientada a documentos con interfaz web.

**Ubicación:** `mongodb/`

**Características:**
- MongoDB 7
- Mongo Express para administración web
- Volúmenes persistentes
- Autenticación configurada
- Health checks

**Instalación:**

```bash
cd mongodb
cp .env.example .env
# Configura credenciales
docker-compose up -d
```

**Acceso:**
- MongoDB: localhost:27017
- Mongo Express: http://localhost:8082

**Credenciales por defecto:**
- Usuario root: `root`
- Contraseña root: `changeme`
- Usuario Mongo Express: `admin`
- Contraseña Mongo Express: `admin`

**String de conexión:**
```
mongodb://root:changeme@localhost:27017/mydb?authSource=admin
```

---

### Nginx

Servidor web y proxy reverso.

**Ubicación:** `nginx/`

**Características:**
- Nginx Alpine (ligero)
- Configuración personalizable
- Logs persistentes
- Soporte HTTP/HTTPS

**Instalación:**

```bash
cd nginx
cp .env.example .env
docker-compose up -d
```

**Acceso:**
- HTTP: http://localhost:8080
- HTTPS: https://localhost:8443

**Estructura de archivos:**
- `nginx/html/` - Archivos estáticos
- `nginx/nginx.conf` - Configuración principal
- `nginx/conf.d/` - Configuraciones adicionales

**Ejemplo de configuración básica:**

Crea `nginx/conf.d/default.conf`:
```nginx
server {
    listen 80;
    server_name localhost;
    
    location / {
        root /usr/share/nginx/html;
        index index.html;
    }
}
```

---

### Portainer

Interfaz de gestión para Docker.

**Ubicación:** `portainer/`

**Características:**
- Portainer CE (Community Edition)
- Gestión completa de Docker
- Dashboard visual
- Gestión de contenedores, imágenes, volúmenes y redes
- Editor de stacks
- Consola de contenedores

**Instalación:**

```bash
cd portainer
cp .env.example .env
docker-compose up -d
```

**Acceso:**
- HTTP: http://localhost:9000
- HTTPS: https://localhost:9443

**Primera vez:**
- Crear usuario administrador al primer acceso
- Seleccionar "Local" para gestionar el Docker local

---

### Watchtower

Actualizador automático de contenedores Docker.

**Ubicación:** `watchtower/`

**Características:**
- Actualización automática de imágenes
- Limpieza de imágenes antiguas
- Notificaciones opcionales
- Programación configurable
- Monitoreo de contenedores en ejecución

**Instalación:**

```bash
cd watchtower
cp .env.example .env
# Configura el intervalo de actualización
docker-compose up -d
```

**Configuración:**
- `WATCHTOWER_CLEANUP`: Limpia imágenes antiguas (true/false)
- `WATCHTOWER_POLL_INTERVAL`: Segundos entre verificaciones (por defecto 24h)
- `WATCHTOWER_INCLUDE_STOPPED`: Incluir contenedores detenidos
- `WATCHTOWER_NOTIFICATION_URL`: URL para notificaciones (Slack, etc.)

**Monitoreo:**
```bash
docker logs -f watchtower
```

---

## 🚀 Uso General

### Comandos básicos de Docker Compose

**Iniciar servicios:**
```bash
docker-compose up -d
```

**Detener servicios:**
```bash
docker-compose down
```

**Ver logs:**
```bash
docker-compose logs -f
```

**Ver logs de un servicio específico:**
```bash
docker-compose logs -f nombre-servicio
```

**Reiniciar servicios:**
```bash
docker-compose restart
```

**Ver estado de servicios:**
```bash
docker-compose ps
```

**Eliminar todo (contenedores, redes, volúmenes):**
```bash
docker-compose down -v
```

**Reconstruir imágenes:**
```bash
docker-compose build --no-cache
docker-compose up -d
```

### Buenas prácticas

1. **Siempre copia `.env.example` a `.env`** antes de iniciar un servicio
2. **Cambia las contraseñas por defecto** en producción
3. **Usa volúmenes** para persistir datos importantes
4. **Revisa los logs** regularmente: `docker-compose logs -f`
5. **Mantén las imágenes actualizadas**: Usa Watchtower o actualiza manualmente
6. **Configura backups** para volúmenes de bases de datos
7. **Usa redes Docker** para aislar servicios
8. **Limita recursos** si es necesario con `deploy.resources`

### Gestión de volúmenes

**Listar volúmenes:**
```bash
docker volume ls
```

**Backup de volumen:**
```bash
docker run --rm -v nombre-volumen:/data -v $(pwd):/backup alpine tar czf /backup/backup.tar.gz -C /data .
```

**Restaurar volumen:**
```bash
docker run --rm -v nombre-volumen:/data -v $(pwd):/backup alpine tar xzf /backup/backup.tar.gz -C /data
```

### Solución de problemas

**Contenedor no inicia:**
```bash
docker-compose logs nombre-servicio
```

**Puerto ya en uso:**
```bash
# Cambiar el puerto en .env o docker-compose.yml
# Ejemplo: "8080:80" cambia el puerto host
```

**Problemas de permisos:**
```bash
sudo chown -R $USER:$USER ./directorio
```

**Limpiar sistema Docker:**
```bash
# Precaución: Elimina todos los contenedores, imágenes y volúmenes no usados
docker system prune -a --volumes
```

**Reiniciar Docker:**
```bash
sudo systemctl restart docker
```

---

## 🤝 Contribuir

¿Quieres agregar un nuevo servicio o mejorar uno existente?

1. Fork este repositorio
2. Crea una rama para tu feature: `git checkout -b feature/nuevo-servicio`
3. Agrega tu servicio en un nuevo directorio
4. Incluye `docker-compose.yml` y `.env.example`
5. Actualiza este README con la documentación
6. Commit tus cambios: `git commit -am 'Agrega servicio X'`
7. Push a la rama: `git push origin feature/nuevo-servicio`
8. Crea un Pull Request

### Estructura para nuevos servicios

```
nombre-servicio/
├── docker-compose.yml
├── .env.example
└── README.md (opcional, para configuración compleja)
```

---

## 📄 Licencia

Este proyecto está bajo la Licencia GNU General Public License v3.0 - ver el archivo [LICENSE](LICENSE) para más detalles.

---

## 🎯 Servicios Futuros

Estamos considerando agregar:
- ✅ MySQL/MariaDB
- ✅ Elasticsearch + Kibana
- ✅ RabbitMQ
- ✅ GitLab Runner
- ✅ Jenkins
- ✅ Grafana + Prometheus
- ✅ MinIO (S3 compatible)
- ✅ Keycloak (Authentication)

---

## 📧 Soporte

Si tienes preguntas o problemas:
- Abre un [Issue](https://github.com/alexhiguera/Dockercraft/issues)
- Revisa la documentación oficial de cada servicio
- Consulta los logs: `docker-compose logs -f`

---

**Desarrollado con ❤️ para la comunidad**
