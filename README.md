# Docker Compose для Synology DSM 7.2
<img src="https://github.com/avenom/synology-docker-compose/blob/main/icons/synology-docker-compose.jpg">

В Synology DSM 7.2 пакет Docker получил свое логическое развитие и трансформировался в новый Container Manager с поддержкой проектов Docker Compose, которые позволяют одним конфигурационным файлом скачивать и разворачивать все необходимые для установки образы и контейнеры.

В этом репозитории собраны проверенные на работоспособность проекты Docker Compose для разных сервисов с непересекающимися друг с другом локальными портами. Если у вас эти порты будут заняты, то можете поменять на свободные. Например в **8080:80** локальный порт - **8080**.

Если заметите неточности, то пишите в [телеграм](https://t.me/avenom).

* [Как узнать ваш PUID и PGID в Synology DSM](#puid)
* [Активация Gmail SMTP для контейнеров Docker](#gmail)

| Проект | Порт |
|---|:---:|
|[Audiobookshelf](https://github.com/avenom/synology-docker-compose/blob/main/audiobookshelf.md)|13378|
|[Glances](https://github.com/avenom/synology-docker-compose/blob/main/glances.md)|61208|
|[Home Assistant](https://github.com/avenom/synology-docker-compose/blob/main/homeassistant.md)|8123|
|[Homepage](https://github.com/avenom/synology-docker-compose/blob/main/homepage.md)|3535|
|[Memos](https://github.com/avenom/synology-docker-compose/blob/main/memos.md)|5230|
|[MeTube](https://github.com/avenom/synology-docker-compose/blob/main/metube.md)|8081|
|[Miniflux + PostgreSQL](https://github.com/avenom/synology-docker-compose/blob/main/miniflux.md)|8085|
|[OpenSpeedTest](https://github.com/avenom/synology-docker-compose/blob/main/openspeedtest.md)|3000|
|[RSS-Bridge](https://github.com/avenom/synology-docker-compose/blob/main/rss-bridge.md)|8086|
|[SMTP To Telegram](https://github.com/avenom/synology-docker-compose/blob/main/smtp_to_telegram.md)|2525|
|[Tautulli](https://github.com/avenom/synology-docker-compose/blob/main/tautulli.md)|8181|
|[Uptime Kuma](https://github.com/avenom/synology-docker-compose/blob/main/uptime-kuma.md)|3001|
|[Vaultwarden](https://github.com/avenom/synology-docker-compose/blob/main/vaultwarden.md)|3012, 5151|
|[Wallabag + MariaDB + Redis](https://github.com/avenom/synology-docker-compose/blob/main/wallabag.md)|8082, 3306|

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
