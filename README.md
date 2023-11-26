# synology-docker-compose
Synology Docker Compose

## Glances

1. Создайте в File Station следующую структуру папок:

```
/docker/glances/
```

2. Создайте в Container Manager новый проект с названием glances, выберите путь /docker/glances/, выберите в источнике "Создать docker-compose.yml", вставьте в окно ниже следующий код:

```
version: '2'
services:
  glances:
    image: nicolargo/glances:latest
    container_name: glances
    pid: host
    network_mode: host
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
    environment:
      - "GLANCES_OPT=-w"
    ports:
      - 61208:61208
    restart: always
```

## Homer + Homer Theme

1. Создайте в File Station следующую структуру папок:

```
/docker/homer/assets
/docker/homer/www/assets
```

2. Создайте в Container Manager новый проект с названием homer, выберите путь /docker/homer/, выберите в источнике "Создать docker-compose.yml", вставьте в окно ниже следующий код:

```
version: '2'
services:
  homer:
    image: b4bz/homer:latest
    container_name: homer
    volumes:
      - /volume1/docker/homer/assets/:/www/assets
    ports:
      - 8080:8080
    restart: always
```

## OpenSpeedTest

1. Создайте в File Station следующую структуру папок:

```
/docker/homer/assets
/docker/homer/www/assets
```

2. Создайте в Container Manager новый проект с названием homer, выберите папку /docker/homer/, вставьте следующий код:


```
version: '3.3'
services:
  openspeedtest:
    image: openspeedtest/latest:latest
    container_name: openspeedtest
    ports:
      - 3000:3000
    restart: always
```

## PlexTraktSync

1. Создайте в File Station следующую структуру папок:

```
/docker/homer/assets
/docker/homer/www/assets
```

2. Создайте в Container Manager новый проект с названием homer, выберите папку /docker/homer/, вставьте следующий код:

```
version: '2'
services:
  plextraktsync:
    image: twolaw/plextraktsync:latest
    container_name: plextraktsync
    command: sync
    volumes:
      - ./config:/app/config
```

## Podfetch

1. Создайте в File Station следующую структуру папок:

```
/docker/homer/assets
/docker/homer/www/assets
```

2. Создайте в Container Manager новый проект с названием homer, выберите папку /docker/homer/, вставьте следующий код:

```
version: '3'
services:
  podfetch:
    image: samuel19982/podfetch:latest
    container_name: podfetch
    user: 1026:100
    ports:
      - "8000:8000"
    depends_on:
      - postgres
    volumes:
      - /volume1/docker/podfetch:/app/podcasts:rw
    environment:
      - POLLING_INTERVAL=300
      - SERVER_URL=http://localhost:80
      - DATABASE_URL=postgresql://postgres:changeme@postgres/podfetch
    restart: always

  postgres:
    image: postgres:latest
    container_name: podfetch-db
    security_opt:
      - no-new-privileges:true
    healthcheck:
      test: ["CMD", "pg_isready", "-q", "-d", "podfetch", "-U", "postgres"]
    environment:
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: changeme
      POSTGRES_DB: podfetch
    volumes:
      - /volume1/docker/podfetch/db:/var/lib/postgresql/data:rw
    restart: always
```

## RSS-Bridge

1. Создайте в File Station следующую структуру папок:

```
/docker/homer/assets
/docker/homer/www/assets
```

2. Создайте в Container Manager новый проект с названием homer, выберите папку /docker/homer/, вставьте следующий код:

```
version: '2'
services:
  rss-bridge:
    image: rssbridge/rss-bridge:latest
    container_name: rss-bridge
    volumes:
      - /volume1/docker/rss-bridge:/config
    ports:
      - 3001:80
    restart: always
```

## SMTP To Telegram

1. Создайте в File Station следующую структуру папок:

```
/docker/homer/assets
/docker/homer/www/assets
```

2. Создайте в Container Manager новый проект с названием homer, выберите папку /docker/homer/, вставьте следующий код:

```
version: '2'
services:
  smtp_to_telegram:
    image: kostyaesmukov/smtp_to_telegram:latest
    container_name: smtp_to_telegram
    environment:
      - PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
      - ST_SMTP_LISTEN=0.0.0.0:2525
      - ST_TELEGRAM_MESSAGE_TEMPLATE={body}
      - ST_TELEGRAM_BOT_TOKEN=ВАШ ТОКЕН БОТА
      - ST_TELEGRAM_CHAT_IDS=ВАШ CHAT ID БОТА
    ports:
      - 2525:2525
    restart: always
```

## Tautulli

1. Создайте в File Station следующую структуру папок:

```
/docker/homer/assets
/docker/homer/www/assets
```

2. Создайте в Container Manager новый проект с названием homer, выберите папку /docker/homer/, вставьте следующий код:

```
version: '3'
services:
  tautulli:
    image: tautulli/tautulli:latest
    container_name: tautulli
    volumes:
      - /volume1/docker/tautulli/config:/config
    environment:
      - PUID=<uid>
      - PGID=<gid>
      - TZ=<timezone>
    ports:
      - 8181:8181
    restart: always
```
