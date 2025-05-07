# Home Assistant

[Home Assistant](https://github.com/home-assistant/core) — это бесплатная и открытая система для автоматизации умного дома, позволяющая интегрировать устройства различных производителей, создавать сценарии автоматизации и управлять ими через веб-интерфейс, мобильные приложения или голосовые команды, при этом обеспечивая локальный контроль и высокий уровень конфиденциальности.

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
