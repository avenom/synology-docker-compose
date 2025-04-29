# Homer + Homer Theme v2

Стартовая страница с вашими ссылками.

<img src="https://github.com/avenom/synology-docker-compose/blob/main/images/homer.jpg">

1. Создайте в File Station следующую структуру папок:

```
/docker/homer/assets/
```

2. Скачайте zip-архив [Homer Theme v2](https://github.com/walkxcode/homer-theme/releases), скопируйте из архива содержимое папки assets в /docker/homer/assets.


3. Создайте в Container Manager новый проект с названием homer, выберите путь /docker/homer/, выберите в источнике "Создать docker-compose.yml", вставьте в окно ниже следующий код:

```
services:
  homer:
    image: b4bz/homer:latest
    container_name: homer
    volumes:
      - ./assets/:/www/assets
    ports:
      - 8080:8080
    restart: always
```

4. Homer будет доступен по адресу:

```
http://synologyip:8080
```

Ссылки добавляются вручную путем редактирования файла /docker/homer/assets/config.yml

Дополнительно можете скачать [иконки](https://github.com/avenom/synology-docker-compose/tree/main/homer/assets/tools) для всех сервисов в этом репозитории или скачать [икон-пак](https://github.com/NX211/homer-icons), которые нужно положить в папку /docker/homer/assets/tools.