# Docker Compose для Synology DSM 7.2

В Synology DSM 7.2 пакет Docker получил свое развитие и трансформировался в новый Container Manager. Появилась поддержка проектов Docker Compose, которые позволяют одним конфигом быстрее скачивать и разворачивать все необходимые образы и контейнеры.

Здесь собраны композеры отобранных проектов с непересекающимися друг с другом локальными портами. Если у вас эти порты будут заняты, то можете поменять на свободные. Например в **8080:80** локальный порт - **8080**.

* [Glances](#glances)
* [Home Assistant Container](#home-assistant-container)
* [Homer + Homer Theme v2](#homer)
* [Miniflux + PostgreSQL](#miniflux)
* [OpenSpeedTest](#openspeedtest)
* [PlexTraktSync](#plextraktsync)
* [Podfetch + PostgreSQL](#podfetch)
* [RSS-Bridge](#rss-bridge)
* [SMTP To Telegram](#smtp_to_telegram)
* [Swing Music](#swingmusic)
* [Tautulli](#tautulli)
* [Uptime Kuma](#uptime-kuma)
* [Vaultwarden](#vaultwarden)
* [Wallabag](#wallabag)

## Glances <a name="glances"></a>

1. Создайте в File Station следующую структуру папок:

```
/docker/glances/
```

2. Создайте в Container Manager новый проект с названием glances, выберите путь /docker/glances/, выберите в источнике "Создать docker-compose.yml", вставьте в окно ниже следующий код:

```
version: '2'
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

1. Создайте в File Station следующую структуру папок:

```
/docker/homer/assets/
```

2. Скачайте zip-архив [Homer Theme v2](https://github.com/walkxcode/homer-theme/releases), скопируйте из архива содержимое папки assets в /docker/homer/assets.


3. Создайте в Container Manager новый проект с названием homer, выберите путь /docker/homer/, выберите в источнике "Создать docker-compose.yml", вставьте в окно ниже следующий код:

```
version: '2'
services:
  homer:
    image: b4bz/homer:latest
    container_name: homer
    volumes:
      - /volume1/docker/homer/assets/:/www/assets
    ports:
      - 8080:8080
    restart: always
```

4. Homer будет доступен по адресу:

```
http://synologyip:8080
```

Ссылки добавляются вручную путем редактирования файла /docker/homer/assets/config.yml

## OpenSpeedTest <a name="openspeedtest"></a>

1. Создайте в File Station следующую структуру папок:

```
/docker/openspeedtest/
```

2. Создайте в Container Manager новый проект с названием openspeedtest, выберите путь /docker/openspeedtest/, выберите в источнике "Создать docker-compose.yml", вставьте в окно ниже следующий код:

```
version: '3.3'
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

1. Создайте в File Station следующую структуру папок:

```
/docker/plextraktsync/config/
```

2. Создайте в Container Manager новый проект с названием plextraktsync, выберите путь /docker/plextraktsync/, выберите в источнике "Создать docker-compose.yml", вставьте в окно ниже следующий код:

```
version: '2'
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

## Podfetch + PostgreSQL <a name="podfetch"></a>

1. Создайте в File Station следующую структуру папок:

```
/docker/podfetch/db/
```

2. Создайте в Container Manager новый проект с названием podfetch, выберите путь /docker/podfetch/, выберите в источнике "Создать docker-compose.yml", вставьте в окно ниже следующий код:

```
version: '3'
services:
  podfetch:
    image: samuel19982/podfetch:latest
    container_name: podfetch
    user: 1026:100
    ports:
      - "8000:8000"
    depends_on:
      - postgres
    volumes:
      - /volume1/docker/podfetch:/app/podcasts:rw
    environment:
      - POLLING_INTERVAL=300
      - SERVER_URL=http://localhost:80
      - DATABASE_URL=postgresql://postgres:changeme@postgres/podfetch
    restart: always

  postgres:
    image: postgres:latest
    container_name: podfetch-db
    security_opt:
      - no-new-privileges:true
    healthcheck:
      test: ["CMD", "pg_isready", "-q", "-d", "podfetch", "-U", "postgres"]
    environment:
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: changeme
      POSTGRES_DB: podfetch
    volumes:
      - /volume1/docker/podfetch/db:/var/lib/postgresql/data:rw
    restart: always
```

3. Podfetch будет доступен по адресу:

```
http://synologyip:8000
```

## RSS-Bridge <a name="rss-bridge"></a>

1. Создайте в File Station следующую структуру папок:

```
/docker/rss-bridge/
```

2. Создайте в Container Manager новый проект с названием rss-bridge, выберите путь /docker/rss-bridge/, выберите в источнике "Создать docker-compose.yml", вставьте в окно ниже следующий код:

```
version: '2'
services:
  rss-bridge:
    image: rssbridge/rss-bridge:latest
    container_name: rss-bridge
    volumes:
      - /volume1/docker/rss-bridge:/config
    ports:
      - 3001:80
    restart: always
```

3. RSS-Bridge будет доступен по адресу:

```
http://synologyip:3001
```

## SMTP To Telegram <a name="smtp_to_telegram"></a>

SMTP To Telegram можно настроить и использовать для отправки уведомлений из Synology DSM и Download Station в вашего телеграм-бота. Подробная инструкция будет позже.

1. Создайте в File Station следующую структуру папок:

```
/docker/smtp_to_telegram/
```

2. Создайте в Container Manager новый проект с названием smtp_to_telegram, выберите папку /docker/smtp_to_telegram/, вставьте следующий код, замените ВАШ ТОКЕН БОТА и ВАШ CHAT ID БОТА на ваши данные:

```
version: '2'
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

1. Создайте в File Station следующую структуру папок:

```
/docker/swingmusic/config/
/docker/swingmusic/music/ - скопируйте в эту папку вашу музыку
```

2. Создайте в Container Manager новый проект с названием swingmusic, выберите путь /docker/swingmusic/, выберите в источнике "Создать docker-compose.yml", вставьте в окно ниже следующий код:

```
version: '2'
services:
  swingmusic:
    image: bitnik212/swingmusic:latest
    container_name: swingmusic
    volumes:
      - ./music:/music
      - ./config:/config
    ports:
      - "1970:1970"
    restart: always
```

3. Swing Music будет доступен по адресу:

```
http://synologyip:1970
```

4. Нажмите Customize root directories, выберите /music, нажмите Select here.

## Tautulli <a name="tautulli"></a>

1. Создайте в File Station следующую структуру папок:

```
/docker/tautulli/config/
```

2. Создайте в Container Manager новый проект с названием tautulli, выберите путь /docker/tautulli/, выберите в источнике "Создать docker-compose.yml", вставьте в окно ниже следующий код:

```
version: '3'
services:
  tautulli:
    image: tautulli/tautulli:latest
    container_name: tautulli
    volumes:
      - /volume1/docker/tautulli/config:/config
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

1. Создайте в File Station следующую структуру папок:

```
/docker/uptime-kuma/data/
```

2. Создайте в Container Manager новый проект с названием uptime-kuma, выберите путь /docker/uptime-kuma/, выберите в источнике "Создать docker-compose.yml", вставьте в окно ниже следующий код:

```
version: '3.3'
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

1. Vaultwarden работает по протоколу HTTPS, поэтому в Synology DSM нужно настроить обратный прокси. Откройте Панель управления, перейдите в Портал для входа, откройты вкладку Дополнительно, нажмите Обратный прокси, создайте обратный прокси со следующими данными:

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

1. Создайте в File Station следующую структуру папок:

```
/docker/uptime-kuma/data/
```

2. Создайте в Container Manager новый проект с названием uptime-kuma, выберите путь /docker/uptime-kuma/, выберите в источнике "Создать docker-compose.yml", вставьте в окно ниже следующий код:

```
version: '3.3'
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
https://vaultwarden.local
```
