# Docker Compose для Synology DSM 7.2
<img src="https://github.com/avenom/synology-docker-compose/blob/main/images/synology-docker-compose.jpg">

В Synology DSM 7.2 пакет Docker получил свое логическое развитие и трансформировался в новый Container Manager с поддержкой проектов Docker Compose, которые позволяют одним конфигурационным файлом скачивать и разворачивать все необходимые для установки образы и контейнеры.

В этом репозитории собраны проверенные на работоспособность проекты Docker Compose для разных сервисов с непересекающимися друг с другом локальными портами. Если у вас эти порты будут заняты, то можете поменять на свободные. Например в **8080:80** локальный порт - **8080**.

Если заметите неточности, то пишите в [телеграм](https://t.me/avenom).

* [Как узнать ваш PUID и PGID в Synology DSM](#puid)
* [Активация Gmail SMTP для контейнеров Docker](#gmail)

| Проект | Порт |
|---|:---:|
|[Glances](#glances)|61208|
|[Home Assistant](#home-assistant)|8123|
|[Homer + Homer Theme v2](#homer)|8080|
|[Memos](#memos)|5230|
|[MeTube](#metube)|8081|
|[Miniflux + PostgreSQL](#miniflux)|8085|
|[OpenSpeedTest](#openspeedtest)|3000|
|[Podgrab](#podgrab)|8083|
|[RSS-Bridge](#rss-bridge)|8086|
|[SMTP To Telegram](#smtp_to_telegram)|2525|
|[Tautulli](#tautulli)|8181|
|[Uptime Kuma](#uptime-kuma)|3001|
|[Vaultwarden](#vaultwarden)|3012, 5151|
|[Wallabag + MariaDB + Redis](#wallabag)|8082, 3306|

## Glances <a name="glances"></a>

Мониторинг сервера, позволяющий отслеживать различные аспекты работы системы, такие как использование процессора, памяти, дисков, сети, запущенные процессы, температуру, напряжение, скорость вращения вентиляторов, контейнеров Docker.

<img src="https://github.com/avenom/synology-docker-compose/blob/main/images/glances.png">

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
    restart: always
```

3. Glances будет доступен по адресу:

```
http://synologyip:61208
```

## Home Assistant <a name="home-assistant"></a>

Система домашней автоматизации с открытым исходным кодом, которая ставит на первое место локальный контроль и конфиденциальность.

<img src="https://github.com/avenom/synology-docker-compose/blob/main/images/home-assistant.png">

1. Создайте в File Station следующую структуру папок:

```
/docker/homeassistant/config/
/docker/homeassistant/config/custom_components/hacs
```

2. В Home Assistant Container нет дополнительных интеграций Supervisor, но можно установить сторонний репозиторий интеграций HACS. Скачайте [hacs.zip](https://github.com/hacs/integration/releases) и разархивируйте содержимое архива в /docker/homeassistant/config/custom_components/hacs/.

3. Создайте в Container Manager новый проект с названием homeassistant, выберите путь /docker/homeassistant/, выберите в источнике "Создать docker-compose.yml", вставьте в окно ниже следующий код:

```
services:
  homeassistant:
      image: homeassistant/home-assistant:latest
      container_name: homeassistant
      network_mode: host
      environment:
        - TZ=Europe/Moscow
      volumes:
        - ./config:/config
      restart: always
```

4. Home Assistant будет доступен по адресу:

```
http://synologyip:8123
```

5. Создайте нового пользователя. В Home Assistant перейдите в "Настройки" > "Устройства и службы", нажмите "Добавить интеграцию", найдите и выберите HACS. Отметьте все и нажмите "Подтвердить". Далее потребуется наличие аккаунта GitHub. Скопируйте код и вставьте его на https://github.com/login/device, нажмите Continue, далее Authorize hacs.

6. В левой панели выберите HACS, в "Отфильтровано по принадлежности к Downloaded" нажмите "Очистить" и можете ставить интеграции. Например можно привязать к Home Assistant Яндекс Станцию, установив две интеграции: Yandex Smart Home и Yandex Station. Далее добавьте эти интеграции в "Настройки" > "Устройства и службы". 

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

Дополнительно можете скачать [иконки](https://github.com/avenom/synology-docker-compose/tree/main/homer/assets/tools) для всех сервисов в этом репозитории или скачать [икон-пак](https://github.com/NX211/homer-icons), которые нужно положить в папку /docker/homer/assets/tools.

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

<img src="https://github.com/avenom/synology-docker-compose/blob/main/images/metube.png">

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

## Miniflux + PostgreSQL <a name="miniflux"></a>

Mинималистичный RSS-агрегатор с интеграциями с разными сервисами.

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
      - "8085:8080"
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
http://synologyip:8085
```

Логин:
```
admin
```

Пароль:
```
password
```

Рекомендую использовать совместно с [RSS-Bridge](#rss-bridge).

## OpenSpeedTest <a name="openspeedtest"></a>

Тестирование скорости и пинга интернета.

<img src="https://github.com/avenom/synology-docker-compose/blob/main/images/openspeedtest.png">

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

## Podgrab <a name="podgrab"></a>

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

## RSS-Bridge <a name="rss-bridge"></a>

Генератор RSS-лент для сайтов и сервисов, которые не предоставляют RSS-подписки.

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
      - 8086:80
    restart: always
```

3. RSS-Bridge будет доступен по адресу:

```
http://synologyip:8086
```

## SMTP To Telegram <a name="smtp_to_telegram"></a>

SMTP-шлюз, который отправляет все входящие сообщения электронной почты в бота Telegram. Например можно настроить отправку уведомлений из Synology DSM в вашего телеграм-бота, прописав пользовательский SMTP-сервер 0.0.0.0 с портом 2525.

1. Создайте телеграм-бота с помощью бота [@BotFather](https://t.me/BotFather). Выберите в "Меню" /newbot, придумайте имя бота типа name_bot. Сохраните токен API.

2. Запустите вашего бота и отправьте ему любое сообщение. В ссылке ниже вставьте вместо API ваш длинный токен и перейдите по ссылке:

```
https://api.telegram.org/botAPI/getUpdates
```

3. Найдите и сохраните номер Chat ID, например в строке "id":НОМЕР,".

4. Создайте в File Station следующую структуру папок:

```
/docker/smtp_to_telegram/
```

5. Создайте в Container Manager новый проект с названием smtp_to_telegram, выберите папку /docker/smtp_to_telegram/, вставьте следующий код, замените ВАШ ТОКЕН API БОТА и ВАШ CHAT ID БОТА на данные вашего бота.

Вариант с портом:

```
services:
  smtp_to_telegram:
    image: kostyaesmukov/smtp_to_telegram:latest
    container_name: smtp_to_telegram
    environment:
      - PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
      - ST_SMTP_LISTEN=0.0.0.0:2525
      - ST_TELEGRAM_MESSAGE_TEMPLATE={body}
      - ST_TELEGRAM_BOT_TOKEN=ВАШ ТОКЕН API БОТА
      - ST_TELEGRAM_CHAT_IDS=ВАШ CHAT ID БОТА
    ports:
      - 2525:2525
    restart: always
```

Вариант с host:

```
services:
  smtp_to_telegram:
    image: kostyaesmukov/smtp_to_telegram:latest
    container_name: smtp_to_telegram
    network_mode: host
    environment:
      - PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
      - ST_TELEGRAM_MESSAGE_TEMPLATE={body}
      - ST_TELEGRAM_BOT_TOKEN=ВАШ ТОКЕН API БОТА
      - ST_TELEGRAM_CHAT_IDS=ВАШ CHAT ID БОТА
    restart: always
```

## Tautulli <a name="tautulli"></a>

Веб-приложение для мониторинга, аналитики и уведомлений для сервера Plex.

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
    network_mode: host
    volumes:
      - ./config:/config
    environment:
      - PUID=1026
      - PGID=100
      - TZ=Europe/Moscow
    restart: always
```

3. Tautulli будет доступен по адресу:

```
http://synologyip:8181
```

## Uptime Kuma <a name="uptime-kuma"></a>

Мониторинг доступности сайтов, доменов, сервисов и портов.

<img src="https://github.com/avenom/synology-docker-compose/blob/main/images/uptime-kuma.png">

1. Создайте в File Station следующую структуру папок:

```
/docker/uptime-kuma/data/
```

2. Создайте в Container Manager новый проект с названием uptime-kuma, выберите путь /docker/uptime-kuma/, выберите в источнике "Создать docker-compose.yml", вставьте в окно ниже следующий код:

```
services:
  uptime-kuma:
    image: louislam/uptime-kuma:latest
    container_name: uptime-kuma
    volumes:
    - /var/run/docker.sock:/var/run/docker.sock
    - ./data:/app/data
    ports:
    - 3001:3001
    restart: always
```

3. Uptime Kuma будет доступен по адресу:

```
http://synologyip:3001
```

## Vaultwarden <a name="vaultwarden"></a>

Неофициальный сервер хранилища паролей, совместимый с Bitwarden. Подходит для самостоятельного развертывания, когда для запуска официального сервиса Bitwarden не хватает ресурсов.

<img src="https://github.com/avenom/synology-docker-compose/blob/main/images/vaultwarden.png">

1. Создайте в File Station следующую структуру папок:

```
/docker/vaultwarden/
```

2. Создайте в Container Manager новый проект с названием vaultwarden, выберите путь /docker/vaultwarden/, выберите в источнике "Создать docker-compose.yml", вставьте в окно ниже следующий код, указав в ADMIN_TOKEN пароль для доступа к админке:

```
services:
  vaultwarden:
    image: vaultwarden/server:latest
    container_name: vaultwarden
    environment:
      - ADMIN_TOKEN=jDAeGLC5C5tJ7f
    volumes:
      - ./:/data
    ports:
      - 3012:3012
      - 5151:80
    restart: always
```

3. Vaultwarden работает по протоколу HTTPS, поэтому в Synology DSM нужно сделать некоторые настройки и воспользоваться несколькими способами получения доступа.

4. Перейдите в "Панель управления" > "Сеть" > "Возможности подключения", включите "Включить HTTP/2".

5. Перейдите в "Панель управления" > "Безопасность" > "Дополнительно", включите "Включить сжатие HTTPS".

**Способ 1: KeenDNS с SSL-сертификатом Let's Encrypt в роутерах Keenetic.**

Если у вас роутер Keenetic, то вы можете использовать KeenDNS, который автоматически получает SSL-сертификат Let's Encrypt.

Используя этот способ вы открываете доступ к Vaultwarden из интернета, поэтому желательно иметь стойкий пароль и включить двухэтапную аутентификацию.

6. В админке Keenetic перейдите в "Сетевые правила" > "Доменное имя", придумайте себе доменное имя типа name.keenetic.pro. В конце страницы нажмите "Добавить" и сделайте следующие настройки:

```
Имя: vaultwarden
Доступ из интернета: Свободный доступ
Устройство: Ваш NAS Synology
Протокол: HTTP
Порт: 5151
```

7. Зайдите в Vaultwarden по адресу и зарегистрируйте нового пользователя:

```
https://vaultwarden.name.keenetic.pro
```

**Способ 2: Незащищенный HTTPS без SSL-сертификата Let's Encrypt для Synology.**

8. Перейдите в "Панель управления" > "Портал для входа" > "Дополнительно", создайте обратный прокси.

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

Пользовательский заголовок
Создать > WebSocket
```

9. Зайдите в Vaultwarden по адресу и зарегистрируйте нового пользователя:

```
https://vaultwarden.local
```

**Способ 3: Защищенный HTTPS с SSL-сертификатом Let's Encrypt для Synology.**

Используя этот способ вы открываете доступ к Synology DSM / Vaultwarden из интернета, поэтому желательно иметь стойкий пароль и включить двухэтапную аутентификацию.

10. Для доступа к Synology DSM по HTTPS с SSL нужно открыть порты 5001, 80, 443. Порты 80 и 443 обязательны для проверки SSL-сертификата Let’s Encrypt, а порт 5001 используется для подключения к вашему NAS по протоколу HTTPS. Решайте сами открывать 5001 или нет или после получения сертификата оставьте только правило с открытым портом 443. Далее пример с роутерами Keenetic. В админке Keenetic перейдите в "Сетевые правила" > "Переадресация портов" и добавьте три правила:

```
DSM
Описание: DSM
Вход: Ваше интернет-подключение
Выход: Ваш NAS Synology
Протокол: TCP/UDP
Открыть порт: 5001

DSM WEB
Описание: DSM WEB
Вход: Ваше интернет-подключение
Выход: Ваш NAS Synology
Протокол: TCP/UDP
Открыть порт: 80

DSM SSL
Описание: DSM SSL
Вход: Ваше интернет-подключение
Выход: Ваш NAS Synology
Протокол: TCP/UDP
Открыть порт: 443
```

11. Перейдите в Synology DSM в "Панель управления" > "Внешний доступ" > "DDNS" и нажмите "Добавить".

```
Поставщик услуг: Synology
Имя хоста: name (придумайте имя)
Учетная запись Synology: войдите в вашу учетку.
Внешний адрес (IPv4): Авто (можете свериться с вашим IP на сайте 2ip.ru)
Внешний адрес (IPv6): Авто
Получить сертификат из Let's Encrypt: да
Включить Heartbeat: да
```

Проверьте доступ к Synology DSM с SSL по ссылке:

```
https://name.synology.me:5001/
```

12. Перейдите в "Панель управления" > "Портал для входа" > "Дополнительно", создайте обратный прокси.

```
Имя обратного прокси: vaultwarden

Источник
Протокол: HTTPS
Имя хоста: vaultwarden.name.synology.me
Порт: 443
Включить HSTS: да

Место назначения:
Протокол: HTTP
Имя хоста: IP-адрес вашего Synology
Порт: 5151

Пользовательский заголовок
Создать > WebSocket
```

13. Зайдите в Vaultwarden по адресу и зарегистрируйте нового пользователя:

```
https://vaultwarden.name.synology.me
```

14. Зайдите в админку Vaultwarden с паролем в ADMIN_TOKEN:

```
https://vaultwarden.name.keenetic.pro/admin
https://vaultwarden.local/admin
https://vaultwarden.name.synology.me/admin/
```

15. Перейдите в "General settings", в "Domain URL" введите HTTPS-адрес Vaultwarden (пр. https://vaultwarden.name.synology.me), снимите галочку с "Allow new signups", чтобы отключить регистрацию новых пользователей и нажмите "Save".

16. Установите расширение [Bitwarden](https://bitwarden.com/download) для вашего браузера и подключитесь к собственному серверу https://vaultwarden.local или https://vaultwarden.name.keenetic.pro.

Настоятельно рекомендую включить в настройках Vaultwarden двухэтапную аутентификацию и использовать приложения: [Google Authenticator](https://support.google.com/accounts/answer/1066447?sjid=16437106721951447589-EU), [Microsoft Authenticator](https://www.microsoft.com/ru-ru/security/mobile-authenticator-app), [Authy](https://authy.com). На всякий случай сохраните код восстановления для Vaultwarden.

## Wallabag + MariaDB + Redis <a name="wallabag"></a>

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

## Как узнать ваш PUID и PGID в Synology DSM <a name="puid"></a>

В некоторых случаях контейнерам нужно знать ваши идентификаторы пользователя (UID) и группы (GID) в Synology DSM.

1.  Откройте "Панель управления" и перейдите в "Планировщик задач". Создайте запланированную задачу скрипта, заданным пользователем.

```
Общие
Задача: PUID и PGID
Пользователь: ваш логин
Включено: нет

Расписание
Дата
Выполнить в следующее число
Поовтор: не повторять

Настройки задач
Уведомления
Отправлять сведения о запуске на электронную почту: ваша почта
Скрипт, заданный пользователем
id
```

2. Выберите в списке задач "PUID и PGID" и нажмите "Запустить".

3. Вам на почту придет такое письмо:

```
Планировщик задач завершил выполнение запланированной задачи.

Задача: PUID and PGID
Время начала: Tue, 20 Aug 2024 16:03:54 +0300
Время остановки: Tue, 20 Aug 2024 16:03:54 +0300
Текущее состояние: 0 (Обычный)
Стандартный вывод/ошибка:
uid=1026(Логин) gid=100(users) groups=100(users),101(administrators)
```

Нужные цифры в строчке uid=1026(Логин) gid=100(users).

## Активация Gmail SMTP для контейнеров Docker <a name="gmail"></a>

Для работы почты в контейнерах докера, нужно сгенерировать пароль приложения в аккаунте Google.

1. Перейти в [Google Security](https://myaccount.google.com/security).
2. Убедитесь, что у вас включена двухэтапная аутентификация: "Вход в Google" > "Двухэтапная аутентификация" > "Включена".
3. Перейти в [Пароли приложений](https://myaccount.google.com/apppasswords).
4. Название приложения: SMTP
5. Google сгенерирует пароль с пробелами. Вам следует удалить все пробелы и сохранить пароль без пробелов.

Настройки сервера Gmail:

```
Host: smtp.gmail.com
Port: 587
Encryption: TLS
Username: yourname@gmail.com
Password: парольприложения
```

Другие порты:
```
587 (TLS)
465 (SSL)
25 (TLS/SSL)
```
