
#### Multiprocessing(Процессы)
>[!info] Процесс - работающее приложение, которому выделена область памяти, недоступная другим приложениям

>[!info] Используйте процессы для задач, связанных с ограничениями процессора
```
import multiprocessing
import time
   
def func(start_int, end_int, result_list):
    result = 0
    for i in range(start_int, end_int):
        result += i ** 2

    result_list.append(result)


manager = multiprocessing.Manager()
result_list = manager.list()  # общий лист для процессов

# передавать в аргументы можно только кортеж
p1 = multiprocessing.Process(target=func, args=(1, 5_000_000, result_list))
p2 = multiprocessing.Process(target=func, args=(5_000_001, 10_000_000, result_list))
    
p1.start()
p1.start()

p1.join()  # основной процесс будет ждать полного выполнения процесса p1
p2.join()

print(sum(result_list))
```

#### Threading(Потоки)
>[!info] Поток - наименьшая еденица выполнения, которая может выполняться ОС. У потоков нет своей памяти, они пользуются памятью создавшего их процесса. Потоки ассоциированы с процессом создавшем их.

>[!info] Используйте потоки для задач, связанных с ограничениями ввода-вывода. Если время ОЖИДАНИЯ ответа(сети, диска и т.п) большое, нужно использовать асинхронку

```
import threading
import time

def func(num):
    print(f"I am thread {num}")

for n in range(4):
    p = threading.Thread(target=func, args=(n,))  # передавать можно только кортеж
    p.start()
    p.join()  # основной поток будет ждать полного выполнения потока p

# модуль threading не имеет функции terminate()
```

#### Асинхронный Python
>[!info] Применяется там, где используется I/O. Всё что касается работы с дисками, фаловой системой, БД, сетью. Наибольшая производительность достигается, когда есть БОЛЬШОЕ время ожидания ответа(сети, диска и т.п).
- Когда программа МНОГОПОТОЧНАЯ происходит постоянное преключение между потоками, и на это тратится время. При асинхронном подходе, всё исполняется в одном потоке, поэтому время на переключение НЕ ТРАТИТСЯ.
- При использовании асинхронного подхода, надо использовать АСИНХРОННЫЕ версии библиотек(aiosqlite, aiohttp, aiogram и т.д.), иначе какой-нибудь код подключения к БД станет БЛОКИРУЮЩИМ.

###### Использование asyncio
>[!info] `async def`  декларирование функции, как асинхронной. Такие функции имеют генераторную природу. Выполняются только в цикле событий. Для того чтобы корутины выполнялись конкурентно, следует обернуть их в задачи.

>[!info] `await`  приврдит к выполнению следующей за ней корутины и также приостанавливает родительскую корутину до выполнения. При этом передаёт исполнение событийтому циклу(если результат не отдаётся сразу же). В этом случаем можно добавить `asyncio.sleep(0)`

>[!info] `acyncio.Task()` обёртка вокруг корутины, которая планирует запуск этой корутины в событийном цикле, также может отменять выполнение корутины

>[!info] `asyncio.run(main())` запускает событийный цикл с одной задачей -> main(и если main заверится ПРЕЖДЕ остальных адач, зарегистриованных в ней, например, то цикл завершится не дожидаясь их)

```
import asyncio

async def print1():                  # определение функции как асинхронной (корутина)
    await asyncio.sleep(5)           # специальный неблокирующий sleep и оператор await для ожидания,
    print('1')                       # чтобы можно было переключиться на другую сопрограмму, пока эта ждёт

async def print2():
    await asyncio.sleep(3)
    print('2')

async def main():
    async with asyncio.TaskGroup() as tg:  # создание задач с помощью асинхронного контекстного менеджера
        tg.create_task(print1())           # это также можно делать и с помощью цикла
        tg.create_task(print2())

asyncio.run(main())                        # событийный цикл
```

###### Создание задач в asyncio
>[!info] `Task` напрямую наследует `future`. Можно считать, что объект `future` представляет значение, которое появится только в будущем. `Task` является комбинацией корутины и `future`. Создавая задачу, мы создаем пустой объект `future` и запускаем корутину. А когда корутина завершится с результатом или вследствие исключения, мы записываем этот результат или объект-исключение во `future`.

>[!info] Когда мы создаём задачу `asyncio.create_task(print2())` код корутины в задаче СРАЗУ же начинает исполнятся и доходит до первого `await`


1. только с версии 3.11
```
async with asyncio.TaskGroup() as tg:    
    tg.create_task(print1())             
    tg.create_task(print2())
```
2.
```
task1 = asyncio.create_task(print1())
task2 = asyncio.create_task(print2())


# здесь код выпоняетя асинхронно, потому что делаетя await для ЗАДАЧИ.
#  если бы делался await для .create_task, то код был бы синхронным
await task1
await task2
```
3.
```
task1 = asyncio.create_task(print1())
task2 = asyncio.create_task(print2())

# gather автоматически обёртывает корутину задачей.
# порядок поступления завершения корутин в .gather НЕ ДЕТЕРМИНИРОВАН!
# однако результы гарантировано возвращаются в том же порядке в котором они и передавались
await asyncio.gather(task1, task2) 
```
- `.gather` отрабатывает исключения и возвращает их как результат вместе с другими результатами корутин, если добавить аргумент `return_exceptions=True`
-  Чтобы получить результат в порядке выполнения корутин(а не добавления, как обычно), нужно использовать `asyncio.as_completed()`. Она принимает список корутин, а возвращает объект генератора

###### Методы задач
|Метод|Описание|
|-------|----------|
|`.done()`|была ли задача выполнена(bool)
|`.cancelled()`|была ли задача отменена(bool)
|`.cancel()`|отменить задачу(с возбуждением исключения asyncio.CancelledError). Не прерывает задачу; Снимет её только если она уже находится в точке ожидания или когда дойдёт до след. такой точки
|`.wait_for(aw, timeout)`|ждет, пока awaitable объект `aw`(корутина или задача) завершится с таймаутом `timeout`. Если истекает таймаут, возбуждается `asyncio.TimeoutError`

Пример `.cancel()`:
```
import asyncio
from asyncio import CancelledError

async def main():
    long_task = asyncio.create_task(asyncio.sleep(6))
    seconds_elapsed = 0
    
    while not long_task.done():
        print('Задача не закончилась, следующая проверка через секунду.')
        await asyncio.sleep(1)
        seconds_elapsed = seconds_elapsed + 1
        if seconds_elapsed == 5:
        long_task.cancel()      # снимет задачу только в точке ожидания,
                                #  т.е. print('Задача не закончилась...
        
    try:
        await long_task         # только здесь мы может отловить CancelledError если она была 
    except CancelledError:
        print('Наша задача была снята')
        
asyncio.run(main())
```

Пример `.wait_for()`:
```
try:
    result = await asyncio.wait_for(long_task, timeout=3)
except asyncio.TimeoutError:
    print('The long task cancelled')
```

Пример выполнения десяти задач асинхронно:
```
import asyncio

async def ask_chat():
    r = await chat.async_ask('Example of request')
    response.append(r.text)

async def main():
    for i in range(10):
        task = asyncio.create_task(ask_chat())
        tasks.append(task)

    await asyncio.gather(*tasks)

response = []
asyncio.run(main())
```

Пример 100 асинхронных запросов на сервер(нужно использовать `aiohttp` потому что `requests` синхронная библиотека)
```
import aiohttp  
import asyncio  
from bs4 import BeautifulSoup
  
  
async def request(url, session):  
    async with session.get(url) as response:  
        html = await response.text()  
        soup = BeautifulSoup(html, 'lxml')  
        title = soup.find('title').text  
        return title  
  
  
async def request_manager():  
    url = 'https://www.mint-coast.ru/'  
    tasks = []  

    # метод .ClientSession() создаёт сеанс(набор с пулом подключений(по умолчанию 100))
    # поскольку это тяжёлое действие, то создаётся обычно один раз и объект передаётся методам
    # у объекта сеанса(session) имеются методы GET PUT POST
    async with aiohttp.ClientSession() as session:  
        for _ in range(100):  
            task = asyncio.create_task(request(url, session))  
            tasks.append(task)  
  
        results = await asyncio.gather(*tasks)  
  
    return results  
  
  
asyncio.run(request_manager())
```

###### Будущие объекты
>[!info] Объект `future` в Python содержит одно значение, которое мы ожидаем получить в будущем, но пока еще, возможно, не получили.
```
from asyncio import Future

my_future = Future()
print(f'my_future готов? {my_future.done()}')
my_future.set_result(42)
print(f'my_future готов? {my_future.done()}')
print(f'Какой результат хранится в my_future? {my_future.result()}')
```

###### Дебаг режим в `asincio`
>[!info] Если какая-то корутина будет выполнятся дольше таймаута, будет выведено сообщение(по умолчанию таймаут - 100 мс)
```
import asyncio

async def main():
    loop = asyncio.get_event_loop()
    loop.slow_callback_duration = 0.250
    
asyncio.run(main(), debug=True)
```

###### Асинхронный контекстный менеджер
>[!info] Класс реализующий два дандер метода для корутины: `__aenter__` и `__aexit__`
>
```
async with aiohttp.ClientSession() as session:  
    for _ in range(100):  
        task = asyncio.create_task(request(url, session))  
        tasks.append(task)  
  
    results = await asyncio.gather(*tasks)  
```

###### Таймауты
>[!info] По умолчанию таймаут в aiohttp равен 5 минут

Пример установки таймаута для get запроса в 10мс
```
import asyncio
import aiohttp
from aiohttp import ClientSession

async def fetch_status(session: ClientSession, url: str) -> int:
    ten_millis = aiohttp.ClientTimeout(total=.01)
    async with session.get(url, timeout=ten_millis) as result:
        return result.status
```

Пример таймаута сессии(сеанса). Полный таймаут равен 1с, а таймаут подключения 100мс
```
async def main():
    session_timeout = aiohttp.ClientTimeout(total=1, connect=.1)
    async with aiohttp.ClientSession(timeout=session_timeout) as session:
        await fetch_status(session, 'https://example.com')
```

Если срабатывает таймаут, то возбуждается исключение `TimeoutError`

###### Обработка исключений при использовании `gather`
>[!info] `return_exceptions=True` означает возврат исключения как результат

Получаем все результаты(с ошибками и без)
```
async def main():
    async with aiohttp.ClientSession() as session:
        urls = ['https://example.com', 'python://example.com']
        tasks = [fetch_status_code(session, url) for url in urls]
        results = await asyncio.gather(*tasks, return_exceptions=True)
        exceptions = [res for res in results if isinstance(res, Exception)]
        successful_results = [res for res in results if not isinstance(res, Exception)]

        print(f'Все результаты: {results}')
        print(f'Завершились успешно: {successful_results}')
        print(f'Завершились с исключением: {exceptions}')
```

###### Обработка результатов по мере поступления
Функция `wait`. Она принимает список awaitable и возвращает два множества - выполненные и выполняющиеся задачи.
```
async def fetch_status(session: ClientSession, url: str, delay: int = 0) -> int:
    await asyncio.sleep(delay)
    async with session.get(url) as result:
        return result.status

async def main():
    async with aiohttp.ClientSession() as session:
        url = 'https://www.example.com'
        pending = [fetch_status(session, 'https://www.example.com', 1),
                    fetch_status(session, 'https://www.example.com', 1),
                    fetch_status(session, 'https://www.example.com', 10)]

        while pending:
            done, pending = await asyncio.wait(fetchers, return_when=asyncio.FIRST_COMPLETED)
            print(f'Число завершившихся задач: {len(done)}')
            print(f'Число ожидающих задач: {len(pending)}')
            
            for done_task in done:
                print(await done_task)
                
            for task in asyncio.tasks.all_tasks():
                print(task)

asyncio.run(main())
```