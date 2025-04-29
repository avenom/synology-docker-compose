# Podgrab

Менеджер и загрузчик подкастов с интегрированным плеером.

<img src="https://github.com/avenom/synology-docker-compose/blob/main/images/podgrab.jpg">

1. Создайте в File Station следующую структуру папок:

```
/docker/podgrab/config/
/docker/podgrab/data/
```

2. Создайте в Container Manager новый проект с названием podgrab, выберите путь /docker/podgrab/, выберите в источнике "Создать docker-compose.yml", вставьте в окно ниже следующий код. В CHECK_FREQUENCY можно выставить частоту проверки наличия новых выпусков в минутах.

```
services:
  podgrab:
    image: akhilrex/podgrab:latest
    container_name: podgrab
    environment:
      - CHECK_FREQUENCY=60
    volumes:
      - ./config:/config
      - ./data:/assets
    ports:
      - 8083:8080
    restart: always
```

3. Podgrab будет доступен по адресу:

```
http://synologyip:8083
```