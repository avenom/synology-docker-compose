# Tautulli

[Tautulli](https://github.com/Tautulli/Tautulli) — это веб-приложение для мониторинга, аналитики и уведомлений Plex Media Server, предоставляющее подробную статистику о просмотрах, активности пользователей и недавно добавленном контенте. 

---

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

* [Как узнать ваш PUID и PGID в Synology DSM](https://github.com/avenom/synology-docker-compose#puid)

3. Tautulli будет доступен по адресу:

```
http://synologyip:8181
```
