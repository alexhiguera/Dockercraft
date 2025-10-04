# n8n

## ¿Qué hace este Docker Compose?

- Levanta dos servicios principales:
  - `n8n`: app principal.
  - `n8n-postgres`: base de datos.
- Crea dos volúmenes:
  - `n8n-data`
  - `n8n-postgres-data`
- Conecta ambos contenedores a la red `example-network` (esta red se espera que exista como externa).
- Incluye etiquetas (labels) opcionales para integrar con Traefik como reverse proxy y TLS automático.

## Variables de entorno (.env)

Coloca estas variables en un archivo `.env` en el mismo directorio (`n8n/.env`) o en la raíz del proyecto si tu `docker-compose` referencia ese `.env`.

Ejemplo de `.env`:

```
# N8N app
N8N_HOST=n8n.example.com
N8N_PORT=5678
N8N_PROTOCOL=https
WEBHOOK_URL=https://n8n.example.com/
GENERIC_TIMEZONE=Europe/Madrid

# Seguridad / Auth
N8N_BASIC_AUTH_ACTIVE=true
N8N_BASIC_AUTH_USER=usuario
N8N_BASIC_AUTH_PASSWORD=contraseña_segura
N8N_SECURE_COOKIE=true

# Base de datos PostgreSQL para n8n
N8N_DB_NAME=n8n
N8N_DB_USER=n8n
N8N_DB_PASSWORD=una_contraseña_segura_de_bd
```

Descripción de las variables:

- N8N_HOST: Nombre de host público donde estará disponible n8n. Usado en etiquetas de Traefik y en la regla de router.
- N8N_PORT: Puerto interno/externo mapeado por Docker (por defecto 5678 en `docker-compose.yml`).
- N8N_PROTOCOL: `http` o `https`. Si usas Traefik en modo `websecure` normalmente será `https`.
- WEBHOOK_URL: URL pública base para webhooks (importantísimo si usas integraciones externas).
- GENERIC_TIMEZONE: Zona horaria que n8n usará para timestamps.

- N8N_BASIC_AUTH_ACTIVE: `true`/`false`. Activa autenticación básica HTTP para proteger la UI.
- N8N_BASIC_AUTH_USER / N8N_BASIC_AUTH_PASSWORD: Credenciales para la autenticación básica.
- N8N_SECURE_COOKIE: `true`/`false`. Indica si las cookies son seguras (recomendado `true` en producción con HTTPS).

- N8N_DB_NAME / N8N_DB_USER / N8N_DB_PASSWORD: Credenciales y nombre de la base de datos PostgreSQL que el contenedor `n8n-postgres` creará y que n8n usará.

Notas importantes sobre seguridad:

- Nunca expongas credenciales en repositorios públicos. Usa un archivo `.env` que esté en `.gitignore` o un secreto manager.
- Si activas Traefik/HTTPS, asegúrate que `N8N_HOST` y `WEBHOOK_URL` estén configurados con la URL pública y que el certificado se renueve correctamente.
- Si usas `N8N_BASIC_AUTH_ACTIVE=true`, mantén credenciales robustas.

## Integración con Traefik

El `docker-compose.yml` incluye labels para Traefik bajo el servicio `n8n`:

- `traefik.http.routers.n8n.rule=Host(`${N8N_HOST}`)` — redirige solicitudes al host definido.
- `traefik.http.routers.n8n.entrypoints=websecure` — usa el entrypoint TLS.
- `traefik.http.routers.n8n.tls.certresolver=letsencrypt` — configura el resolver para certificados automáticos.

Si NO usas Traefik, puedes eliminar o comentar las labels y exponer el puerto directamente (ya está mapeado `5678:5678`).

## Cómo ejecutar

1. Crear (o editar) `n8n/.env` con las variables arriba descritas.
2. Asegúrate de que la red externa `example-network` exista. Si no, créala:

```bash
docker network create example-network
```

3. Levanta los servicios desde el directorio `n8n`:

```bash
cd n8n
docker compose up -d
```

4. Verifica que PostgreSQL esté saludable y que n8n haya iniciado:

```bash
docker compose ps
docker compose logs -f n8n
```

## Healthcheck y dependencias

El servicio `n8n` depende de `n8n-postgres` y en el `docker-compose.yml` está configurado para esperar a que el servicio de PostgreSQL esté `healthy` (usando `pg_isready`).

## Troubleshooting (rápido)

- Si n8n no arranca, revisa `docker compose logs n8n` y `docker compose logs n8n-postgres`.
- Si ves errores de conexión a la DB, confirma que las variables `N8N_DB_*` en `.env` coincidan con las del contenedor `n8n-postgres` (user/password/db) y que el servicio PostgreSQL esté healthy.
- Problemas con Traefik: revisa que la red `example-network` sea la misma que usa Traefik y que `N8N_HOST` apunte al dominio correcto.