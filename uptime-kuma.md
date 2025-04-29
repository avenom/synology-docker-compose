# Uptime Kuma

Мониторинг доступности сайтов, доменов, сервисов и портов.

<img src="https://github.com/avenom/synology-docker-compose/blob/main/images/uptime-kuma.png">

1. Создайте в File Station следующую структуру папок:

```
/docker/uptime-kuma/data/
```

2. Создайте в Container Manager новый проект с названием uptime-kuma, выберите путь /docker/uptime-kuma/, выберите в источнике "Создать docker-compose.yml", вставьте в окно ниже следующий код:

```
services:
  uptime-kuma:
    image: louislam/uptime-kuma:latest
    container_name: uptime-kuma
    volumes:
    - /var/run/docker.sock:/var/run/docker.sock
    - ./data:/app/data
    ports:
    - 3001:3001
    restart: always
```

3. Uptime Kuma будет доступен по адресу:

```
http://synologyip:3001
```