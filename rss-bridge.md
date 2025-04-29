# RSS-Bridge

Генератор RSS-лент для сайтов и сервисов, которые не предоставляют RSS-подписки.

<img src="https://github.com/avenom/synology-docker-compose/blob/main/images/rss-bridge.png">

1. Создайте в File Station следующую структуру папок:

```
/docker/rss-bridge/
```

2. Создайте в Container Manager новый проект с названием rss-bridge, выберите путь /docker/rss-bridge/, выберите в источнике "Создать docker-compose.yml", вставьте в окно ниже следующий код:

```
services:
  rss-bridge:
    image: rssbridge/rss-bridge:latest
    container_name: rss-bridge
    volumes:
      - ./:/config
    ports:
      - 8086:80
    restart: always
```

3. RSS-Bridge будет доступен по адресу:

```
http://synologyip:8086
```