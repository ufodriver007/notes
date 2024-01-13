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
```
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
|`INSERT INTO table (id, name) VALUES (1, 'Astra')`|вставка значений
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
```
SELECT teacher.surname, lesson.name FROM teacher INNER JOIN lesson ON teacher.id = lesson.teacher.id
```

Пример. Выборка всех полей и объединение 2х таблиц вертикально
```
SELECT * FROM teacher UNION SELECT * FROM lesson
```

#### Функции
|Пример функции|Описание|
|`SELECT AVG(age) FROM teacher`|среднее значение колонки
|`SELECT MAX(age), MIN(age) FROM teacher`|максимальное и минимальные значения колонки
|`SELECT SUM(age) FROM teacher`|сумма всех значений колонки
|`SELECT COUNT(age) FROM teacher`|количество записей
|`SELECT age, COUNT(age) FROM teacher GROUP BY age`|выборка по age с группировкой по кол-ву записей
|`SELECT title, body FROM news ORDER BY RANDOM() LIMIT 1`|рандомная запись
|`ROUND()`|округление
|`NOW()`|дата и время сейчас
|`NOW()::DATE`|дата сейчас

#### PostgreSQL
Установка
```
sudo apt install postgresql postgresql-contrib
```

Версия
```
pg_config --version
```

```
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
```
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
```
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
```
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
```
SELECT one.name, many.description FROM one JOIN many ON one.id = many.one_id WHERE one.id = 1;
```

###### МНОГИЕ-КО-МНОГИМ
С использованием дополнительной промежуточной таблицы, которая связывает две таблицы.
```
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
```
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
```
SELECT t2.name 
FROM table1 t1 
JOIN table1_table2 t12 ON t1.id = t12.table1_id 
JOIN table2 t2 ON t12.table2_id = t2.id 
WHERE t1.id = 1; 
```

#### Установка PostgeSQL в Django
```
pip install psycopg2-binary
   ИЛИ
pip install psycopg2
```

В `settings.py` Django проекта
```
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

```
python manage.py makemigrations
python manage.py **migrate**
```

#### Перенос самой базы данных с сервера на сервер
>[!info] Можно просто использовать фикстуры. Т.е. экспорт моделей в JSON и последующий импорт данных в другую БД. [[Django#Fixtures]]

1) Из под root меняем строчки в файле `/etc/postgresql/14/main/pg_hba.conf`
`local all postgres peer` 
    меняем на:
`local all postgres md5`
Также добавил своего пользователя:
`local all mintmachine md5`

И делаем рестарт:
```
sudo systemctl restart postgresql
```

2) Экспорт базы
```
pg_dump -U mintmachine -d mint_coast -W > mint_coast_dump.sql
```
`-U` указывет имя пользователя, которому принадлежит БД mint_coast (mintmachine)
`-d` указывает имя БД для экспорта (mint_coast)
`-W` запросить пароль

Экспортированный файл будет в текущей директории

3) Копируем файл на сервер:
```
scp mint_coast_dump.sql ufodriver@89.23.110.30:/home/ufodriver/dump
```

4) Создаём БД на др. сервере:
```
sudo -u postgres createdb mint_coast
```

5) Заливаем бэкап в БД:
```
sudo -u postgres psql mint_coast < mint_coast_dump.sql
```