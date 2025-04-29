# Tautulli

Веб-приложение для мониторинга, аналитики и уведомлений для сервера Plex.

<img src="https://github.com/avenom/synology-docker-compose/blob/main/images/tautulli.jpg">

1. Создайте в File Station следующую структуру папок:

```
/docker/tautulli/config/
```

2. Создайте в Container Manager новый проект с названием tautulli, выберите путь /docker/tautulli/, выберите в источнике "Создать docker-compose.yml", вставьте в окно ниже следующий код:

```
services:
  tautulli:
    image: tautulli/tautulli:latest
    container_name: tautulli
    network_mode: host
    volumes:
      - ./config:/config
    environment:
      - PUID=1026
      - PGID=100
      - TZ=Europe/Moscow
    restart: always
```

3. Tautulli будет доступен по адресу:

```
http://synologyip:8181
```