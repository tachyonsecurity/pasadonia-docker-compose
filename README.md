# Pasadonia — Docker Compose Deployment

This repository contains everything you need to run **Pasadonia** with Docker
Compose. It pulls the official prebuilt images from Docker Hub — no source code
or build step required.

## Prerequisites

- [Docker Engine](https://docs.docker.com/get-docker/) 24+
- [Docker Compose](https://docs.docker.com/compose/) v2 (bundled with recent Docker)
- A host with internet access to pull images from Docker Hub

## Quick start

### 1. Get this repository

```sh
git clone https://github.com/tachyonsecurity/pasadonia-docker-compose.git
cd pasadonia-docker-compose
```

### 2. Create your environment file

```sh
cp example.env .env
```

Open `.env` and replace every `<placeholder>` with a real value — database
password, JWT/crypto secrets, customer-portal URL, etc. **Keep `.env` private;**
it is excluded from git by `.gitignore`.

### 3. Map the local hostname

Pasadonia's auth domain expects `pasadonia.local.com`. Add it to your hosts file:

- **Linux/macOS** — `sudo nano /etc/hosts`
- **Windows** — open `C:\Windows\System32\drivers\etc\hosts` as Administrator

Add:

```
127.0.0.1   pasadonia.local.com
```

> Deploying on a remote server? Point this name at the server's address instead,
> and update the `*BaseURL` / `wsURL` values in `.env` accordingly.

### 4. Start the stack

```sh
# Latest published release
docker compose up -d

# …or pin a specific version (recommended for production)
VERSION=X.Y.Z docker compose up -d
```

### 5. Open the app

- **Frontend:** http://pasadonia.local.com:3000/
- **API docs (Swagger):** http://pasadonia.local.com:9001/api-docs/#/

## Upgrading

```sh
# Pull the new images and recreate the changed services
VERSION=X.Y.Z docker compose pull
VERSION=X.Y.Z docker compose up -d
```

Pin the same `VERSION` for `pull` and `up`. Data persists in named volumes
(`mongo-data`, `postgres_data`, `redis-data`, `captures-data`) across upgrades.

## Stopping

```sh
docker compose down          # stop and remove containers (keeps volumes/data)
docker compose down -v       # also remove volumes (DESTROYS all data)
```

## Notes

- **Elevated network capabilities:** the `app` and `worker` services request
  `NET_RAW` and `NET_ADMIN` for network-scanning and packet-capture features
  (e.g. nmap SYN scans, pcap). These are granted via `cap_add` in the compose
  file and are required for those features to work.
- **Docker Hub rate limits:** anonymous pulls are rate-limited by Docker Hub. If
  you hit a limit, run `docker login` with any Docker Hub account before pulling.
- **Versions:** images are published as `:latest` and as exact `X.Y.Z` tags.
  Pin an explicit version in production so upgrades are deliberate.

### Ports

| Port | Service |
|------|---------|
| 3000 | Frontend |
| 9001–9008 | Backend services (API, WS, jobs, alerts, …) |
| 27018 | MongoDB |
| 5433 | PostgreSQL |
| 3567 | SuperTokens |
| 5672 / 15672 | RabbitMQ (AMQP / management UI) |
| 6379 | Redis |

## Support

For troubleshooting or assistance, please open an issue in this repository or
contact Tachyon Security support.
