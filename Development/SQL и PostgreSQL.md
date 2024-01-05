![[postgresql_logo.png]]

>[!info] Транзакция - пакет запросов, которые либо выполняются все, либо не выполняется ничего из пакета.

>[!info] Нормализация БД — это процесс организации данных определенным образом и рекомендации по проектированию. То есть таблицы и связи между ними (отношения) создаются в соответствии с правилами. В результате обеспечивается нужный уровень безопасности данных, а сама база становится более гибкой. Также устраняются несогласованные зависимости и избыточность. 

###### Реляционные БД
- PostgreSQL
- MySQL
- SQLite

###### Нереляционные БД
- Redis (InMemory)
- MongoDB

SQL используется для общения с реляционными БД
> ==1 Нормальная форма== - Одна ячейка содержит ОДНО значение
> ==2 Нормальная форма== - каждая запись должна иметь уникальный идентификатор(первичный ключ, id)

###### Связи между таблицами
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

###### Команды
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
||



