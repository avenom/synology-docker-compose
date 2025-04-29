# Vaultwarden

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