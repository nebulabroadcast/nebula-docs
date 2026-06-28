# Docker-Based Installation

This guide walks you through deploying a local or production-ready instance of Nebula using Docker and Docker Compose. 

By containerizing Nebula, you ensure a reproducible environment where services can scale horizontally.

---

## Architecture Overview

The Nebula system is composed of several Dockerized components that communicate over a shared network

---

## Directory Layout

Before starting the containers, prepare a project folder layout based on the example in
https://github.com/nebulabroadcast/nebula-tutorial repository

```text
nebula/
├── docker-compose.yml
├── plugins/
│    ├── powerpack/
│    └── dramatica/
├── settings/
│   ├── settings.py
│   ├── storages.py
│   ├── services.py
│   └── folders.py
└── storage/
```

---

## Docker Compose Configuration

Create a `docker-compose.yml` file with the following services. This matches the playout OSC settings and local/shared storage mounting criteria.

```yaml

volumes:
  db: {}

services:
  postgres:
    image: postgres:18
    container_name: nebula-postgres
    restart: unless-stopped
    environment:
      POSTGRES_DB: nebula
      POSTGRES_USER: nebula
      POSTGRES_PASSWORD: nebula
    volumes:
      - db:/var/lib/postgresql

  redis:
    image: redis:latest
    container_name: nebula-redis
    restart: unless-stopped

  backend:
    image: nebulabroadcast/nebula:latest
    container_name: nebula-backend
    restart: unless-stopped
    ports:
      - "4455:80"
    volumes:
      - ./settings:/app/settings
      - ./storage:/mnt/nebula_01
    depends_on:
      - postgres
      - redis

  worker:
    image: nebulabroadcast/nebula-worker:latest
    container_name: worker
    restart: unless-stopped
    privileged: true # Grants system admin capacity to handle external storage mounts
    cap_add:
      - SYS_ADMIN
      - DAC_READ_SEARCH
    ports:
      # Expose UDP ports for CasparCG playout controller OSC feedback
      - "6251:6251/udp"
      - "6252:6252/udp"
    volumes:
      - ./storage:/mnt/nebula_01
    depends_on:
      - postgres
      - redis
```
