
![[docker.png]]
Установка
```
sudo apt install docker*          (из репозитория Linux Mint)
```
Удаление
```
sudo apt autoremove docker* --purge
```

>[!info] Регистр - место хранения образов(например DockerHub)

>[!info] Образы - это готовые решения. Их нельзя изменять. Например образы python3.11, nginx, ubuntu. Запустить образ без контейнера невозможно(он может создастся автоматически)

>[!info] Контейнер - это набор из образов готовый к запуску

|Команда|Описание|
|-------|-----------|
|`sudo usermod -aG docker ufodriver`| Добавить ufodriver в группу docker
|`id ufodriver`| Посмотреть группы, в которых состоит ufodriver
|`docker run -d -p 80:80 docker/getting-started`|запустить образ в фоновом режиме(-d) и связываем порты(-p), т.е. порт localhost будет ассоциироваться с портом в образе. Если проект не будет найден, он будет скачан
|`docker run -d mysql -e PASS=12345`|создать и запустить контейнер в фоновом режиме(-d) с переменной окружения
|`docker run --name test docker/getting-started`|создать и запустить контейнер с именем test
|`docker info`|инфо общая
|`docker images`|инфо о всех образах
|`docker ps`|инфо о запущенных контейнерах(docker ps -a все контейнеры)
|`docker login`|авторизация в докере. можно пользоваться DockerHub
|`docker logout`|разлогиниться
|`docker pull openjdk`|скачать образ java
|`docker pull openjdk:11`|скачать образ java версии 11
|`docker run -it --name MyJava openjdk`|запустить образ openjdk в интерактивном режиме с именем контейнера MyJava
|`docker start MyJava`|запустить контейнер MyJava
|`docker pause MyJava`|поставить на паузу контейнер
|`docker unpause MyJava`|снять с паузы контейнер
|`docker kill MyJava`|выйти из контейнера аварийно(он помечается удалённым)
|`docker start --rm MyJava`|запустить контейнер MyJava, который помечен удалённым
|`docker stop 13de40040048`|остановка контейнера по ID(можно посмотреть в docker ps)
|`docker restart 13de40040048`|перезапуск контейнера по ID
|`docker image rm 13de40040048`|удалить контейнер по ID
|`docker rmi Image1`|удалить образ
|`docker system df -v`|подробная инфо о системе докер, занимаемое место
|`docker run ubuntu arch`|выполнение контейнера с командой
|`docker exec -it MyNginx /bin/bash`|выполнение /bin/bash в контейнере MyNginx в интерактивном режиме
|`docker inspect 13de40040048`|подробная инфо по контейнеру, в т.ч порты и ip
|`docker stats 98e57aea7263`|ресурсы, потребляемые контейнером
|`docker logs -f 98e57aea7263`|логи, переданные контейнером докеру в реальном времени
|`docker system prune -a --volumes`|удалить все котейнеры и тома

>[!info] `run` - создание контейнера и запуск. `start` - запуск контейнера

#### Dockerfile
Описание только одного образа.
```
FROM openjdk:11          # образ
COPY . /usr/src/myapp    # какие файлы из нашего проекта копируем в образ
WORKDIR /usr/src/myapp   # рабочая директория(после запуска автоматически попадаем в эту директорию)
RUN javac Main.java      # выполнить комманду при первом запуске контейнера
CMD ["java", "Main"]     # выполнять каждый раз при запуске контейнера(комманда "java Main")
EXPOSE 8001              # порт в образе
ENV PASS=12345           # переменные окружения
```
Затем:
```
docker build .                      # сбилдить образ используя Dockerfile в текущей директории
docker build -t my-app:1.01 .       # сбилдить образ используя Dockerfile в текущей директории и
                                    #   указать имя my-app
docker tag 94c5f968ae9f myimage:v01 # после билда образа так можно изменить его название и версию
docker image inspect 94c5f968ae9f   # можно посмотреть какие комманды используются в контейнере
```

#### docker-compose.yml
Описание нескольких образов
```
version: '3.1'

services:                                        # список образов
  php:                                           # просто имя
    build: ./php                                 # путь к уже готовому Dockerfile

  db:                                      
    image: mariadb:10.6                          # точное название образа
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: notSecureChangeMe
  volumes:
    - /opt/web/html:/var/www/html
    - /opt/web/pics:/var/www/pictures

  phpmyadmin:
    image: phpmyadmin
    restart: always
    ports:
      - 8080:80
    environment:
      - PMA_ARBITRARY=1
    depends_on:                                   # запускать только после
      - db
      - php

networks:
  default:
    driver: bridge
    name: webnet
```
Затем:
```
docker-compose build               # сбилдить образ используя файл docker-compose.yml
docker-compose up -d               # запустить(если образов нет, они будут скачаны) в фоне
docker-compose down                # остановить
docker-compose logs -f             # логи
```

#### Выгрузка своего образа в DockerHub
```
docker build -t ufodriver007/my-app .  # сбилдить образ используя Dockerfile в текущей директории и
                                       #   указать имя my-app
docker push ufodriver007/my-app        # запушить образ в DockerHub
```

#### Docker volumes
Для сохранения настроек

```
docker volume ls                                    # список томов
docker run -v /opt/mysql_data:/var/lib/mysql mysql  # создать и запустить контейнер с хост-томом
                                                    #  (директория хоста:директория контейнера)
docker run -v /var/lib/mysql mysql                  # создать и запустить контейнер с анонимным
                                                    #   томом (директория контейнера)
docker run -v mysql_data:/var/lib/mysql mysql       # создать и запустить контейнер с именным томом
                                                    #   (имя:директория контейнера)
```

>[!info] Директории хост машины монтируются в контейнер. Докер хранит тома(анонимные и именные) по адресу `/var/lib/docker/volumes/`

```
docker volume create test          # создать именной том test
docker volume rm test              # удалить именной том test
```

Пример `docker-compose.yml`:
```
version: '3'

services:
 django:
  build: .
  container_name django
  command: python manage.py runserver 0.0.0.0:8000
  volumes:
    - .:/usr/src/app                           # текущая папка связывается с папкой хоста /usr/src/app

 pgdb:
  image: postgres
  environment:
   - POSTGRES_DB=postgres
   - POSTGRES_USER=postgres
   - POSTGRES_PASSWORD=postgres
  container_name: pgdb
  volumes:
   - pgdbdata:/var/lib/postgresql/data

volumes:
 pgdbdata:null
```

Выполнение команд через докер:
```
docker-compose run django django-admin startproject myapp .
docker-compose run django python manage.py migrate
```

#### Сети
>[!info] Интерфейс docker по-умолчанию docker0

|Команда|Описание|
|-------|-----------|
|`docker network ls`|список сетей
|`docker network inspect NAME`|подробная информация о сети
|`docker network rm NAME`|удалить сеть
|`docker run nginx --network=host`|отдельный хост, изолированные контейнеры(если не создать сеть заранее)
|`docker run nginx --network=none`|без интернета, изолированные контейнеры, доступ только локально
|`docker run nginx --network=macvlan`|каждому контейнеру даётся виртуальная сетевая карта и свой ip и mac
|`docker run nginx --network=ipvlan`|каждому контейнеру даётся виртуальная сетевая карта и свой ip(mac общий)

>[!info] Драйвер по-умолчанию - bridge

```
docker network create ---driver macvlan --subnet 192.168.100.0/24 --gateway 192.168.100.1 myMACvlan
docker network create --driver bridge NAME
docker run --net NAME nginx

docker network connect myNet01 container2                   # подключиться к сети
docker network disconnect dosahd843274shd2398sd container2  # отключиться от сети по NetworkID (смотреть docker network inspect NAME)
```

