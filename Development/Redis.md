![[redis.png]]

[Установка на Linux](https://redis.io/docs/latest/operate/oss_and_stack/install/install-redis/install-redis-on-linux/)
[Документация общая](https://redis.io/docs/latest/develop/clients/redis-py/)
[Документация Redis-py](https://redis-py.readthedocs.io/en/stable/)
[Учебник по Redis](https://python-scripts.com/redis)

>[!info] [Redis](http://redis.io/) (Remote Dictionary Service) — это опенсорсный сервер баз данных типа ключ-значение. Точнее всего описать Redis можно, сказав, что это — сервер структур данных. 

![[2ccbd1d1db0adfe14542fa9c85ed1aa7.jpeg|500]]

Типы данных поддерживаемые Redis:

- Строка (String)
- Битовый массив (Bitmap)
- Битовое поле (Bitfield)
- Хеш-таблица (Hash)
- Список (List)
- Множество (Set)
- Упорядоченное множество (Sorted set)
- Геопространственные данные (Geospatial)
- Структура HyperLogLog (HyperLogLog)
- Поток (Stream)

Эффективно оперирует данными:
- Данные, которые редко меняются, к которым часто обращается приложение.
- Данные, не относящиеся к критически важным, которые часто меняются.

#### Установка интерфейса для Python
```bash
pip install redis
```

Пример для подключения в `AIOGram`
```python
from aiogram import Dispatcher
from aiogram.fsm.storage.redis import RedisStorage

storage = RedisStorage.from_url('redis://HOST:6379/0')

# подключение с авторизацией
storage = RedisStorage.from_url('redis://username:password@193.3.168.217:6379/0')
# 0 - это номер базы данных. По умолчанию в Redis баз данных 16 штук (от 0 до 15).
# Число можно увеличить через config.

dp: Dispatcher = Dispatcher(storage=storage)
dp.message.middleware.register(ThrottlingMiddleware(storage=storage))
```

>[!tip] **Если вы не добавляли пользователя отдельно, то его username = "default"**

Пример простого соединения
```python
import redis

r = redis.Redis(host='HOST', port=6379, db=0, username='username', password='your_pass')

try:
    info = r.info()
    print(info['redis_version'])
    response = r.ping()
    if response: 
        print("Подключение успешно!")
    else:
        print("Не удалось подключиться к Redis.")
except redis.exceptions.RedisError as e:
    print(f"Ошибка: {e}")
```

#### Строки
```python
import redis
  
r = redis.Redis(host='localhost', port=6379, db=0)  

my_string_key = r.set('My new great KEY!', "122")  
  
print(my_string_key)                # True  
print(r.get('My new great KEY!'))   # b'122'  
print(r.get('wrong key'))           # None  
  
r.delete('My new great KEY!')       # удаление  
  
print(r.keys())                     # список всех ключей  
  
r.set('My_Number', 1)  
r.incrby('My_Number')               # увеличение на 1
r.incrby('My_Number', 100)          # увеличение на 100
  
print(r.get('My_Number'))           # b'102'
```

#### Списки
```python
r.lpush('cars', 'Toyota')          # вставить слева
r.lpush('cars', 'BWM')             # вставить слева
r.rpush('cars', 'KIA')             # вставить справа

print(r.lrange('cars', 0, -1))     # [b'BWM', b'Toyota', b'Toyota', b'KIA']  
print(r.lpop('cars'))              # Возврат слева и удаление     b'BWM'  
print(r.llen('cars'))              # длина списка  
  
r.lmove('cars', 'sold', 'LEFT', 'LEFT')  # перенести значение из одного списка в другой

r.delete('sold')                   # удаление
```

#### Хэш таблицы
```python
r.hset('iphone', mapping={'brand': 'Apple', 'model': 'iPhone X', 'memory': 64, 'color': 'black'})  
print(r.hget('iphone', 'model'))      # Получить отдельное значение b'iPhone X'
print(r.hgetall('iphone'))            # Получить все значения
        # {b'brand': b'Apple', b'model': b'iPhone X', b'memory': b'64', b'color': b'black'}

r.hincrby('iphone', 'memory', 64)     # увеличение значения на 64
print(r.hget('iphone', 'memory'))     # b'128'

r.delete('sold')                   # удаление
```

#### TTL(Время жизни)
Создать строку с TTL
```python
r.setex('var_with_ttl', 20, 'my_value')  # 20 seconds
```

Узнать оставшееся время жизни
```python
r.ttl('var_with_ttl')           # -1 (Неограничено) # -2 (Удалено)
```

Изменить TTL
```python
r.expire('var_with_ttl', 10)
```

Проверить существование
```python
r.exists('var_with_ttl')        # 0
```