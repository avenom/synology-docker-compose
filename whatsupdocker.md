# What's Up Docker

[What's Up Docker](https://github.com/getwud/wud) — это инструмент для мониторинга и уведомлений о доступных обновлениях Docker-контейнеров, позволяющий пользователю вручную обновлять контейнеры по мере необходимости. 

---

1. Создайте в File Station следующую структуру папок:

```
/docker/whatsupdocker/
```

2. Создайте в Container Manager новый проект с названием whatsupdocker, выберите путь /docker/whatsupdocker/, выберите в источнике "Создать docker-compose.yml", вставьте в окно ниже следующий код:

```
services:
  whatsupdocker:
    image: fmartinou/whats-up-docker:latest
    container_name: whatsupdocker
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
    ports:
      - 3333:3000
    restart: always
```

3. ПРОЕКТ будет доступен по адресу:

```
http://synologyip:3333
```
