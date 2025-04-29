# Glances

Мониторинг сервера, позволяющий отслеживать различные аспекты работы системы, такие как использование процессора, памяти, дисков, сети, запущенные процессы, температуру, напряжение, скорость вращения вентиляторов, контейнеров Docker.

<img src="https://github.com/avenom/synology-docker-compose/blob/main/images/glances.png">

1. Создайте в File Station следующую структуру папок:

```
/docker/glances/
```

2. Создайте в Container Manager новый проект с названием glances, выберите путь /docker/glances/, выберите в источнике "Создать docker-compose.yml", вставьте в окно ниже следующий код:

```
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
    restart: always
```

3. Glances будет доступен по адресу:

```
http://synologyip:61208
```
