![[Raspberry-Pi-Logo-01-300x300.png]]

## Облако на Nextcloud

1. Идём на [pi-hosted.com/](https://pi-hosted.com/)
2. Устанавливаем Docker
```bash
wget -qO- https://raw.githubusercontent.com/pi-hosted/pi-hosted/master/install_docker.sh | bash
```
3. Перезагружаем raspberry pi
4. Устанавливаем Portainer
```bash
wget -qO- https://raw.githubusercontent.com/pi-hosted/pi-hosted/master/install_portainer.sh | bash
```
5. Заходим в интерфейс Portainer `127.0.0.1:9000` и создаём учётку
6. Заходим в настройки и в `App Templates` вписываем
```
https://raw.githubusercontent.com/pi-hosted/pi-hosted/master/template/portainer-v2-arm64.json
```
7. Заходим в `App Templates`(`127.0.0.1:9000/#!/2/docker/templates`) и находим Nextcloud и устанавливаем его. Там надо будет вписать таймзону `Europe/Moscow`, `DATABASE_PASSWORD` и `MYSQL_ROOT_PASSWORD` и порт поставить `5443`. Нажать `Deploy this stack`
8. Дальше можно будет подключиться `127.0.0.1:5443` и начать пользоваться хранилищем

#### Доверенные домены
Чтобы получить доступ извне, нужно в конфиг добавить доверенные домены
1. пишем `docker ps` в малине, чтобы получить список всех запущенных контейнеров
2. Заходим в bash в nextcloud контейнере
```bash
docker exec -it <container ID> /bin/bash
```
3. Редактируем файл конфига в контейнере
```bash
nano /config/www/nextcloud/config.php
```

**Место хранения файлов в Nextcloud(установленный через докер и Portainer):**
```
/portainer/Files/AppData/Config/Nextcloud/Data/ufodriver/files
```
