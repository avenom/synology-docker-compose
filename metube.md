# MeTube

[MeTube](https://github.com/alexta69/metube) — это веб-интерфейс для скачивания видео, аудио и плейлистов с YouTube и десятков [других сайтов](https://github.com/yt-dlp/yt-dlp/blob/master/supportedsites.md).

---

1. Создайте в File Station следующую структуру папок:

```
/docker/metube/downloads/
```

2. Создайте в Container Manager новый проект с названием metube, выберите путь /docker/metube/, выберите в источнике "Создать docker-compose.yml", вставьте в окно ниже следующий код:

```
services:
  metube:
    image: alexta69/metube:latest
    container_name: metube
    ports:
      - 8081:8081
    volumes:
      - ./downloads:/downloads
    restart: always
```

3. MeTube будет доступен по адресу:

```
http://synologyip:8081
```
