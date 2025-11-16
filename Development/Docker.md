
![[docker.png]]
Установка
```bash
sudo apt install docker*          # (из репозитория Linux Mint)
```
Удаление
```bash
sudo apt autoremove docker* --purge
```

>[!info] Регистр - место хранения образов(например DockerHub)

>[!info] Образ — это неизменяемый шаблон, который содержит все необходимые файлы, библиотеки и настройки для запуска приложения. Это своего рода "чертеж" для создания контейнера. Это описание того что должно быть установлено, инструкции для настройки окружения(готовые решения). Их нельзя изменять. Например образы python3.11, nginx, ubuntu. Запустить образ без контейнера невозможно(он может создастся автоматически)

>[!info] Контейнер — это запущенный экземпляр образа(или набор из образов). Это изолированная среда, в которой выполняется приложение, определенное в образе.

>[!info] Также существует файл `.dockerignore` работающий по аналогии с `.gitignore`

Чтобы выполять команды без `sudo`,  нужно:
1. Добавить группу `docker`
```
sudo groupadd docker
```
2. Добавить пользователя в группу `docker`
```
sudo gpasswd -a $USER docker
```
3. Чтобы активировать изменения в группах
```
newgrp docker
```
Или же просто выйдите из системы

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
|`docker volume ls`|список томов
|`docker volume prune`|удалить неиспользуемые тома
|`docker attach 13de40040048`|позволяет передать указанный активный контейнер под интерактивное управление или увидеть его стандартный вывод
|`docker rename dcf8cda72903 ubuntu_main`|позволяет переименовать уже запущенный контейнер

>[!info] `run` - создание контейнера и запуск. `start` - запуск контейнера

![[docker_commands.jpg]]

#### Dockerfile
Описание только одного образа.
```docker
# образ
FROM python:3.11   
# оболочка(-с далее идёт команда, которую нужно выполнить)
SHELL ["/bin/bash", "-c"]  
# Python не будет пытаться писать файлы .pyc
ENV PYTHONDONTWRITEBYTECODE=1  
# Запрещает вывод stdout и stderr буферезировать
ENV PYTHONUNBUFFERED=1         

# выполнить комманду при первом запуске контейнера
# получается "/bin/bash -с pip install --upgrade pip"
RUN pip install --upgrade pip  
                               
RUN apt update && apt -qy install gcc cron libjpg-dev
RUN useradd -rms /bin/bash ufo && chmod 777 /opt /run

# рабочая директория(после запуска автоматически попадаем в эту директорию)
WORKDIR /ufo   

# какие файлы из нашего проекта копируем в образ
COPY --chown=ufo:ufo . .    
# какие файлы из нашего проекта копируем в образ
ADD . /code/                

# задание переменной окружения
ENV APP_NAME=DOCKER_DEMO    

RUN mkdir /ufo/static && mkdir /ufo/media && chown -R ufo:ufo /ufo && chmod 775 /ufo
RUN pip install -r requirements.txt
# переключаемся на пользователя ufo
USER ufo                 
# выполнять каждый раз при запуске контейнера
CMD gunicorn docker_demo.wsgi:application -b 0.0.0.0:8000  

# порт в образе
EXPOSE 8000              
```
Затем:
```bash
docker build .                      # сбилдить образ используя Dockerfile в текущей директории
docker build -t docker-demo:1.01 .  # сбилдить образ используя Dockerfile в текущей директории и
                                    #   указать имя my-app
docker run -p 8084:8000 docker-demo # выполнить сбилженный контейнер с пробросом портов (8084 на
                                    #   хосте и 8000 на виртуалке)
docker tag 94c5f968ae9f myimage:v01 # после билда образа так можно изменить его название и версию
docker image inspect 94c5f968ae9f   # можно посмотреть какие комманды используются в контейнере
```
Пример nginx докерфайла для образа(для этого нужно предварительно создать `nginx.conf` и  `proxy_params` файлы)
```docker
FROM nginx:latest

RUN rm /etc/nginx/conf.d/default.conf
COPY nginx.conf /etc/nginx/conf.d
COPY proxy_params /etc/nginx
```

#### docker-compose.yml
>[!info] Docker Compose — это инструмент для определения и управления многоконтейнерными приложениями. Вместо того, чтобы описывать каждый контейнер по отдельности, вы создаёте один файл (обычно `docker-compose.yml`), описывающий все контейнеры приложения, их зависимости и связи между ними. Это упрощает настройку и запуск сложных приложений, состоящих из нескольких взаимосвязанных контейнеров.

Описание нескольких образов
```yaml
version: '3.9'

services:                                        # список образов
  db:                                      
    image: postgres:15                           # точное название образа
    container_name: ufo_postgres
    restart: always
    volumes:
      - ~/.pg/pg_data/ufo:/var/lib/postgresql/data
    env_file:
      - .env
      
  django_project:
    image: test_django:latest                     # образ сбилдженый из докерфайла
    container_name: test_django
    restart: always
    depends_on:                                   # запускать только после
      - db
    volumes:
      - static_volume:/ufo/static
      - media_volume:/ufo/media
    env_file:
      - .env
    command: >
      bash -c "./manage.py collectstatic --noinput && ./manage.py migrate && gunicorn -b 0.0.0.0:8000 myapp.wsgi:application"

  nginx:
    build:                                        # билдим из докерфайла
      ./Dockerfile
    context: ./docker/nginx/
    container_name: ufo_nginx
    volumes:
      - static_volume:/ufo/static
      - media_volume:/ufo/media
    depends_on:                                   # запускать только после
      - django_project
    ports:
      - "${NGINX_EXTERNAL_PORT}:80"               # из переменной окружения
    profiles:
      - dev                                       # сервис с профилем dev


volumes:
  static_volume:
  media_volume:
  
networks:
  default:
    driver: bridge
    name: webnet
```
Затем:
```bash
docker-compose build               # сбилдить образ используя файл docker-compose.yml
docker-compose build --no-cache    # сбилдить образ, без кеширования
docker-compose up -d               # запустить(если образов нет, они будут скачаны) в фоне
docker-compose up --build          # запустить и ПЕРЕбилдить
docker-compose down                # остановить
docker-compose logs -f             # логи
docker-compose --profile dev up    # запустить сервисы с профилем dev
```

#### Выгрузка своего образа в DockerHub
```bash
docker build -t ufodriver007/my-app .  # сбилдить образ используя Dockerfile в текущей директории
                                       #  и указать имя my-app
docker push ufodriver007/my-app        # запушить образ в DockerHub
docker pull ufodriver007/my-app        # скачать образ из DockerHub
docker run ufodriver007/django_test    # запустить скачанный образ
```

#### Docker volumes
Для сохранения настроек

```bash
docker volume ls                                    # список томов
docker run -v /opt/mysql_data:/var/lib/mysql mysql  # создать и запустить контейнер с хост-томом
                                                    #  (директория хоста:директория контейнера)
docker run -v /var/lib/mysql mysql                  # создать и запустить контейнер с анонимным
                                                    #   томом (директория контейнера)
docker run -v mysql_data:/var/lib/mysql mysql       # создать и запустить контейнер с именным томом
                                                    #   (имя:директория контейнера)
```

>[!info] Директории хост машины монтируются в контейнер. Докер хранит тома(анонимные и именные) по адресу `/var/lib/docker/volumes/`

```bash
docker volume create test          # создать именной том test
docker volume rm test              # удалить именной том test
```

Пример `docker-compose.yml`:
```yaml
version: '3'

services:
 django:
  build: .
  container_name: django
  command: python manage.py runserver 0.0.0.0:8000
  volumes:
    - .:/usr/src/app             # текущая папка связывается с папкой хоста /usr/src/app

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
```bash
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

```bash
docker network create ---driver macvlan --subnet 192.168.100.0/24 --gateway 192.168.100.1 myMACvlan
docker network create --driver bridge NAME
docker run --net NAME nginx

docker network connect myNet01 container2                   # подключиться к сети
docker network disconnect dosahd843274shd2398sd container2  # отключиться от сети по NetworkID (смотреть docker network inspect NAME)
```

