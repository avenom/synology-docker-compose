# Homepage

[Homepage](https://github.com/gethomepage/homepage) — это ваша стартовая страница с вашими ссылками.

---

1. Создайте в File Station следующую структуру папок:

```
/docker/homepage/config/
/docker/homepage/public/icons/
/docker/homepage/public/images/
```

2. Создайте в Container Manager новый проект с названием homepage, выберите путь /docker/homepage/, выберите в источнике "Создать docker-compose.yml", вставьте в окно ниже следующий код:

```
services:
  homepage:
    image: gethomepage/homepage:latest
    container_name: homepage
    environment:
      HOMEPAGE_ALLOWED_HOSTS: '*'
      PUID: 1026
      PGID: 100
    volumes:
      - ./config:/app/config
      - ./public/icons:/app/public/icons
      - ./public/images:/app/public/images
      - /var/run/docker.sock:/var/run/docker.sock
    ports:
      - 3535:3000
    restart: always
```

* [Как узнать ваш PUID и PGID в Synology DSM](https://github.com/avenom/synology-docker-compose#puid)

3. Homepage будет доступен по адресу:

```
http://synologyip:3535
```

Ссылки добавляются вручную путем редактирования файла /docker/homepage/config/services.yaml

Дополнительно можете скачать мою коллекцию [иконок и логотипов](https://github.com/avenom/synology-docker-compose/tree/main/icons) или скачать [икон-пак](https://github.com/NX211/homer-icons), которые нужно положить в папку /docker/homepage/public/tools.
