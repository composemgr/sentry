## 👋 Welcome to Sentry 🚀

Error tracking and performance monitoring platform

## 📋 Description

Sentry is an error tracking and performance monitoring platform. Applications
report exceptions, crashes and slow transactions to it, and Sentry groups them
into issues with the stack traces, the release they came from and the users
they affect. This stack runs the self-hosted Sentry server behind the host
nginx proxy, with PostgreSQL for the primary data and Redis for the caches and
queues.

The `getsentry/sentry` image runs all server processes in a single container.
That is the supported way to run the all-in-one image, so there is no separate
worker service here.

## 🚀 Services

- **app**: getsentry/sentry:latest

### Infrastructure Components

- **sentry-db**: Postgres database
- **sentry-redis**: Redis cache and queue

## 📦 Installation

### Option 1: Quick Install
```bash
curl -q -LSsf "https://raw.githubusercontent.com/composemgr/sentry/main/docker-compose.yaml" -o compose.yml
```

### Option 2: Git Clone
```bash
git clone "https://github.com/composemgr/sentry" ~/.local/srv/docker/sentry
cd ~/.local/srv/docker/sentry
cp default.env .env
docker compose --env-file .env up -d
```

### Option 3: Using composemgr
```bash
composemgr install sentry
```

## 🔧 Configuration

### Environment Variables

```shell
TZ=America/New_York
DB_CREATE_DATABASE_NAME=sentry
DB_USER_NAME=sentry
DB_USER_PASS=replace-with-a-random-password
APP_SECRET_KEY=replace-with-a-random-secret-min-32-chars
EMAIL_SERVER_HOST=localhost
EMAIL_SERVER_PORT=25
EMAIL_SERVER_MAIL_FROM=noreply@example.com
```

See `docker-compose.yaml` for complete list of configurable options.

### Environment Files

`default.env.sample` holds the full set of variables and is copied to `.env`
before starting the stack:

```bash
cp default.env .env
docker compose --env-file .env up -d
```

`app.env.sample` holds the app-specific overrides and is copied to `app.env`:

```bash
cp app.env.sample app.env
```

`composemgr up` applies `app.env` and `default.env` automatically. Raw
`docker compose` commands need `--env-file .env` to pick up the same values.

### Reverse Proxy

Three standalone compose files ship in this repo:

| File | Use |
|------|-----|
| `docker-compose.yaml` | Default, published on `172.17.0.1:64341` |
| `docker-compose.traefik.yaml` | Behind an existing external `traefik` network |
| `docker-compose.tunnel.yaml` | Behind an existing external `cloudflare` tunnel network |

```bash
docker compose -f docker-compose.traefik.yaml --env-file .env up -d
```

Sentry ingests event payloads that can be large, so raise the request body size
limit in the proxy if uploads are rejected.

## 🌐 Access

- **Web Interface**: http://172.17.0.1:64341

## 📂 Volumes

- `./volumes/data/sentry` - Sentry data directory, uploads and file storage
- `./volumes/db/postgres/sentry` - Database data
- `./volumes/db/redis/sentry` - Redis data

## 🔐 Security

- Set `APP_SECRET_KEY` to a random value of at least 32 characters, changing it
  later signs everyone out and invalidates stored session data
- Set `DB_USER_PASS` to a random password, the database is reachable only on
  the internal project network but is still the crown jewel of the install
- Configure `EMAIL_SERVER_LOGIN_NAME` and `EMAIL_SERVER_LOGIN_PASS`, without a
  mail transport password resets and alert notifications never leave the host
- Use HTTPS via the reverse proxy in production, event payloads and tokens
  travel in clear over plain HTTP
- The container is published only on the dockerhost address `172.17.0.1`
- Regularly update Docker images for security patches
- Backup the Postgres volume and the data volume together

## 🔍 Logging

```shell
docker compose --env-file .env logs -f app
```

## 🛠️ Management

```bash
# Start services
docker compose --env-file .env up -d

# Stop services
docker compose --env-file .env down

# Update to latest images
docker compose --env-file .env pull && docker compose --env-file .env up -d

# View logs
docker compose --env-file .env logs -f

# Restart services
docker compose --env-file .env restart
```

## 📋 Requirements

- Docker Engine 20.10+
- Docker Compose V2+

## 🤝 Author

🤖 casjay: [Github](https://github.com/casjay) 🤖  
🦄 composemgr: [Github](https://github.com/composemgr) 🦄
