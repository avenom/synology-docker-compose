# IT-Tools

[IT-Tools](https://github.com/CorentinTh/it-tools) — это коллекция удобных онлайн-инструментов для разработчиков и людей, работающих в сфере IT.

---

1. Создайте в File Station следующую структуру папок:

```
/docker/it-tools/
```

2. Создайте в Container Manager новый проект с названием it-tools, выберите путь /docker/it-tools/, выберите в источнике "Создать docker-compose.yml", вставьте в окно ниже следующий код:

```
services:
  it-tools:
    image: corentinth/it-tools:latest
    container_name: it-tools
    ports:
      - 8090:80
    restart: always
```

3. IT-Tools будет доступен по адресу:

```
http://synologyip:8090
```
