# Wallabag + MariaDB + Redis

Веб-приложение типа сервиса Pocket, позволяющее сохранять веб-страницы для последующего чтения, извлекает содержимое веб-страницы, чтобы вы не отвлекались на всплывающие окна.

<img src="https://github.com/avenom/synology-docker-compose/blob/main/images/wallabag.jpg">

1. Создайте в File Station следующую структуру папок:

```
/docker/wallabag/db/
/docker/wallabag/images/
```

2. Создайте в Container Manager новый проект с названием wallabag, выберите путь /docker/wallabag/, выберите в источнике "Создать docker-compose.yml", вставьте в окно ниже следующий код, прописав IP-адрес вашего Synology в  SYMFONY__ENV__DOMAIN_NAME=http://synologyip:8082


```
services:
  wallabag:
    image: wallabag/wallabag:latest
    container_name: wallabag
    environment:
      - MYSQL_ROOT_PASSWORD=wallaroot
      - SYMFONY__ENV__DATABASE_DRIVER=pdo_mysql
      - SYMFONY__ENV__DATABASE_HOST=db
      - SYMFONY__ENV__DATABASE_PORT=3306
      - SYMFONY__ENV__DATABASE_NAME=wallabag
      - SYMFONY__ENV__DATABASE_USER=wallabag
      - SYMFONY__ENV__DATABASE_PASSWORD=wallapass
      - SYMFONY__ENV__DATABASE_CHARSET=utf8mb4
      - SYMFONY__ENV__DATABASE_TABLE_PREFIX="wallabag_"
      - SYMFONY__ENV__DOMAIN_NAME=http://synologyip:8082
      - SYMFONY__ENV__SERVER_NAME="Wallabag"
      - SYMFONY__ENV__FOSUSER_CONFIRMATION=false
    ports:
      - "8082:80"
    volumes:
      - ./images:/var/www/wallabag/web/assets/images
    healthcheck:
      test: ["CMD", "wget" ,"--no-verbose", "--tries=1", "--spider", "http://localhost"]
      interval: 1m
      timeout: 3s
    depends_on:
      - db
      - redis
    restart: always

  db:
    image: mariadb:latest
    container_name: wallabag-db
    environment:
      - MYSQL_ROOT_PASSWORD=wallaroot
    volumes:
      - ./db:/var/lib/mysql
    healthcheck:
      test: ["CMD", "mysqladmin" ,"ping", "-h", "localhost"]
      interval: 20s
      timeout: 3s
    restart: always

  redis:
    image: redis:latest
    container_name: wallabag-redis
    healthcheck:
      test: ["CMD", "redis-cli", "ping"]
      interval: 20s
      timeout: 3s
    restart: always
```

3. После запуска проекта, необходимо подождать 3-5 минут, пока создается база данных, ориентируйтесь на звук работы жестких дисков или установите [Glances](https://github.com/avenom/synology-docker-compose#glances) для мониторинга работы контейнеров. После создания базы данных, статус контейнеров будет зеленым, но статус контейнера wallabag-db возможно будет оранжевым, не обращайте на это внимание, все должно работать без проблем.

4. Зайдите в Wallabag по адресу и зарегистрируйте нового пользователя:

```
http://synologyip:8082
```

5. Перейдите по ссылке http://synologyip:8082/developer. Нажмите "Create a new client", в "Name of the client" введите Wallabagger и нажмите "Create a new client". Запишите Client ID и Client secret.

6. Установите в браузере расширение [Wallabagger](https://github.com/wallabag/wallabagger).

7. В настройках расширения введите следующие данные:

```
Использовать HTTPS: нет
Wallabag URL: synologyip:8082
Проверить URL

Client ID: вставьте сохраненный Client ID
Secret-код клиента: вставьте сохраненный Client secret
Логин пользователя: ваш логин Wallabag
Пароль пользователя: ваш пароль Wallabag
Получить токен
```