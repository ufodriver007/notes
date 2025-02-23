![[nginx.png]]
установить nginx
```
sudo apt-get install nginx
```

остановить nginx
```
sudo service nginx stop
```

запустить nginx
```
sudo service nginx start
```

>[!info] Место хранения сайта по умолчанию `/var/www/html`

разрешить с других устройств подключаться к серверу по порту 80
```
sudo ufw allow 'Nginx HTTP'
```

разрешить с других устройств подключаться к серверу по порту 443
```
sudo ufw allow 'Nginx HTTPS'
```

сервер, исполняющий Python код
```
sudo apt install uvicorn
```

менеджер процессов для uvicorn
```
sudo apt instal gunicorn
```

запустить uvicorn и слушать все IP
```
uvicorn server:app --host 0.0.0.0

```

создаём конфиг
```
sudo nano /etc/nginx/sites-enabled/demo.conf
```

пишем в этот конфиг
```
server {
    server_name demo.ru;
    listen 80;
    location / {
        include proxy_params;
        proxy_pass http://127.0.0.1:8000      # проксировать все запросы на сайт на 127.0.0.1:8000
    }
}
```

#### Пишем сервис для systemd
```
sudo nano /etc/systemd/system/authdemo.service
```

пример конфига для сервиса
```
[Unit]
Description=Gunicorn instance to serve Authdemo app
After=network.target

[Service]
User=www
Group=www-data
WorkingDirectory=/home/www/code/authdemo
Environment="PATH=/home/www/code/authdemo/env/bin"
ExecStart=/home/www/code/authdemo/env/bin/gunicorn -w 5 -k uvicorn.workers.UvicornWorker server:app

[Install]
WantedBy=multy-user.target
```

т.е. запускаем gunicorn и 5 процессов(workers) и процесс, который непосредственно будет работать(UvicornWorker) и само приложение server:app(FastAPI)

```
sudo nginx -s reload                          перезагружаем конфиг nginx
sudo systemctl enable authdemo                включаем
sudo systemctl start authdemo                 стартуем сервис
sudo systemctl status authdemo                смотрим статус
```

>[!info] Логи nginx можно посмотреть здесь `/var/log/nginx/error.log`

#### SSL
SSL покупаем wildcard, он действует на все поддомены
копируем файл(а потом и остальные) сертификата на свой удалённый сервер
```
scp root_pem_thawte_ssl123_1.crt www@authdemo.ru
```

Создаём директорию `/etc/nginx/ssl` и перемещаем туда все файлы сертификатов
Создаём бандл
```
sudo cat authdemo_ru_2022_04_04.crt intermediate_pem_thawte_ssl123_1.crt root_pem_thawte_ssl123_1.crt > /tmp/bundle.crt

sudo mv /tmp/bundle.crt .
```
в файле смотрим, чтобы между end certificate и begin certificate был символ переноса строки

Добавляем в конфиг nginx
```
sudo nano /etc/nginx/sites-enabled/demo.conf
```
```
server {
    server_name authdemo.ru;
    listen 443;
    ssl on;
    ssl_certificate /etc/nginx/ssl/bundle.crt
    ssl_certificate_key /etc/nginx/ssl/private.key
    location / {
        include proxy_params;
        proxy_pass http://127.0.0.1:8000                    проксировать все запросы на сайт на 127.0.0.1:8000
    }
}
```

перезагружаем конфиг nginx
```
sudo nginx -s reload
```

можно проверить на сайте [sslshopper.com](https://www.sslshopper.com/)

И наконец настраиваем автоматическую переадресацию на ssl. В конфиге nginx
```
sudo nano /etc/nginx/sites-enabled/demo.conf
```

в верхней секции добавляем `return 301 https://$host$request_uri;`
```
server {
    server_name authdemo.ru;
    listen 443;
    ssl on;
    ssl_certificate /etc/nginx/ssl/bundle.crt
    ssl_certificate_key /etc/nginx/ssl/private.key
    location / {
        include proxy_params;
        proxy_pass http://127.0.0.1:8000      проксировать все запросы на сайт на 127.0.0.1:8000
    }
    return 301 https://$host$request_uri;     т.е. эта страница постоянно перемещена на новый адрес
}
```

Максимальный размер тела клиентских запросов `/etc/nginx/nginx.conf`
```
server {
    ...
    client_max_body_size 100M;
    ...
}
```

###### CertBot
Сертификат от certbot на 3 месяца

```
sudo apt install certbot python3-certbot-nginx
sudo certbot --nginx
```

Далее вводим свой email(для напоминания об истекающем сроке действия) и выбираем домен.  
Certbot сам исправит конфиг nginx и поставит редиректы.

###### Zerossl
[Выдаёт сертификаты с привязкой к IP](https://zerossl.com/)

#### Блокировка по ip
Создаём blacklist
```
sudo nano /etc/nginx/blacklist.conf
```

Наполняем его
```
deny 192.168.1.0/24;
deny 192.168.2.102;
allow all;
```

В файл конфигурации nginx `/etc/nginx/sites-available/default` вписываем
```
include /etc/nginx/blacklist.conf;
```

Меняем владельца
```
sudo chown www-data.www-data /etc/nginx/blacklist.conf
```

Перезагружаем Nginx
```
sudo service nginx restart
```

>[!info] Whitelist создаётся по тому же принципу, только описываются разрешённые ip, а все остальные запрещаются.

```
allow 1.2.3.4;
allow 5.6.7.0/24;
...
deny all;
```