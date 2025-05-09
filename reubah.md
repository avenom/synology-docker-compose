# Reubah

[Reubah](https://github.com/dendianugerah/reubah) — это веб-приложение для локальной конвертации изображений и документов.

---

1. Создайте в File Station следующую структуру папок:

```
/docker/reubah/
/docker/reubah/doctmp/
```

2. Создайте в Container Manager новый проект с названием reubah, выберите путь /docker/reubah/, выберите в источнике "Создать docker-compose.yml", вставьте в окно ниже следующий код:

```
services:
  reubah:
    image: ghcr.io/dendianugerah/reubah:latest
    container_name: reubah
    volumes:
      - ./doctmp:/tmp
    ports:
      - 8052:8081
    restart: always
```

3. Reubah будет доступен по адресу:

```
http://synologyip:8052
```