# Docker Compose для Synology DSM 7.2

В Synology DSM 7.2 пакет Docker получил свое развитие и трансформировался в новый Container Manager. Появилась поддержка проектов Docker Compose, которые позволяют одним конфигом быстрее скачивать и разворачивать все необходимые образы и контейнеры.

Здесь собраны композеры отобранных проектов с непересекающимися друг с другом локальными портами. Если у вас эти порты будут заняты, то можете поменять на свободные. Например в **8080:80** локальный порт - **8080**.

* [Glances](#glances)
* [Home Assistant Container](#home-assistant-container)
* [Homer + Homer Theme v2](#homer)
* [Memos](#memos)
* [MeTube](#metube)
* [Miniflux + PostgreSQL](#miniflux)
* [OpenSpeedTest](#openspeedtest)
* [PlexTraktSync](#plextraktsync)
* [Podgrab](#podgrab)
* [RSS-Bridge](#rss-bridge)
* [SMTP To Telegram](#smtp_to_telegram)
* [Swing Music](#swingmusic)
* [Syncthing](#syncthing)
* [Tasks.md](#tasks-md)
* [Tautulli](#tautulli)
* [Uptime Kuma](#uptime-kuma)
* [Vaultwarden](#vaultwarden)
* [Wallabag + MariaDB + Redis](#wallabag)

## Glances <a name="glances"></a>

Мониторинг системы, позволяющий отслеживать различные аспекты работы системы, такие как использование процессора, памяти, дисков, сети, запущенные процессы, температуру, напряжение, скорость вращения вентиляторов, контейнеров Docker.

<img src="https://github.com/avenom/synology-docker-compose/blob/main/images/glances.jpg">

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
    ports:
      - 61208:61208
    restart: always
```

3. Glances будет доступен по адресу:

```
http://synologyip:61208
```

## Homer + Homer Theme v2 <a name="homer"></a>

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

Дополнительно можете скачать [иконки](https://github.com/avenom/synology-docker-compose/tree/main/homer/assets/tools) для всех сервисов в этом репозитории или скачать [икон-пак](https://github.com/NX211/homer-icons), которые нужно положить в папку /homer/assets/tools.

## Memos <a name="memos"></a>

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

## MeTube <a name="metube"></a>

Веб-интерфейс для скачивания видео и плейлистов с YouTube и десятков [других сайтов](https://github.com/yt-dlp/yt-dlp/blob/master/supportedsites.md).

<img src="https://github.com/avenom/synology-docker-compose/blob/main/images/metube.gif">

1. Создайте в File Station следующую структуру папок:

```
/docker/metube/downloads/
```

2. Создайте в Container Manager новый проект с названием metube, выберите путь /docker/metube/, выберите в источнике "Создать docker-compose.yml", вставьте в окно ниже следующий код:

```
services:
  metube:
    image: ghcr.io/alexta69/metube
    container_name: metube
    ports:
      - "8084:8081"
    volumes:
      - ./downloads:/downloads
    restart: always
```

3. MeTube будет доступен по адресу:

```
http://synologyip:8084
```

## Miniflux + PostgreSQL <a name="miniflux"></a>

Mинималистичный RSS-клиент с интеграциями с разными сервисами.

<img src="https://github.com/avenom/synology-docker-compose/blob/main/images/miniflux.png">

1. Создайте в File Station следующую структуру папок:

```
/docker/miniflux/db/
```

2. Создайте в Container Manager новый проект с названием miniflux, выберите путь /docker/miniflux/, выберите в источнике "Создать docker-compose.yml", вставьте в окно ниже следующий код:

```
services:
  miniflux:
    image: miniflux/miniflux:latest
    container_name: miniflux
    ports:
      - "8081:8080"
    depends_on:
      db:
        condition: service_healthy
    environment:
      - DATABASE_URL=postgres://miniflux:minifluxpassword@db/miniflux?sslmode=disable
      - RUN_MIGRATIONS=1
      - CREATE_ADMIN=1
      - ADMIN_USERNAME=admin
      - ADMIN_PASSWORD=password
    restart: always
  db:
    image: postgres:latest
    container_name: miniflux-db
    environment:
      - POSTGRES_USER=miniflux
      - POSTGRES_PASSWORD=minifluxpassword
      - POSTGRES_DB=miniflux
    volumes:
      - ./db:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD", "pg_isready", "-U", "miniflux"]
      interval: 10s
      start_period: 30s
    restart: always
```

3. Miniflux будет доступен по адресу:

```
http://synologyip:8081
```

Логин:
```
admin
```

Пароль:
```
password
```

## OpenSpeedTest <a name="openspeedtest"></a>

Инструмент для тестирования скорости интернета.

<img src="https://github.com/avenom/synology-docker-compose/blob/main/images/openspeedtest.jpg">

1. Создайте в File Station следующую структуру папок:

```
/docker/openspeedtest/
```

2. Создайте в Container Manager новый проект с названием openspeedtest, выберите путь /docker/openspeedtest/, выберите в источнике "Создать docker-compose.yml", вставьте в окно ниже следующий код:

```
services:
  openspeedtest:
    image: openspeedtest/latest:latest
    container_name: openspeedtest
    ports:
      - 3000:3000
    restart: always
```

3. OpenSpeedTest будет доступен по адресу:

```
http://synologyip:3000
```

## PlexTraktSync <a name="plextraktsync"></a>

Двухсторонняя синхронизация между trakt.tv и Plex Media Server. Требует наличия учетной записи trakt.tv, но не требует наличия подписок Plex Pass и Trakt VIP.

<img src="https://github.com/avenom/synology-docker-compose/blob/main/images/plextraktsync.jpg">

1. Создайте в File Station следующую структуру папок:

```
/docker/plextraktsync/config/
```

2. Создайте в Container Manager новый проект с названием plextraktsync, выберите путь /docker/plextraktsync/, выберите в источнике "Создать docker-compose.yml", вставьте в окно ниже следующий код:

```
services:
  plextraktsync:
    image: twolaw/plextraktsync:latest
    container_name: plextraktsync
    command: sync
    volumes:
      - ./config:/app/config
```

3. Зайдите в контейнер plextraktsync и нажмите "Пуск", после того, как контейнер запустится, нажмите "Действие" и "Откройте терминал". Следуйте пошаговой инструкции (возможно потребуется запустить вручную командой run). После всех этапов, начнется синхронизация между библиотеками Plex и вашим аккаунтом Trakt, после чего контейнер завершит работу и остановится.

4. Важно понимать, что контейнер не нужно держать всегда включенным, после запуска он синхронизирует аккаунты, после чего останавливается. Рекомендую добавить в планировщике Synology DSM запуск по расписанию. Откройте "Панель управления" и перейдите в "Планировщик задач". Создайте запланированную задачу скрипта, заданным пользователем.

```
Общие
Задача: plextraktsync
Пользователь: root

Расписание
Повтор: ежедневно
Время запуска: выберите нужное время

Настройка задач
Скрипт, заданный пользователем
docker container start plextraktsync
```

## Podgrab <a name="podgrab"></a>

Менеджер/загрузчик/архиватор подкастов с интегрированным плеером, позволяющий загружать эпизоды подкастов, как только они выходят в эфир.

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
      - CHECK_FREQUENCY=240
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

## RSS-Bridge <a name="rss-bridge"></a>

Генератор RSS-лент для сайтов, у которых нет RSS-подписок.

<img src="https://github.com/avenom/synology-docker-compose/blob/main/images/rss-bridge.png">

1. Создайте в File Station следующую структуру папок:

```
/docker/rss-bridge/
```

2. Создайте в Container Manager новый проект с названием rss-bridge, выберите путь /docker/rss-bridge/, выберите в источнике "Создать docker-compose.yml", вставьте в окно ниже следующий код:

```
services:
  rss-bridge:
    image: rssbridge/rss-bridge:latest
    container_name: rss-bridge
    volumes:
      - ./:/config
    ports:
      - 3001:80
    restart: always
```

3. RSS-Bridge будет доступен по адресу:

```
http://synologyip:3001
```

## SMTP To Telegram <a name="smtp_to_telegram"></a>

Небольшая программа, которая прослушивает SMTP и отправляет все входящие сообщения электронной почты в Telegram. Если у вас есть программа, которая может отправлять уведомления по электронной почте через SMTP. Вы можете использовать smtp_to_telegram в качестве SMTP-сервера, чтобы письма с уведомлениями отправлялись в выбранные чаты Telegram.

1. Создайте в File Station следующую структуру папок:

```
/docker/smtp_to_telegram/
```

2. Создайте в Container Manager новый проект с названием smtp_to_telegram, выберите папку /docker/smtp_to_telegram/, вставьте следующий код, замените ВАШ ТОКЕН БОТА и ВАШ CHAT ID БОТА на ваши данные:

```
services:
  smtp_to_telegram:
    image: kostyaesmukov/smtp_to_telegram:latest
    container_name: smtp_to_telegram
    environment:
      - PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
      - ST_SMTP_LISTEN=0.0.0.0:2525
      - ST_TELEGRAM_MESSAGE_TEMPLATE={body}
      - ST_TELEGRAM_BOT_TOKEN=ВАШ ТОКЕН БОТА
      - ST_TELEGRAM_CHAT_IDS=ВАШ CHAT ID БОТА
    ports:
      - 2525:2525
    restart: always
```

## Swing Music <a name="swingmusic"></a>

Красиво оформленный, самостоятельно размещаемый сервер потоковой передачи музыки. Как Spotify, только с вашей музыкой.

<img src="https://github.com/avenom/synology-docker-compose/blob/main/images/swingmusic.png">

1. Создайте в File Station следующую структуру папок:

```
/docker/swingmusic/config/
/docker/swingmusic/music/ - скопируйте в эту папку вашу музыку
```

2. Создайте в Container Manager новый проект с названием swingmusic, выберите путь /docker/swingmusic/, выберите в источнике "Создать docker-compose.yml", вставьте в окно ниже следующий код:

```
services:
  swingmusic:
    image: ghcr.io/swing-opensource/swingmusic:latest
    container_name: swingmusic
    volumes:
      - ./config:/config
      - ./music:/music
    ports:
      - "1970:1970"
    restart: always
```

3. Swing Music будет доступен по адресу:

```
http://synologyip:1970
```

4. Нажмите Specific directories, выберите /music, нажмите Select here.

## Syncthing <a name="syncthing"></a>

Двухсторонняя синхронизация файлов между двумя или более устройствами, например между компьютером и сервером.

<img src="https://github.com/avenom/synology-docker-compose/blob/main/images/syncthing.png">

1. Создайте в File Station следующую структуру папок:

```
/docker/syncthing/config/
```

2. Создайте в Container Manager новый проект с названием syncthing, выберите путь /docker/syncthing/, выберите в источнике "Создать docker-compose.yml", вставьте в окно ниже следующий код:

```
services:
  syncthing:
    image: lscr.io/linuxserver/syncthing:latest
    container_name: syncthing
    hostname: syncthing
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Etc/UTC
    volumes:
      - ./config:/config
    ports:
      - 8384:8384
      - 22000:22000/tcp
      - 22000:22000/udp
      - 21027:21027/udp
    restart: always
```

3. Syncthing на сервере будет доступен по адресу:

```
http://synologyip:8384
```

4. [Скачайте](https://syncthing.net/downloads/) и запустите Syncthing на компьютере, который будет доступен по адресу:

```
http://127.0.0.1:8384
```

Для примера создадим синхронизацию одной папки между сервером и компьютером на винде.

5. В Syncthing на сервере создадим и добавим папку /config/files. В "ID папки" пропишите files.

6. В Syncthing на компьютере создадим и добавим папку C:\files. В "ID папки" пропишите files.

7. Откройте в Syncthing на компьютере "Действия" > "Показать ID" и нажмите "Скопировать". В Syncthing на сервере нажмите "Добавить удаленное устройство" и вставьте из буфера в "ID устройства".

8. Откройте в Syncthing на сервере "Действия" > "Показать ID" и нажмите "Скопировать". В Syncthing на компьютере нажмите "Добавить удаленное устройство" и вставьте из буфера в "ID устройства".

Убедитесть, что на обоих устройствах будет написано "Подключено"

9. В Syncthing на компьютере отредактируйте папку files, откройте вкладку "Предоставление доступа" и выберите подключенный Syncthing на сервере.

10. В Syncthing на сервере отредактируйте папку files, откройте вкладку "Предоставление доступа" и выберите подключенный Syncthing на компьютере.

Убедитесть, что на обоих устройствах будет написано "В актуальном состоянии".

Можете добавлять файлы в папки!

## Tasks.md <a name="tasks-md"></a>

Минималистичный органайзер заметок и задач с поддержкой синтаксиса Markdown, тегов, возможностью прикрепления картинок. Все заметки хранятся в виде .md-файлов.

<img src="https://github.com/avenom/synology-docker-compose/blob/main/images/tasks-md.gif">

1. Создайте в File Station следующую структуру папок:

```
/docker/tasks-md/api-data/
/docker/tasks-md/stylesheets-data/
/docker/tasks-md/images/
```

2. Создайте в Container Manager новый проект с названием tasks-md, выберите путь /docker/tasks-md/, выберите в источнике "Создать docker-compose.yml", вставьте в окно ниже следующий код:

```
services:
  tasks-md:
      image: baldissaramatheus/tasks.md:latest
      container_name: tasks-md
      environment:
          - ENABLE_LOCAL_IMAGES=true
      volumes:
          - ./api-data:/api/files
          - ./stylesheets-data:/api/static/stylesheets/
          - ./images:/api/images/
      ports:
        - 3456:8080
      restart: always
```

3. Tasks.md будет доступен по адресу:

```
http://synologyip:3456
```

Можно выбрать три цветовые темы, заменив вторую строчку в файле /docker/tasks-md/stylesheets-data/index.css

```
@import url(./color-themes/adwaita.css);
@import url(./color-themes/catppuccin.css);
@import url(./color-themes/nord.css);
```

## Tautulli <a name="tautulli"></a>

Веб-приложение для мониторинга, аналитики и уведомлений для Plex Media Server.

<img src="https://github.com/avenom/synology-docker-compose/blob/main/images/tautulli.jpg">

1. Создайте в File Station следующую структуру папок:

```
/docker/tautulli/config/
```

2. Создайте в Container Manager новый проект с названием tautulli, выберите путь /docker/tautulli/, выберите в источнике "Создать docker-compose.yml", вставьте в окно ниже следующий код:

```
services:
  tautulli:
    image: tautulli/tautulli:latest
    container_name: tautulli
    volumes:
      - ./config:/config
    environment:
      - PUID=<uid>
      - PGID=<gid>
      - TZ=<timezone>
    ports:
      - 8181:8181
    restart: always
```

3. Tautulli будет доступен по адресу:

```
http://synologyip:8181
```

## Uptime Kuma <a name="uptime-kuma"></a>

Мониторинг доступности и аптайма сайтов.

<img src="https://github.com/avenom/synology-docker-compose/blob/main/images/uptime-kuma.jpg">

1. Создайте в File Station следующую структуру папок:

```
/docker/uptime-kuma/data/
```

2. Создайте в Container Manager новый проект с названием uptime-kuma, выберите путь /docker/uptime-kuma/, выберите в источнике "Создать docker-compose.yml", вставьте в окно ниже следующий код:

```
services:
  uptime-kuma:
    image: elestio/uptime-kuma:latest
    container_name: uptime-kuma
    volumes:
    - ./data:/app/data
    ports:
    - 3002:3001
    restart: always
```

3. Uptime Kuma будет доступен по адресу:

```
http://synologyip:3002
```

## Vaultwarden <a name="vaultwarden"></a>

Неофициальный сервер хранилища паролей, совместимый с Bitwarden. Подходит для самостоятельного развертывания, когда запуск официального сервиса Bitwarden, требующего больших ресурсов, может быть не идеальным.

<img src="https://github.com/avenom/synology-docker-compose/blob/main/images/vaultwarden.png">

1. Vaultwarden работает по протоколу HTTPS, поэтому в Synology DSM нужно настроить обратный прокси. Перейдите в Панель управления > Портал для входа > Дополнительно, нажмите Обратный прокси, создайте обратный прокси со следующими данными:

```
Имя обратного прокси: vaultwarden

Источник
Протокол: HTTPS
Имя хоста: vaultwarden
Порт: 443
Включить HSTS: да

Место назначения:
Протокол: HTTP
Имя хоста: IP-адрес вашего Synology
Порт: 5151
```

2. Перейдите в Панель управления > Сеть > Возможности подключения, включите "Включить HTTP/2".

3. Перейдите в Панель управления > Безопасность > Дополнительно, включите "Включить сжатие HTTPS".

4. Создайте в File Station следующую структуру папок:

```
/docker/vaultwarden/
```

5. Создайте в Container Manager новый проект с названием vaultwarden, выберите путь /docker/vaultwarden/, выберите в источнике "Создать docker-compose.yml", вставьте в окно ниже следующий код:

```
services:
  vaultwarden:
    image: vaultwarden/server:latest
    container_name: vaultwarden
    volumes:
      - ./:/data
    ports:
      - 3012:3012
      - 5151:80
    restart: always
```

6. Vaultwarden будет доступен по адресу:

```
https://vaultwarden.local
```

## Wallabag + MariaDB + Redis <a name="wallabag"></a>

Веб-приложение, позволяющее сохранять веб-страницы для последующего чтения. Кликните, сохраните и прочитайте, когда захотите. Оно извлекает содержимое, чтобы вы не отвлекались на всплывающие окна.

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

5. Для сохранения статей установите в браузере расширение [Wallabagger](https://github.com/wallabag/wallabagger).
