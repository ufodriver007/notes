![[flask@logotyp.us.png|300]]
>[!info] Это микрофреймворк для создания веб-приложений.

#### Документация
[pypi.org](https://pypi.org/project/Flask/)
[Документация](https://flask.palletsprojects.com/en/stable/)
[Мега-Учебник  Flask](https://habr.com/ru/articles/804245/)

#### WSGI(Web Server Gateway Interface)

Основная идея WSGI — простое взаимодействие Python-приложения и веб-сервера. Согласно стандарту, веб-приложение — это просто функция или другой callable объект, которая принимает два аргумента:
- Словарь переменных окружения
- Функцию, которая устанавливает параметры ответа, а возвращает iterable в качестве ответа

Минимальное WSGI-приложение, то есть приложение, которое поддерживает стандарт WSGI, выглядит примерно так:

```python
def app(environ, start_response):
    data = b"Hello, World!\n"
    start_response("200 OK", [
        ("Content-Type", "text/plain"),
        ("Content-Length", str(len(data)))
    ])
    return iter([data])
```

Все современные веб-фреймворки в Python поддерживают стандарт WSGI, то есть предоставляют на верхнем уровне этот `callable` объект — `app` в Flask, wsgi модуль в Django. А все WSGI-серверы умеют с этим объектом работать. Некоторые веб-серверы, как `nginx`, не поддерживают стандарт, но все равно умеют общаться с WSGI совместимыми (gunicorn, uWSGI)

#### Установка
```bash
pip install Flask
```

```python
from flask import Flask
```

#### Это callable WSGI-приложение
```python
app = Flask(__name__)          # указываем имя своего приложения, чтобы Flask знал, где искать шаблоны и статические файлы

@app.route('/')
def hello_world():
    return 'Welcome to Flask!'
```


Запуск `dev` сервера:
```bash
flask --app example --debug run --port 8000
```


##### Методы HTTP
1) Можно передавать как аргумент декоратора route:
```python
@app.route('/', methods=['GET', 'POST'])
```
2) Можно использовать отдельные декораторы:
```python
@app.get('/')
    # или
@app.post('/')
```

Посмотреть карту маршрутов:
```bash
export FLASK_APP=example:app   # функция app в example.py
python -m flask routes
```


#### Request и response
И запрос и ответ в Flask представлены двумя объектами, обратиться к которым можно внутри каждого
 обработчика маршрута. Глобальный объект `request` используется, чтобы извлекать данные запроса.
   
```python
from flask import request

@app.route('/')
def hello_world():
    print(request.headers) # Выводит все заголовки
    return 'Hello, World!'
```

`response`, чтобы формировать ответ и в процессе работы наполнять данными — заголовками и телом. 
По умолчанию Flask самостоятельно формирует объект `response`, определяет формат возвращаемого контента из обработчика и подставляет нужный формат данных в заголовке:
```python
from flask import render_template

@app.route('/json/')
def json():
    return {'json': 42} # Возвращает тип application/json

@app.route('/html/')
def html():
    return render_template('index.html') # Возвращает тип text/html
```

`request.args['name']` – извлекает значение конкретного параметра. Если параметр не найден, то приложение вернет ошибку 400
`request.args.get(name, default=None, type=None)` – извлекает значение конкретного параметра в формате строки. Если параметр не найден, приложение продолжает работу, а метод вернет default. С помощью type можно привести параметр к определенному типу.
 Если привести к указанному типу невозможно, возникнет исключение ValueError


#### Динамические маршруты
```python
@app.route('/courses/<id>')
def courses(id):
    return f'Course id: {id}'
```


#### ШАБЛОНИЗАТОР(Jinja2)
```python
from flask import render_template

@app.route('/users/<id>')
def users(id):
    return render_template(
        'index.html',if user['name'] == '':
        name=id,
    )
```

Функция `render_template()` выполняет рендеринг указанного шаблона и добавляет результат в ответ.
 Сама функция принимает на вход два параметра:
 - Путь до нужного шаблона
 - Контекст — набор именованных аргументов, который будет доступен внутри шаблона. Сюда можно передавать все что угодно

Далее добавляем файл `templates/index.html` со следующим содержимым:
 ```jinja2
<h1>Hello, {{ name }}</h1>
```

```jinja2
{{ ... }} — для вывода переменных
{% ... %} — для управления логикой шаблона
{# ... #} — для комментариев
```

#### Поисковая форма
```html
<form action="/search" method="get">
</form>

<input type="search" required name="term">
</form>
```

#### Редирект
```python
@app.post('/users')
def users_post():
    return redirect('/users', code=302)
```


#### Именованные маршруты
Этот подход лучше чем хардкодить маршуруты, т.к. если структура изменится, то придётся вручную икать и исправлять в шаблонах все пути.
```python
from flask import url_for

@app.route('/users/<id>')
def users_page(id):
    pass

@app.route('/')
def index():
    return redirect(url_for('users_page', id=3), code=302)
```


#### Flash
```python
from flask import flash

@app.post('/foo')
def foo():
    # Добавление флеш-сообщения.
    # Оно станет доступным только на следующий HTTP-запрос.
    # 'success' — тип флеш-сообщения. Используется при выводе для форматирования.
    # Например, можно ввести тип success и отражать его зеленым цветом.
    flash('This is a message', 'success')
    return redirect('/bar')

@app.get('/bar')
def bar():
    # Извлечение flash-сообщений, которые установлены на предыдущем запросе
    messages = get_flashed_messages(with_categories=True)
    print(messages)  # => [('success', 'This is a message')]
    return render_template(
        'bar.html',
        messages=messages,
    )
```

В шаблоне выводим flash:
```jinja2
<!-- templates/bar.html -->
{% if messages %}
  <ul class=flashes>
  {% for category, message in messages %}
    <li class="{{ category }}">{{ message }}</li>
  {% endfor %}
  </ul>
{% endif %}
```


В основном используют следующие:
- `success` — для удачно завершившихся действий
- `warning` — для предупреждений
- `error `— в тех ситуациях, когда произошла ошибка


#### Общепринятные маршруты и методы CRUD

|Метод|     Маршрут |              Шаблон   |           Описание|
|---|---|---|---|
|GET |      /schools      |       schools/index.html   | Список школ|
|GET  |     /schools/{id}  |      schools/show.html    | Информация о школе
|GET  |     /schools/new  |       schools/new.html    |  Форма создания новой школы
|POST |     /schools       |                           | Создание новой школы
|GET  |     /schools/{id}/edit |  schools/edit.html   |  Форма редактирования школы
|PATCH/PUT| /schools/{id}  |                          |  Обновление школы
|DELETE |  /schools/{id}   |                          | Удаление школы


#### Куки
Получение:
```python
foo = request.cookies.get('foo')
```

Установка:
```python
response = make_response(render_template(...))
response.set_cookie('foo', 'the bar')
```

Удаляем cookie 'cart'
```python
resp.set_cookie('cart', 'asdf', max_age=0)
```


#### Сессии
Старт сессии на техническом уровне означает установку специальной куки в браузер. Обычно она содержит идентификатор сессии, который уникален для каждого пользователя. Данные сессии могут храниться где угодно, это зависит от конкретной реализации. В этом одно из ключевых отличий работы с пользователями напрямую через куки или через сессию.
```python
from flask import Flask, session

app = Flask(__name__)

# Секретный ключ, который используется для криптографической подписи файла сессии
app.secret_key = 'BAD_SECRET_KEY'

if session.get('count') is None:
    session['count'] = 0
else:
    session['count'] += 1

print(session['count'])
```

Удаление сессии:
```python
@app.post('/logout')
def logout():
    session.clear()  # или session.pop('user', None)
    return redirect('/')
```


#### Подключение к PostreSQL
```bash
pip install flask psycopg2-binary
```

Создаём init_db.py
```python
import os
import psycopg2

conn = psycopg2.connect(
        host="localhost",
        database="flask_db",
        user=os.environ['DB_USERNAME'],
        password=os.environ['DB_PASSWORD'])

cur = conn.cursor()

cur.execute('DROP TABLE IF EXISTS books;')
cur.execute('CREATE TABLE books (id serial PRIMARY KEY,'
                                 'title varchar (150) NOT NULL,'
                                 'author varchar (50) NOT NULL,'
                                 'pages_num integer NOT NULL,'
                                 'review text,'
                                 'date_added date DEFAULT CURRENT_TIMESTAMP);'
                                 )


cur.execute('INSERT INTO books (title, author, pages_num, review)'
            'VALUES (%s, %s, %s, %s)',
            ('A Tale of Two Cities',
             'Charles Dickens',
             489,
             'A great classic!')
            )


cur.execute('INSERT INTO books (title, author, pages_num, review)'
            'VALUES (%s, %s, %s, %s)',
            ('Anna Karenina',
             'Leo Tolstoy',
             864,
             'Another great classic!')
            )

conn.commit()

cur.close()
conn.close()
```

#### Пример приложения для CI/CD

[[Django, DRF#CI/CD]]
```bash
pip install flask
```

```bash
pip install python-dotenv
```

```python
import hashlib  
import hmac  
from flask import Flask, request, abort  
from dotenv import load_dotenv  
import os  
import subprocess  
  
app = Flask(__name__)  
  
load_dotenv()  
SECRET_TOKEN = os.getenv('SECRET_KEY')  # получаем из .env значение токена для доступа к API  
  
  
def verify_signature(request):  
    signature = request.headers.get('X-Hub-Signature')  # заголовок в котором github шлёт токен  
    if signature is None:  
        abort(403)  
    sha_name, signature = signature.split('=')  
    mac = hmac.new(SECRET_TOKEN.encode(), msg=request.data, digestmod=hashlib.sha1)  
    if not hmac.compare_digest(mac.hexdigest(), signature):  
        abort(403)  
  
  
@app.route('/webhook', methods=['POST'])  
def webhook():  
    verify_signature(request)  
    if request.method == 'POST':  
        subprocess.run(['git', 'pull'], cwd='/root/app_tasks')  
        # Run Django tests  
        test_process = subprocess.run(['/path/to.python', 'manage.py', 'test'], cwd='/path/to/app_tasks')  
  
        # Check if tests passed  
        if test_process.returncode == 0:  
            restart_process = subprocess.run(['sudo', 'systemctl', 'restart', 'gunicorn'])  
            if restart_process.returncode == 0:  
                return 'Updated, tests passed and server restarted', 200  
            else:  
                return 'Updated, tests passed, but failed to restart server', 500  
    else:  
        return 'Invalid request', 400  
  
  
if __name__ == '__main__':  
    app.run(host='0.0.0.0', port=5000)
```

###### Реальный пример
```bash
pip install GitPython
```

```python
from datetime import datetime  
import os  
import subprocess  
import time  
import threading  
  
from flask import Flask, request  
from dotenv import load_dotenv  
from loguru import logger  
from git import Repo  
  
  
load_dotenv()  
app = Flask(__name__)  
GITFLIC_TOKEN = os.getenv('GITFLIC_TOKEN')  
PRIVATE_SSH_KEY_PATH = os.getenv('PRIVATE_SSH_KEY_PATH')  
REPO_DIR = os.getcwd()  
  
logger.remove()  
logger.add('logs/webhook.log', rotation='10mb', level='DEBUG')  
  
# Глобальная блокировка для предотвращения одновременных запросов  
rebuild_lock = threading.Lock()  
  
  
def git_pull() -> bool:  
    try:  
        logger.info('Git pull...')  
  
        os.environ["GIT_SSH_COMMAND"] = f"ssh -i {PRIVATE_SSH_KEY_PATH} -o IdentitiesOnly=yes"              repo = Repo(REPO_DIR)  
        current_commit = repo.head.commit.hexsha  
        repo.remotes.origin.pull()  
  
        new_commit = repo.head.commit.hexsha  
        if current_commit != new_commit:  
            logger.info("Успешно!")  
            return True  
        else:  
            logger.info("Изменений нет")  
            return False  
  
    except subprocess.CalledProcessError as e:  
        logger.error(f"Ошибка Git: {e.stderr}")  
        return False  
    except Exception as e:  
        logger.error(f"Общая ошибка: {str(e)}")  
        return False  
  
  
def rebuild_and_restart() -> bool:  
    start_time = time.time()  
    result = git_pull()  
    if not result:  
        return False  
  
    try:  
        logger.info('Git pull...')  
        subprocess.run(  
            ['git', 'pull'],  
            capture_output=True,  
            check=True,  
            text=True  
        )  
  
        # Останавливаем старый контейнер  
        logger.info('Stopping old container...')  
        subprocess.run(  
            ['docker-compose', 'down'],  
            capture_output=True,  
            check=True,  
            text=True  
        )  
  
        # Удаляем образы, которые не используются и старше часа  
        logger.info('Removing old Docker images...')  
        subprocess.run(  
            ['docker', 'system', 'prune', '-a', '-f'],  
            capture_output=True,  
            check=True,  
            text=True  
        )  
  
        logger.info('Removing old static volume...')  
        subprocess.run(  
            ['docker', 'volume', 'rm', 'kct_cabinet_static_volume'],  
            capture_output=True,  
            check=True,  
            text=True  
        )  
  
        # Запускаем новый контейнер из собранного образа  
        logger.info('Starting new container...')  
        subprocess.run(  
            ['docker-compose', 'up', '-d'],  
            capture_output=True,  
            check=True,  
            text=True,  
            timeout=600  
        )  
  
        logger.info(f'Rebuilding complete. Time spent: {(time.time() - start_time):.2f}sec')  
  
        return True  
    except subprocess.TimeoutExpired:  
        logger.error(f"Timeout exception. Retry again...")  
        rebuild_and_restart()  
    except subprocess.CalledProcessError as e:  
        logger.error(f"Error: {e.stderr}")  
        return False  
    except Exception as e:  
        logger.error(f"Unexpected error: {str(e)}")  
        return False  
  
  
@app.route('/webhook', methods=['POST'])  
def webhook():  
    if not rebuild_lock.acquire(blocking=False):  
        logger.warning('Rebuild in progress. New request rejected.')  
        return {'Status': 'busy', 'Message': 'Rebuild in progress'}, 423  # HTTP 423 Locked  
  
    try:  
        token = request.headers.get('Authorization')  
  
        if token != GITFLIC_TOKEN:  
            logger.warning(f'Unauthorized request ({datetime.now()}) from {request.headers.get("X-Forwarded-For") or request.remote_addr}')  
            return {'Status': 'Unauthorized'}, 401  
  
        logger.info('New GitFlic event')  
        logger.info(request.get_json())  
  
        if rebuild_and_restart():  
            return {'Status': 'ok'}, 200  
        return {'Status': 'fail'}, 500  
    finally:  
        # Освобождаем блокировку после завершения обработки  
        rebuild_lock.release()  
  
  
if __name__ == '__main__':  
    app.run(host='0.0.0.0', port=5051)
```


