# Glances

[Glances](https://github.com/nicolargo/glances) — это кроссплатформенный инструмент мониторинга системы, предоставляющий в реальном времени информацию о процессоре, памяти, дисках, сети, запущенных процессах, температуре, скорости вращения вентиляторов, контейнеров Docker.

---

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
