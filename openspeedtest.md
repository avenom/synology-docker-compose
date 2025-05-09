# OpenSpeedTest

[OpenSpeedTest](https://github.com/openspeedtest/Speed-Test) — это программа для тестирования скорости интернета, работающая на HTML5 без использования Flash или Java, позволяющяя измерять стабильную скорость загрузки и выгрузки через любой современный веб-браузер.

---

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
