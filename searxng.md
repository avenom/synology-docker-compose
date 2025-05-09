# SearXNG

[SearXNG](https://github.com/searxng/searxng) — это метапоисковик, агрегирующий результаты из более чем 70 поисковых систем без отслеживания пользователей. 

---

1. Создайте в File Station следующую структуру папок:

```
/docker/searxng/
```

2. Создайте в Container Manager новый проект с названием searxng, выберите путь /docker/searxng/, выберите в источнике "Создать docker-compose.yml", вставьте в окно ниже следующий код:

```
services:
  searxng:
    image: searxng/searxng:latest
    container_name: searxng
    volumes:
      - ./:/etc/searxng
    ports:
      - 5147:8080
    restart: always
```

3. SearXNG будет доступен по адресу:

```
http://synologyip:5147
```