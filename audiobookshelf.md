# Audiobookshelf

[Audiobookshelf](https://github.com/advplyr/audiobookshelf) — это сервер для самостоятельного хостинга аудиокниг и подкастов с поддержкой всех аудиоформатов, синхронизацией прогресса между устройствами и мобильными приложениями для Android и iOS.

---

1. Создайте в File Station следующую структуру папок:

```
/docker/audiobookshelf/config/
/docker/audiobookshelf/audiobooks/
/docker/audiobookshelf/podcasts/
/docker/audiobookshelf/metadata/
```

2. Создайте в Container Manager новый проект с названием audiobookshelf, выберите путь /docker/audiobookshelf/, выберите в источнике "Создать docker-compose.yml", вставьте в окно ниже следующий код:

```
services:
  audiobookshelf:
    image: advplyr/audiobookshelf:latest
    container_name: audiobookshelf
    ports:
      - 13378:80
    volumes:
      - ./config:/config
      - ./audiobooks:/audiobooks
      - ./podcasts:/podcasts
      - ./metadata:/metadata
    restart: always
```

3. Audiobookshelf будет доступен по адресу:

```
http://synologyip:13378
```
