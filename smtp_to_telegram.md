# SMTP To Telegram

[SMTP To Telegram](https://github.com/KostyaEsmukov/smtp_to_telegram) — это SMTP-шлюз, который отправляет все входящие сообщения электронной почты в бота Telegram. Например можно настроить отправку уведомлений из Synology DSM в вашего телеграм-бота, прописав пользовательский SMTP-сервер 0.0.0.0 с портом 2525.

---

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
