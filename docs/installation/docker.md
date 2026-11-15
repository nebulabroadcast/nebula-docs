# Docker-Based Installation

This guide deploys Nebula with Docker Compose. The
[nebula-tutorial](https://github.com/nebulabroadcast/nebula-tutorial) repository
is a complete, working example of this setup: clone it and follow its README
for a step-by-step walkthrough.

---

## Architecture Overview

A Docker deployment consists of four containers:

| Service | Image | Role |
| --- | --- | --- |
| `postgres` | `postgres:18` | Database: assets, events, settings, users, jobs |
| `redis` | `redis:alpine` | Cache, sessions and the real-time message bus |
| `server` | `nebulabroadcast/nebula-server` | Web interface and API (port 80 in the container) |
| `worker` | `nebulabroadcast/nebula-worker` | Runs the services: watch folders, metadata, transcoding, playout control |

More workers can run on other machines; each one starts the services assigned
to its hostname (see [Configuring Nebula](../setup/configuring_nebula.md)).

---

## Directory Layout

```text
nebula-tutorial/
├── docker-compose.yml
├── Makefile           # shortcuts: make setup, make reload, make update...
├── plugins/           # server and CLI plugins, mounted at /plugins
├── settings/          # settings templates, mounted at /settings
│   ├── actions.py
│   ├── channels.py
│   └── services.py
└── storage/           # media storage 1, mounted at /mnt/nebula_01
```

---

## Docker Compose Configuration

```yaml
volumes:
  db: {}

services:
  postgres:
    image: postgres:18
    environment:
      - "POSTGRES_USER=nebula"
      - "POSTGRES_PASSWORD=nebula"
      - "POSTGRES_DB=nebula"
    volumes:
      - "/etc/localtime:/etc/localtime:ro"
      - "db:/var/lib/postgresql"
    restart: unless-stopped

  redis:
    image: redis:alpine
    restart: unless-stopped

  server:
    image: nebulabroadcast/nebula-server:latest
    ports:
      - "4455:80"
    volumes:
      - "/etc/localtime:/etc/localtime:ro"
      - "./plugins:/plugins"
      - "./settings:/settings"
      - "./storage:/mnt/nebula_01"
    depends_on:
      - redis
      - postgres

  worker:
    image: nebulabroadcast/nebula-worker:latest
    hostname: worker
    volumes:
      - "/etc/localtime:/etc/localtime:ro"
      - "./storage:/mnt/nebula_01"
    privileged: true
    depends_on:
      - server
```

* The worker's `hostname` matters: services are assigned to workers by
  hostname (`host="worker"` in `services.py`).
* `privileged: true` lets the worker mount network storages itself
  (see [Storages](../setup/storages.md)). A worker that only uses local
  storages doesn't need it.
* A worker controlling CasparCG also needs its OSC ports published
  (see [CasparCG Playout](../setup/casparcg.md)).

---

## First Start

```bash
docker compose up -d
make setup
```

`make setup` creates the database schema and applies the settings templates
(see [Configuring Nebula](../setup/configuring_nebula.md)). The interface is
then available at `http://<host>:4455`.

On an empty database, setup creates the user `admin` with the password
`nebula`: change it right away (`make password`, a CLI plugin from the
tutorial's `plugins/cli`). More users are created in the web interface, or
with the built-in [`adduser`](../setup/cli.md#adduser) command.

## Updating

`make update` pulls the latest images and recreates the server and worker
containers. Release notes are on
[GitHub](https://github.com/nebulabroadcast/nebula/releases).
