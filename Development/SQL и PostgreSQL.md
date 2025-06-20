![[postgresql_logo.png]]

>[!info] Транзакция - пакет запросов, которые либо выполняются все, либо не выполняется ничего из пакета.

>[!info] Нормализация БД — это процесс организации данных определенным образом и рекомендации по проектированию. То есть таблицы и связи между ними (отношения) создаются в соответствии с правилами. В результате обеспечивается нужный уровень безопасности данных, а сама база становится более гибкой. Также устраняются несогласованные зависимости и избыточность. 

>[!info] Индекс — это специальная структура данных, позволяющая решать задачу ускорения доступа к строкам в таблице, а также задачу предотвращения дублирования значений ключевых атрибутов в различных строках таблицы. Для реализации первичного ключа (PRIMARY KEY) всегда автоматически создается индекс.

#### Реляционные БД
- PostgreSQL
- MySQL
- SQLite

#### Нереляционные БД
- Redis (InMemory)
- MongoDB

SQL используется для общения с реляционными БД
> ==1 Нормальная форма== - Одна ячейка содержит ОДНО значение
> ==2 Нормальная форма== - каждая запись должна иметь уникальный идентификатор(первичный ключ, id)

#### Связи между таблицами
- Один ко многим
- Один к одному
- Многие ко многим

Пример c sqlite
```python
import sqlite3

db = sqlite3.connect('test_db.sqlite')
cursor = db.cursor()

cursor.execute('''
               CREATE TABLE IF NOT EXISTS users (
               id INTEGER PRIMARY KEY,
                name TEXT NOT NULL,
                email TEXT NOT NULL UNIQUE)''')

cursor.execute("SELECT * FROM users")
result_all = cursor.fetchall()                   # лист кортежей, в каждом - строка из БД

name = 'Джон Фёдор'
cursor.execute("SELECT * FROM users WHERE name=?", (name,))
result = cursor.fetchone()                      # кортеж, одна строка

cursor.executemany("INSERT INTO users (name, email) VALUES (?, ?)", (names, values))

cursor.execute("DELETE FROM data WHERE site=?", (site,))

# выполнить запросы(только для меняющих базу)
db.commit()

db.close()
```

#### Команды
|Пример команды|Описание|
|------------|--------------|
|`DROP DATABASE new`|удалить БД new|
|`INSERT INTO table (id, name) VALUES (1, 'Astra'`)|вставка значений
|`SELECT * FROM table`|выбрать все поля из таблицы
|`SELECT DISTINCT name FROM table`|выбрать уникальные значения поля name в таблице table
|`SELECT * FROM table WHERE alive="ok" LIMIT 2`|выбрать все поля с alive=ok, максимум 2 результата
|`SELECT * FROM table WHERE OFFSET 10 LIMIT 5`|выбрать все поля со сдвигом 10, максимум 5 результатов
|`SELECT * FROM table ORDER BY name`|выборка с указанием порядка сортировки
|`SELECT * FROM table WHERE name in ('Alex', 'Bob', 'Rick')`|выборка всех имен, входящих в кортеж
|`SELECT * FROM table ORDER BY name DESC`|выборка с указанием обратного порядка сортировки
|`SELECT * FROM table WHERE surname LIKE "%ов"`|выборка всех записей у которых surname заканчивается на ов
|`SELECT * FROM table WHERE id > 3 AND id < 45`|выборка записей у которых id > 3, но меньше 45
|`SELECT * FROM table WHERE id < 3 OR id > 45`|выборка записей у которых id < 3 или больше 45
|`SELECT * FROM table WHERE NOT id=2`|выборка записей у которых id не равен 2
|`SELECT * FROM table WHERE age BETWEEN 35 AND 45`|выборка из диапазона значений
|`SELECT email, count(*) FROM table GROUP BY email HAVING COUNT(*) > 1`|выбрать поля email и count(количество каждого email), группировка по кол-ву
|<code>SELECT NOW() - INTERVAL '1 YEAR'</code>|выборка записей с диапазоном дат до года назад
|`ALTER TABLE teacher ADD age INT`|добавление колонки age с типом int
|`UPDATE table SET age = 35 WHERE id=1`|обновление записи
|`DELETE FROM teacher WHERE id = 6`|удаление записи
|`ALTER TABLE employee ADD bicycle_id BIGINT REFERENCES bicycle (id)`|добавить колонку bicycle_id, которая будет ссылаться на таблицу bicycle, колонку id (Внешний ключ)
|`ALTER TABLE employee ADD UNIQUE(bicycle_id)`|добавить ограничение на колонку bicycle_id(только уникальные значения)
|`UPDATE employee SET bicycle_id = 2 WHERE id = 4`|изменить значение в таблице employee в колонке bicycle_id на 2 у записи, где id = 4

`GROUP BY` - это конструкция, определяющая колонки, по которым будет проводится группировка результатов агрегатных функций. Например
```sql
-- Выборка сумирования цены с группировкой по user_id

SELECT SUM(price) FROM spendings GROUP BY user_id
```

Поскольку `WHERE` нельзя использовать для агрегатных функций, надо использовать `HAVING`(только после `GROUP BY`)
```sql
SELECT SUM(price) FROM spendings GROUP BY user_id HAVING SUM(price) > 10000
```

#### JOIN
==JOIN на примере 2х таблиц(учителя и уроки). Это горизонтальное объединение.==

**INNER JOIN(или просто JOIN)**
Пересечение множеств. Допустим только те учителя, которые ведут уроки.

**LEFT OUTER JOIN**
Все учителя и пересекающиеся с ними уроки.

**RIGHT OUTER JOIN**
Все уроки и пересекающиеся с ними учителя.

**FULL JOIN**
Все значения обоих таблиц.

**UNION**
Вертикальное объединение.

Пример. Выбирает пересечение множеств(учителя и уроки) по связующим колонкам `teacher.id = lesson.teacher.id`
```sql
SELECT teacher.surname, lesson.name FROM teacher INNER JOIN lesson ON teacher.id = lesson.teacher.id
```

Пример. Выборка всех полей и объединение 2х таблиц вертикально
```sql
SELECT * FROM teacher UNION SELECT * FROM lesson
```

##### Подробное объяснение
Для начала будет 2 таблицы:  `teachers` и `subjects`.
Поле `subject` в таблице `teachers` - это внешний ключ на поле `id` в таблице `subjects`.
Учителя
![[teachers.png]]

Уроки
![[2025-04-18_02-52.png]]

Пример самого простого объединения `INNER JOIN(или просто JOIN)`
```sql
SELECT * FROM teachers JOIN subjects ON teachers.subject = subjects.id;
/*
Выбираем ВСЕ поля из выборки (объединение teachers и subjects)
После ON указываем СПОСОБ соединения
*/ 
```

Результат
![[2025-04-18_03-04.png]]
То есть к каждой записи одной таблицы мы присоединяем запись другой таблицы

Ещё пример запроса `INNER JOIN(или просто JOIN)`
```sql
SELECT teachers.first_name,                 -- имя поля для выборки
       teachers.last_name,                  -- имя поля для выборки
       subjects.name,                       -- имя поля для выборки
       subjects.cabinet                     -- имя поля для выборки
  FROM teachers                             -- из какой таблицы (считается левой)
       JOIN subjects                        -- с какой таблицей соединяем (считается правой)
        ON teachers.subject = subjects.id;  -- после ON описывается способ соединения
```

Результат
![[2025-04-18_02-55.png]]

![[2025-04-18_03-49.png]]

OUTER JOIN - это пересечение множеств + одна из не пересекающихся частей
Пример запроса (У двух записей teachers обнулены внешние ключи)
```sql
SELECT * FROM teachers RIGHT OUTER JOIN subjects ON teachers.subject = subjects.id
/*
Выбираем ВСЕ поля из выборки (объединение всех subjects и связанных с ними teachers)
После ON указываем СПОСОБ соединения
*/
```

Результат
![[2025-04-18_03-54.png]]

#### Функции

|Пример функции|Описание|
|----------|-------------|
|`SELECT VERSION()`|возвращает версию БД|
|`SELECT AVG(age) FROM teacher`|среднее значение колонки
|`SELECT MAX(age), MIN(age) FROM teacher`|максимальное и минимальные значения колонки
|`SELECT SUM(age) FROM teacher`|сумма всех значений колонки
|`SELECT COUNT(age) FROM teacher`|количество записей
|`SELECT age, COUNT(age) FROM teacher GROUP BY age`|выборка по age с группировкой по кол-ву записей
|`SELECT title, body FROM news ORDER BY RANDOM() LIMIT 1`|рандомная запись
|`ROUND()`|округление
|`NOW()`|дата и время сейчас
|`NOW()::DATE`|дата сейчас

#### AIOSQLite
>[!info] Асинхронная обёртка для SQLite

[Документация](https://aiosqlite.omnilib.dev/en/stable/)
```bash
pip install aiosqlite
```

Пример использования в AIOgram
```python
import aiosqlite

# Создаем соединение с базой данных  
_db_connection = None  
  
async def init_db():  
    global _db_connection  
    _db_connection = await aiosqlite.connect('bot_db.sqlite')  
    await _db_connection.execute('''  
           CREATE TABLE IF NOT EXISTS users (           id INTEGER PRIMARY KEY,            telegram_id TEXT NOT NULL,            current_uid TEXT NOT NULL UNIQUE)''')  
    await _db_connection.commit()  
  
async def get_db_connection():  
    global _db_connection  
    if _db_connection is None:  
        await init_db()  
    return _db_connection  
  
async def select_all() -> list[tuple[str, str]]:  
    db = await get_db_connection()  
    try:  
        async with db.execute("SELECT telegram_id, current_uid FROM users") as cursor:  
            result = await cursor.fetchall()  
            return [(str(res[0]), str(res[1])) for res in result]  
    except Exception as e:  
        logger.error(f"Ошибка при получении всех пользователей в БД {e}")  
        return []  
  
async def create_user(tg_id: str, current_uid: str):  
    db = await get_db_connection()  
    try:  
        async with db.execute(f"INSERT INTO users (telegram_id, current_uid) VALUES (?, ?)", (tg_id, current_uid)) as cursor:  
            await db.commit()  
    except Exception as e:  
        logger.error(f"Ошибка при создании пользователя в БД {e}")  
  
async def update_current_uid(tg_id: str, current_uid: str):  
    db = await get_db_connection()  
    try:  
        async with db.execute("UPDATE users SET current_uid=? WHERE telegram_id=?", (current_uid, tg_id)) as cursor:  
            await db.commit()  
    except Exception as e:  
        logger.error(f"Ошибка при обновлении пользователя в БД {e}")  
  
async def delete_user(tg_id: str):  
    db = await get_db_connection()  
    try:  
        async with db.execute("DELETE FROM users WHERE telegram_id=?", (tg_id,)) as cursor:  
            await db.commit()  
    except Exception as e:  
        logger.error(f"Ошибка при удалении пользователя в БД {e}")  
  
async def get_current_uid(tg_id: str) -> Optional[str]:  
    db = await get_db_connection()  
    try:  
        async with db.execute("SELECT current_uid FROM users WHERE telegram_id=?", (tg_id,)) as cursor:  
            result = await cursor.fetchone()  
            if result:  
                return str(result[0])  
            else:  
                return None  
    except Exception as e:  
        logger.error(f"Ошибка при получении current_uid из БД {e}")  
        await cursor.close()  
        return None
```

Вызываем `init_db()` в функции `main()`
```python
async def main() -> None:
    await init_db()
```

#### PostgreSQL
Установка
```bash
sudo apt install postgresql postgresql-contrib
```

Статус демона
```bash
sudo service postgresql status
```

Отключить сервис postgres
```bash
/etc/init.d/postgresql stop
```

Включить сервис postgres
```bash
/etc/init.d/postgresql start
```

Версия
```bash
pg_config --version
```

```bash
sudo systemctl status postgresql                # проверка, что служба включена
sudo pg_isready                                 # проверка, принимает ли входящие соединения
sudo -i -u postgres                             # переключитесь на пользователя postgres
createdb bot_users                              # создать БД
dropdb bot_users                                # удалить БД
psql                                            # консольный клиент postgres(SQL запросы)
```

|Команда psql|Описание|
|----------|-------------|
|`\l`|список БД
|`\q`|выход
|`\?`|хелп
|`\c bot_users`|войти в БД
|`\conninfo`|инфо
|`\d table`|показать структуру таблицы table
|`\dt`|показать таблицы (или `\dt+`)
|`\i somedir/script2.sql`|выполнить sql скрипт(например сгенерированный mockaroo.com)
|`\du`|список пользователей

Примеры SQL запросов для psql
```
ALTER USER postgres WITH PASSWORD 'qwerty';     # установить пароль qwerty пользователю postgres
CREATE USER username WITH PASSWORD 'qwerty';    # создать пользователя username с паролем qwerty
ALTER USER username WITH SUPERUSER;             # дать права суперпользователя username'у
DROP USER username;                             # удалить пользователя
DROP TABLE table;                               # удалить таблицу
GRANT ALL PRIVILEGES ON DATABASE myproject TO myprojectuser;    # Предоставить пользователю БД
                                                                #  права доступа к базе данных
```

#### Типы данных PostgreSQL
|Тип данных|Описание|
|----------|-------------|
|BIGSERIAL|автоинкрементируемое значение (напр. id BIGSERIAL NOT NULL PRIMARY KEY)
|VARCHAR(50)|текст с ограничением 50 символов
|DATE|дата
|NUMERIC(19, 2)|числовое значение 19 цифр до запятой и 2 после
|char(3)|буквы или цифры(3 символа)
|text|текст(без указания макс. длины)
|integer|числовые данные

#### Ограничения PostgreSQL
```sql
NOT NULL
CHECK (range > 0)
PRIMARY KEY (aircraft_code)                 # Первичный ключ. Нужен для уникальной идентификации
                                            #  всех записей таблицы
FOREIGN KEY (aircraft_code)                 # Внешний ключ. Нужен для связи таблиц между собой. 
                                            # Колонка aircraft_code ссылается на
   REFERENCES aircrafts (aircraft_code )    # колонку aircraft_code в таблице aircrafts.
   ON DELETE CASCADE                        # При удалении какой-либо строки из таблицы aircrafts
                                            #  удаление связанных строк из текущей таблицы, 
                                            #  связанных с этой строкой по внешнему ключу берёт на 
                                            #  себя СУБД также нельзя будет добавить в текущую
                                            #  таблицу запись со знач. aircraft_code, которого нет
                                            # в таблице aircrafts(поскольку тек. таблица ССЫЛАЕТСЯ
                                            #  на таблицу aircrafts)
```

#### Реализация отношений
###### ОДИН-К-ОДНОМУ
С помощью внешнего ключа и уникального ограничения.
```sql
CREATE TABLE table1 (
  id SERIAL PRIMARY KEY,
  name VARCHAR(255),
  other_columns...
);

CREATE TABLE table2 (
  id SERIAL PRIMARY KEY,
  table1_id INTEGER UNIQUE REFERENCES table1 (id),
  other_columns...
);
ALTER TABLE table2 ADD CONSTRAINT table2_table1_id_unique UNIQUE (table1_id);
```

###### ОДИН-КО-МНОГИМ
С помощью внешнего ключа.
```sql
CREATE TABLE one (
  id SERIAL PRIMARY KEY,
  name VARCHAR(255)
);

CREATE TABLE many (
  id SERIAL PRIMARY KEY,
  one_id INTEGER REFERENCES one (id),
  description TEXT
);
```

Пример выборки:
```sql
SELECT one.name, many.description FROM one JOIN many ON one.id = many.one_id WHERE one.id = 1;
```

###### МНОГИЕ-КО-МНОГИМ
С использованием дополнительной промежуточной таблицы, которая связывает две таблицы.
```sql
CREATE TABLE table1 (
  id SERIAL PRIMARY KEY,
  name VARCHAR(255)
);

CREATE TABLE table2 (
  id SERIAL PRIMARY KEY,
  name VARCHAR(255)
);

CREATE TABLE table1_table2 (
  table1_id INTEGER REFERENCES table1 (id),
  table2_id INTEGER REFERENCES table2 (id),
  PRIMARY KEY (table1_id, table2_id)
);
```

Добавляем записи
```sql
-- Добавляем записи в таблицу "table1"
INSERT INTO table1 (name) VALUES ('Object 1');
INSERT INTO table1 (name) VALUES ('Object 2');

Добавляем записи в таблицу "table2"

-- Добавляем записи в таблицу "table1_table2"
INSERT INTO table1_table2 (table1_id, table2_id) VALUES (1, 1);
INSERT INTO table1_table2 (table1_id, table2_id) VALUES (1, 2);
INSERT INTO table1_table2 (table1_id, table2_id) VALUES (2, 2);
```

Пример выборки(Этот запрос вернет все записи из таблицы "table2", связанные с объектом "Object 1" из таблицы "table1")
```sql
SELECT t2.name 
FROM table1 t1 
JOIN table1_table2 t12 ON t1.id = t12.table1_id 
JOIN table2 t2 ON t12.table2_id = t2.id 
WHERE t1.id = 1; 
```

#### Установка PostgeSQL в Django
```bash
pip install psycopg2-binary
   # ИЛИ
pip install psycopg2
```

В `settings.py` Django проекта
```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': 'myproject',gt
        'USER': 'myprojectuser',
        'PASSWORD': 'password',
        'HOST': 'localhost',
        'PORT': '',
    }
}
```

```bash
python manage.py makemigrations
python manage.py **migrate**
```

#### Перенос самой базы данных с сервера на сервер
>[!info] Можно просто использовать фикстуры. Т.е. экспорт моделей в JSON и последующий импорт данных в другую БД. [[Django, DRF#Fixtures]]

1) Из под root меняем строчки в файле `/etc/postgresql/14/main/pg_hba.conf`
`local all postgres peer` 
    меняем на:
`local all postgres md5`
Также добавил своего пользователя:
`local all mintmachine md5`

И делаем рестарт:
```bash
sudo systemctl restart postgresql
```

2) Экспорт базы
```bash
pg_dump -U mintmachine -d mint_coast -W > mint_coast_dump.sql
```
`-U` указывет имя пользователя, которому принадлежит БД mint_coast (mintmachine)
`-d` указывает имя БД для экспорта (mint_coast)
`-W` запросить пароль

Экспортированный файл будет в текущей директории

3) Копируем файл на сервер:
```bash
scp mint_coast_dump.sql ufodriver@89.23.110.30:/home/ufodriver/dump
```

4) Создаём БД на др. сервере:
```bash
sudo -u postgres createdb mint_coast
```

5) Заливаем бэкап в БД:
```bash
sudo -u postgres psql mint_coast < mint_coast_dump.sql
```

#### Резервное копирование(бэкап) PostgreSQL
Из под root меняем строчки в файле `/etc/postgresql/14/main/pg_hba.conf`  
    `local all postgres peer`  
    меняем на:  
    `local all postgres md5`  
    Также добавил своего пользователя:  
    `local all mintmachine md5`

И делаем рестарт:

```bash
sudo systemctl restart postgresql
```

Чтобы PostgreSQL при дампе не спрашивал пароль создаём файл `.pgpass`
```bash
hostname:port:database:username:password
```

Задаём ему нужные права
```bash
chmod 600 ~/.pgpass
```

Python скрипт для создания дампов
```python
import os  
import time  
import glob  
  
  
def dump_database():  
    POSTGRES_USER = example_user 
    POSTGRES_DB = example_db
  
    try:  
        if not os.path.exists(f'{os.getenv("HOME")}/db_dumps/'):  
            os.mkdir(f'{os.getenv("HOME")}/db_dumps')  
  
        # получаем все файлы в директории с дампами(словарь "время создания":"путь")  
        files = {os.path.getmtime(x): os.path.abspath(x) for x in glob.glob(f"{os.getenv('HOME')}/db_dumps/*")}  
  
        # удаляем файлы, если их больше 7  
        while len(files) >= 7:  
            os.remove(files.pop(min(files.keys())))  
  
    except Exception as e:  
        print(e)  
  
        # создаём файл дампа  
    print("Preparing database backup started")  
    dump_db_operation_status = os.WEXITSTATUS(os.system(  
        f"pg_dump -U {POSTGRES_USER} -d {POSTGRES_DB} -f {os.getenv('HOME')}/db_dumps/{time.strftime('%d-%m-%Y_%H:%M:%S')}.sql"  
    ))  
    if dump_db_operation_status != 0:  
        exit(f"Dump database command exits with status {dump_db_operation_status}.")  
    print("DB dumped")  
  
  
dump_database()
```

Добавляем в cron  [[Linux#Планировщик CRON]]
```bash
crontab -e
```

Каждый день в 5 утра
*(минута)-(час)-(день месяца)-(месяц)-(день недели)*
```
0 5 * * 0-6 /home/www/pg_backup.bash
```

Логи CRON
```bash
sudo grep CRON /var/log/syslog
```