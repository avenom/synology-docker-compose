# Miniflux + PostgreSQL

[Miniflux](https://github.com/miniflux/v2) — это минималистичный RSS-ридер для размещения на собственном сервере. Он поддерживает форматы RSS, Atom и JSON Feed, обеспечивает защиту от отслеживания, интеграцию с множеством сторонних сервисов и предоставляет удобный интерфейс с возможностью настройки внешнего вида и поведения.

---

1. Создайте в File Station следующую структуру папок:

```
/docker/miniflux/db/
```

2. Создайте в Container Manager новый проект с названием miniflux, выберите путь /docker/miniflux/, выберите в источнике "Создать docker-compose.yml", вставьте в окно ниже следующий код:

```
services:
  miniflux:
    image: miniflux/miniflux:latest
    container_name: miniflux
    ports:
      - "8085:8080"
    depends_on:
      db:
        condition: service_healthy
    environment:
      - DATABASE_URL=postgres://miniflux:minifluxpassword@db/miniflux?sslmode=disable
      - RUN_MIGRATIONS=1
      - CREATE_ADMIN=1
      - ADMIN_USERNAME=admin
      - ADMIN_PASSWORD=password
    restart: always
  db:
    image: postgres:latest
    container_name: miniflux-db
    environment:
      - POSTGRES_USER=miniflux
      - POSTGRES_PASSWORD=minifluxpassword
      - POSTGRES_DB=miniflux
    volumes:
      - ./db:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD", "pg_isready", "-U", "miniflux"]
      interval: 10s
      start_period: 30s
    restart: always
```

3. Miniflux будет доступен по адресу:

```
http://synologyip:8085
```

Логин:
```
admin
```

Пароль:
```
password
```

Рекомендую использовать совместно с [RSS-Bridge](https://github.com/avenom/synology-docker-compose/blob/main/rss-bridge.md).
