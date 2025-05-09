# qBittorrent

[qBittorrent](https://github.com/qbittorrent/qBittorrent) — это торрент-клиент с открытым исходным кодом, предлагающий мощный функционал без рекламы.

---

1. Создайте в File Station следующую структуру папок:

```
/docker/qbittorrent/
/docker/qbittorrent/config/
/docker/qbittorrent/downloads/
```

2. Создайте в Container Manager новый проект с названием qbittorrent, выберите путь /docker/qbittorrent/, выберите в источнике "Создать docker-compose.yml", вставьте в окно ниже следующий код:

```
services:
  qbittorrent:
    image: linuxserver/qbittorrent:latest
    container_name: qbittorrent
    environment:
      - PUID=1026
      - PGID=100
      - TZ=Europe/Moscow
      - WEBUI_PORT=9865
    volumes:
      - ./config:/config
      - ./downloads:/downloads
    ports:
      - 9865:9865
      - 6882:6881
      - 6882:6881/udp
    restart: always
```
* [Как узнать ваш PUID и PGID в Synology DSM](https://github.com/avenom/synology-docker-compose#puid)

3. qBittorrent будет доступен по адресу:

```
http://synologyip:9865
```