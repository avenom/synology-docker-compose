# Memos

Минималистичный органайзер заметок и задач с поддержкой синтаксиса Markdown, тегов, возможностью прикрепления картинок.

<img src="https://github.com/avenom/synology-docker-compose/blob/main/images/memos.jpg">

1. Создайте в File Station следующую структуру папок:

```
/docker/memos/
```

2. Создайте в Container Manager новый проект с названием memos, выберите путь /docker/memos/, выберите в источнике "Создать docker-compose.yml", вставьте в окно ниже следующий код:

```
services:
  memos:
    image: ghcr.io/usememos/memos:latest
    container_name: memos
    volumes:
      - ./:/var/opt/memos
    ports:
      - 5230:5230
    restart: always
```

3. Memos будет доступен по адресу:

```
http://synologyip:5230
```