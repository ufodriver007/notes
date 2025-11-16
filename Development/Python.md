![[Pasted image 20231218023825.png]]

>[!info] **Python** - высокоуровневый язык программирования общего назначения с динамической(при объявлении переменной не нужно явно указывать тип) строгой типизацией(операции между разными типами данных требуют явного их преобразования) и автоматическим управлением памятью. 

>[!info] **Модуль** - это просто пайтон файл

>[!info] **Пакет** - это модули объединённые по смыслу в папку и файл `__init__.py`(это конфигуратор, может быть пустым)

#### Виртуальное окружение и pip
|  команда                 |  описание  |
|--------------------------|-----------|
|`python3 -m venv env`|создаёт виртуальное окружение в директорию env
|`source env/bin/activate`|активировать виртуальное окружение(из свежесозданной папки env)
|`deactivate`|деактивировать venv
|`pip install -U pip`|апдейт пипа
|`pip list`|список библиотек
|`pip install requests`|загрузка библиотеки requests
|`pip uninstall requests`|удаление библиотеки
|<code>pip freeze \| xargs pip uninstall -y</code>|удалить все пакеты виртуального окружения
|`pip freeze > requirements.txt`|создать файл с зависимостями
|`pip install -r requirements.txt`|установить все пакеты из списка
|`python3 -m http.server`|встроенный http сервер

#### Пакетный менеджер UV
[GitHub + Документация](https://github.com/astral-sh/uv)

Установка (Linux)
```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

Апдейт (если установлено standalone)
```bash
uv self update
```

Список версий `Python`
```bash
uv python list
```

Установка/Удаление `Python`
```bash
uv python install 3.13.4
uv python uninstall 3.13.4
```

Весь код в этой директории будет запускаться при помощи этой версии интерпретатора
```bash
uv python pin 3.13.4
```

Инициализация. Добавляет `.git`, `.gitignore`, `pyproject.toml`(описывает зависимости), `README.md` и директорию `.venv`(с ней не нужно будет работать напрямую)
```bash
uv init
```

Выполнение интерпретации файла. Если нужного интерпретатора нет, UV его скачает.
```bash
uv run main.py
uv run manage.py startapp my_django_app
```

Установка пакетов
```bash
uv add requests
```

Удаление пакетов
```bash
uv remove requests
```

###### Зависимости
`pyproject.toml` - это приоритетный список зависимостей. Но при необходимости можно создать и `requirements.txt`
```bash
uv pip compile -o requirements.txt  # Генерация из pyproject.toml
uv pip freeze > requirements.txt    # Из текущего окружения
```

Синхронизация зависимостей (`pyprject.toml`)
```bash
uv sync
```

Установка пакетов из файла зависимостей (`requirements.txt`)
```bash
uv pip install -r requrements.txt
```

Показать зависимости в виде дерева
```bash
uv tree
```

#### Установка свежей версии Python
###### Апдейт системы и установка зависимостей
```bash
sudo apt update
sudo apt install software-properties-common
```
###### Добавление PPA репозитория
1. `apt install -y software-properties-common`
2. `add-apt-repository -y ppa:deadsnakes/ppa`
3. `apt-get update && apt-cache search python3.1`
4. `apt-get install python3.11 -y`
- можно создать симлинк для удобства
   `ln -s /usr/bin/python3.11 /usr/bin/python`
   `python --version`
###### Компиляция из исходников
1. `sudo apt-get -y update`
2. `sudo apt -y install zlib1g-dev build-essential libgdbm-dev libncurses5-dev libssl-dev libnss3-dev libffi-dev libreadline-dev wget libsqlite3-dev libbz2-dev`
3. `mkdir /home/ufodriver/build`
4. `cd /home/ufodriver/build`
5. `wget https://www.python.org/ftp/python/3.13.5/Python-3.13.5.tgz`
6. `tar -xf *.tgz`
7. `cd <версия_python>`
8. `./configure --enable-optimizations`     запуск процедуры подготовки к компиляции
9. `make -j $(nproc)`    сборка пакета
10. `sudo make altinstall`    интеграция собранного вами пакета непосредственно в ОС
- можно создать симлинк для удобства
  `ln -s /usr/bin/python3.11 /usr/bin/python`
  `python --version`
  
###### Установка дополнительных пакетов
```bash
sudo apt install python3.11-dev
sudo apt install python3.11-gdbm                 
sudo apt install python3.11-venv                 # virtual environment
sudo apt install python3.11-tk                   # tkinter
```

#### Простая защита скрипта
1. Обфускация кода
2. Трансляция в Си и компиляция в исполняемый файл
3. Привязка в железу
4. Ограничение времени действия программы с помощью получения времени с NTP-сервера

###### Обфускация кода
```bash
pip install pyarmor
```
Генерация
```bash
pyarmor gen foo.py
```


###### Nuitka(компиляция в исполняемый файл)
>[!info] EXE в Windows и BIN в Linux

1. `sudo apt install nuitka`
2. `sudo apt install gcc`   компилятор си
3. `pip install nuitka`
4. `python3 -m nuitka --windows-disable-console --follow-imports BasicEnglish.py`
>[!info] Параметры
>
>|  команда                 |  описание |
>|---------------------|-----------|
 >| `--standalone` | для создания папки с зависимостями Си, чтобы можно было передавать приложение|
 >|`--windows-disable-console`|оконное приложение
 >|`--follow-imports`|программа со всеми модулями(либы и др. .py), а не один файл `main.py`
 >|`--include-data-files=/etc/*.txt=etc/`|скопировать некоторые или все файлы в каталог `etc`
 >|`--include-data-dir=/path/to/images=images`|скопировать всю папку со всеми файлами в каталог `images`
 >|`--windows-icon-from-ico=example.ico` |установить иконку
 
>[!tip] В Windows можно затем использовать InnoSetup для создания установщика(чтобы передавать один файл)

>[!tip] Чтобы из терминала передаввать аргументы, нужно импортировать `sys`. А `sys.argv` будет массивом с аргументами

###### Получение времени с NTP-сервера
```bash
pip install ntplib
```
Пример функции проверки даты
```python
import ntplib  
from datetime import datetime, timezone  
  
  
def get_time_from_ntp():  
    try:  
        ntp_client = ntplib.NTPClient()  
        response = ntp_client.request('pool.ntp.org')  # Используем публичный NTP-сервер  
        ntp_time = datetime.fromtimestamp(response.tx_time, tz=timezone.utc)  # Преобразуем время в формат UTC  
        return ntp_time  
    except Exception as e:  
        print(f"Ошибка при обращении к NTP-серверу: {e}")  
        return None  
  
  
def check_expiration_date(expiration_date_str):  
    # Преобразуем в date  
    expiration_date = datetime.strptime(expiration_date_str, "%Y-%m-%d").date()
  
    # Получаем текущую дату с NTP-сервера
    current_date = get_time_from_ntp()  
  
    if current_date is None:  
        print("Ошибка: не удалось получить текущую дату с NTP-сервера.")  
        return False  
  
    if current_date.date() <= expiration_date:  # Преобразуем current_date в date для сравнения  
        return True  
    else:  
        return False  
  
  
print(check_expiration_date('2024-12-07'))  # True
```

###### Получение серийного номера жёсткого диска
```python
import subprocess
import os

def get_disk_serial_number():  
    if os.name == 'nt':  # Для Window возвращает 'nt', для unix-подобных возвращает 'posix'  
        try:  
            output = subprocess.check_output('wmic diskdrive get serialnumber', shell=True)  
            serial = output.decode().split("\n")[1].strip()  
            return serial  
        except Exception as e:  
            print(f"Ошибка выполнения команды: {e}")  
            return None  
    else:  
        try:  
            result = subprocess.run(  
                ["udevadm", "info", "/dev/sda"],  
                capture_output=True,
                text=True,  
                check=True  
            )  
            # Найти строку с серийным номером  
            for line in result.stdout.splitlines():  
                if "ID_SERIAL=" in line:  
                    return line.split("ID_SERIAL=")[1]  
            return None  
        except subprocess.CalledProcessError as e:  
            print(f"Ошибка выполнения команды: {e}")  
            return None  
  
  
print(get_disk_serial_number())
```

#### Способы хранения паролей в программе
1. Создать отдельный файл и хранить пароль в нём, предварительно указав этот файл в `.gitignore`
2. Хранить данные в переменной окружения. Задать переменную в Linux для текущего сеанса оболчки `export PASSWORD=12345` (или же добавить эту строчку в `~/.bashrc`). Получить в Python:
```python
import os
PASSWORD = os.getenv('PASSWORD')
```
3. Использовать библиотеку `python-dotenv`.
```bash
pip install python-dotenv
```

Создаём файл `.env` и записываем туда все переменные окружения, которые будут добавляться каждый раз(не забываем закинуть его в `.gitignore`).

```python
import os
from dotenv import load_dotenv

load_dotenv()                                # take environment variables from .env.
PASSWORD = os.getenv('PASSWORD')
```
   
#### ИМПОРТ
Когда мы пишем `import`, интерпретатор сначала ищет модули в:
   1. среди встроенных модулей(`print(sys.builtin_module_names)`)
   2. среди модулей в той же директории, что и исполняемый файл
   3. обращается к путям переменной окружения `PYTHONPATH`
   4. обращается к стандартной библиотеке(`print(sys.stdlib_module_names)`)
   5. ищет среди сторонних библиотек в папке `site-packages`

   Файл `__init__.py` позволяет указать интерпретатору, что именно нужно импортировать из пакета.
   
####  ПАКЕТЫ
   >[!tip] чтобы создать пакет, нужно создать папку, закинуть туда все модули(файлы `*.py`) и создать там файл `__init__.py`
   
   Чтобы можно было импортировать весь пакет(а не части) в `__init__.py` дописываем импорты всех модулей: `import <модуль>`

####  Области видимости(LEGB): Scope
- локальная
- вышестоящей функции
- глобальная
- Built-In

>[!info] Если хотим внести изменения в глобальную переменную, пишем `global x`, если хотим изменить переменную в функции, вызвавшей нашу `nonlocal x`

```python
def name():
    x = 10
    def name2():
        nonclocal x
        x = 100
    print(x)              # 100
```

>[!warning] Нужно по возможности избегать глобальных переменных, чтобы не захламлять RAM.

#### Типы данных
|Group|Types|
|------|-------:|
|Text Type|str
|Numeric Types|int, float, complex
|Sequence Types|list, tuple, range
|Mapping Type|dict
|Set Types|set, frozenset
|Boolean Type|bool
|Binary Types|bytes, bytearray, memoryview
|None Type|None

1.    str
2.   int
3.    float
4.    complex     (состоит из двух чисел с плавающей точкой, представляющих соответственно его действительную и мнимую части)
5.    ==list        (изменяемый тип)==   
6.    tuple
7.    range
8.    ==dict        (изменяемый тип)==   
9.    ==set         (изменяемый тип)==
10.   frozenset
11.   bytes
12.   ==bytearray   (изменяемый тип)==
13.   ==memoryview  (изменяемый тип)==
14.   bool
15.   None
>[!info] Неизменяемые типы можно хэшировать. Только хэшируемые объекты могут быть ключом словаря или элементом множества set.
Для копирования изменяемых объектов, применяют метод `copy()`. Но если в нём есть другие измен. объекты, то ссылки на них остаются(Это поверностная копия). Чтобы ПОЛНОСТЬЮ скопировать, применяют `deepcopy()`
```python
from copy import deepcopy

new_dict = deepcopy(old_dict)
```

Последовательности в Python можно разделить на:
- Контейнерные последовательности
    Позволяют хранить элементы разных типов, в т. ч. вложенные контейнеры.
    Примерами могут служить `list`, `tuple` и `collections.deque`.
- Плоские последовательности
    Позволяют хранить элементы только одного типа. Примерами могут служить `str`, `bytes` и `array.array`.

В контейнерных последовательностях хранятся ссылки на объекты любого типа, тогда как в плоских последовательностях – сами значения прямо в памяти, занятой последовательностью, а не как отдельные объекты Python.
![[python_seq.png]]

>[!info] Можно предложить деление на *изменяемые* и *неизменяемые* последовательности. Однако например в tuple можно положить список и именить потом сам список. Поскольку tuple будет хранить только ссылку на список, это в принципе становится возможно. Также если tuple содержит в себе список, его невозможно хэшировать. Поэтому изменяемыми и неизменяемыми последовательности можно назвать условно.
```python
l = ['a', 'b', 'c']  
t = (1, 2, 3, l)  
print(t)        # (1, 2, 3, ['a', 'b', 'c'])
print(id(t))    # 139927422979104
  
l.append('d')  
print(t)        # (1, 2, 3, ['a', 'b', 'c', 'd'])
print(id(t))    # 139927422979104
  
print(hash(t))   # TypeError: unhashable type: 'list'
```

>[!infp] К сожалению, в Python нет по-настоящему неизменяемой последовательности контейнерного типа: даже в «неизменяемых» кортежах значения могут быть изменены, если представляют собой изменяемые объекты, например списки или объекты, определенные пользователем.

#### Объекты
Каждый объект Python, находящийся в памяти, имеет заголовок с метаданными. У простейшего объекта, `float`, имеется поле значения и два поля метаданных:
- `ob_refcnt`: счетчик ссылок на объект;
- `ob_type`: указатель на тип объекта;
- `ob_fval`: число типа double (в смысле C), в котором хранится значение с плавающей точкой.

#### dir() и help()
- Функция `dir()` возвращает список всех методов и атрибутов объекта
- Функция `help()`  напечатает автоматически сгенерированную документацию по объекту, классу или модулю

####  Type hinting
>[!info] Это просто подсказки типов, НЕ УСЛОВИЕ для аргументов

Это могут быть подсказки для:
-  аргументов функций
- возвращаемых значений
- создания переменной (например если мы используем внешнюю нетипизированную функцию и IDE не знает какой тип вернёт эта функция)

```python
from typing import Optional  # тип либо какой-то конкретный, либо None
                   Union     # как замена |, например var122: Union[int, float]
                   Literal   # когда ожидается конкретное значение,
                             # напр. user : dict[Literal['name'] | Literal['second_name']
                   NamedTuple # Именованный кортеж
                   dict      # Словарь
                   Iterable  # Итерируемый объект

book: str = "My new book"

class Coordinates(NamedTuple):
    latitude: float
    longitude: float

def func(age: float, name: str) -> bool:
    """
    Описание функции
    """   
    pass   

def func(age: int | float, name: str) -> bool:
    pass

def func(age: float, info: list[str]) -> list[str | bool]:
    pass

def func(coords: Coordinates) -> None:
    pass

def func(user: tuple[int, ...]) -> None:  # кортеж с неизвестным кол-во элементов
    pass
```

#### Перечисления
```python
from enum import Enum

class TrafficLight(Enum):  
    RED = "stop"  
    GREEN = "go"  
    YELLOW = "wait"  
    
def func() -> TrafficLight:
    return TrafficLight.RED
```

###### Alias
```python
Celsius = int

@dataclass(slots=True, frozen=True)
class Weather:
    temperature: Celsius  # алиас, так сразу понятно что тут int в градусах Цельсия
    weather_type: str
    sunrise: datetime
    sunset: datetime
    city: str
```

####  Аннотации
`func.__annotations__`  *возвращает словарь с ключами(переменными) и значениями(значения этих переменных), если были использованы подсказки типов для функции*

#### Каскадное присвоение
```python
num = num2 = 5                      # обе переменные равны 5
```

####  Множественное присвоение
```python
num1, num2 = 5, 7
z, x, c = (9, 8, 7)                 # распаковка кортежа
```

#### Свап переменных
```python
a, b = b, a
```

#### Распаковка
- Список
```python
z, x, c = [1, 2, 3]
z, x, *c = [1, 2, 3, 4, 5]           # c = [3, 4, 5]
a, *body, c, d = range(5)
*head, b, c, d = range(5)
```

```python
a = [123, 456, 789]  
[val, *_] = a   # питонический способ получения элемента списка
print(val)      # 123

a = [123, 456, ['a', 'b']]  
[_, _, [val, _]] = a  
print(val)    # a
```

- Словарь
```python
new_dict = {'a': 1, 'b': 2, 'c': 3}
a, b, c = new_dict                   # a = 'a', b = 'b', c = 'c'
```



#### Оператор "морж" :=
   >[!info] variable := expression
   
   Переменной присваивается значение выражения и тут же возвращается
```python
print(num := 15)
```

#### Сравнение типов
```python
if isinstance(x, int):
    return True
```

#### Decimal
Точные вычисления чисел с плавающей точкой
```python
import decimal

number = decimal.Decimal("0.300") # Задаём сразу и число и точность после запятой
print(number * 3)    # нельзя смешивать в операциях дробные числа float и Decimal
```
Округление
```python
number.quantize(Decimal("1.00"))  # округление до двух знаков после запятой
```
По умолчанию округляется до ближайшего чётного числа(ROUND_HALF_EVEN)
Чтобы округлять в сторону повышения от 5 и выше:
```python
number.quantize(decimal.Decimal("1.00"), decimal.ROUND_HALF_UP)
```

#### Магические методы
Внутренние методы классов, которые обычно не следует вызывать явно.
Мы реализуем специальные методы, когда хотим, ==чтобы наши объекты могли
поддерживать и взаимодействовать с базовыми конструкциями языка==:
   - коллекции;
   - доступ к атрибутам;
   - итерирование (включая асинхронное итерирование с помощью async for);
   - перегрузку операторов;
   - вызов функций и методов;
   - представление и форматирование строк;
   - асинхронное программирование с использованием await;
   - создание и уничтожение объектов;
   - управляемые контексты (т. е. блоки with и async with).
   >Благодаря реализации специальных методов пользовательские объекты могут вести себя как встроенные типы
   
>[!info] Обычно они предназначены для вызова интерпретатором, а не вами. 
>Например:
   `for i in x`: подразумевает вызов функции `iter(x)`, которая, в свою очередь,
   может вызывать метод `x.__iter__()`, если он реализован, или использовать `x.__getitem__()`
   
|Метод|Описание|
|------|-------|
|`__iter__()`|возвращает сам итератор(для поддержки for, распаковки и других видов итерирования)
|`__getitem__('abc')`|получить значение по ключу 'abc'
|`__len__()`|длина
|`__eq__()`|сравнение объектов( == )
|`__ne__()`| !=
|`__repr__`|строковое представление(если не реализуем для своего класса, то будет что-то типа <MyClass object at 0x10e100070>) для отладки и протоколирования
|`__bool__()`|представление в булевом значении
|`__doc__`|Документация
|`__dir__()`|вызывается методом dir() и выводит список доступных атрибутов объекта
|`__new__()`|статический метод, вызывается когда создается экземпляр класса
|`__init__()`|метод отвечает за инициализацию(заполнение полей) экземпляров класса после их создания
|`__getattribute__(self, item)`|Метод автоматич. вызывается, когда считываются атрибуты экземпляра класса. Возвращает значение соответств. атрибута
|`__setattr__(self, key, value) `|Метод автоматич. вызывается, когда устанавливаются атрибуты экземпляра класса.
|`__getattr__(self, item)  `|Метод автоматич. вызывается, когда идёт обращение к несуществующему атрибуту экземпляра класса
|`__delattr__() `|Метод автоматич. вызывается, когда удаляется атрибут экземпляра класса
|`__call__() `|Отвечает за возможность вызова оператором ()
|`__lt__(self, other)`|Определяет поведение оператора сравнения «меньше», <
|`__dict__`|Возвращает словарь атрибутов объекта, типа `{'name': 'Alice', 'age': 25}`

#### if-elif-else
Если выражение в `if` будет `False`, пойдёт проверять `elif` блок.
`elif` блоков может быть несколько

#### Тернарный оператор
<выражение если True> if <булево выражение> else <выражение если False>
```python
return 'more than 10' if f > 10 else 'less than 10'
```

#### for
```python
for x in list:
    print x
```
>[!tip] После циклов можно ставить else, и блок кода там будет исполняться однократно после цикла.

#### Pattern matching (Сопоставление с образцом)
Основное предназначение - это работа со сложными условиями, когда в противном случае придётся городить вереницу `if-elif ` или слишком длинные строчки деструтуризтрующие данные.
```python
match shape:
    case Rectangle(width=w, height=h):
        return w * h
    case Circle(radius=r):
        return 3.14 * r * r
    case _:
        return 'unknown shape'
```

Пример вычисления факториала
```python
def factorial(n):
    match n:
        case 0 | 1:
            return 1
        case _:
            return n * factorial(n - 1)
```

Пример с распаковкой данных
```python
data = ('user', 'bob', 'worker', 'somevalue', 18)
match data:
    case [user, *_, age] if age >= 18:
        print(user, age)
    case _:
        pass
```

```python
def handle_command(self, message):
    match message:
        case ['BEEPER', frequency, times]:
            self.beep(times, frequency)
        case ['NECK', angle]:
            self.rotate_neck(angle)
        case ['LED', ident, intensity]:
            self.leds[ident].set_brightness(ident, intensity)
        case ['LED', ident, red, green, blue]:
            self.leds[ident].set_color(ident, red, green, blue)
        case _:  # ветвь case по умолчанию
            raise InvalidCommand(message)
```

Пример сопоставления словаря (это вполне может быть JSON)
```python
def get_creators(record: dict) -> list:
    match record:
        case {'type': 'book', 'api': 2, 'authors': [*names]}:
            return names
        case {'type': 'book', 'api': 1, 'author': name}:
            return [name]
        case {'type': 'book'}:
            raise ValueError(f"Invalid 'book' record: {record!r}")
        case {'type': 'movie', 'director': name}:
            return [name]
        case _:
            raise ValueError(f'Invalid record: {record! r}')
```

#### Запуск приложения ассоциированного с файлом
```python
os.startfile('C://Program Files/Inkscape/inkscape.exe')           # Для Windows
subprocess.call(['open', '/home/ufodriver/Загрузки/RegEx.txt'])   # Для Linux
```

#### Выполнить команду
Здесь возможно внедрение комманд и подстановочных знаков. Низкоуровневое API
```python
os.system('df -h')
```

Здесь НЕвозможно внедрение комманд и подстановочных знаков. Высокоуровневое API
```python
import subprocess  
  
command = ['cat', 'test.txt']  
completed_process = subprocess.run(command, capture_output=True, check=True)
# Поулчаем вывод терминала
print(completed_process.stdout)
```

#### Мониторинг(Загруженность CPU и RAM)
```bash
pip install psutil
```

Получение процента загруженности CPU и RAM
```python
import psutil  

print(f'CPU: {psutil.cpu_percent(interval=1)}%')  
print(f'RAM: {psutil.virtual_memory().percent}%')  
print(f'Ядер в процессоре: {psutil.cpu_count()}')  
print(f'Частота процессора(макс.): {psutil.cpu_freq(percpu=False).max}МГц')  
  
print(f'Загруженность диска: {psutil.disk_usage("/").percent}%')  
print(f'Общая ёмкость диска: {psutil.disk_usage("/").total / 1000000000:.1f}Гб')  
print(f'Занято на диске: {psutil.disk_usage("/").used / 1000000000:.1f}Гб')  
print(f'Свободно на диске: {psutil.disk_usage("/").free / 1000000000:.1f}Гб')

# Вывод:
# CPU: 44.6%
# RAM: 40.3%
# Ядер в процессоре: 8
# Частота процессора(макс.): 4500.0МГц
# Загруженность диска: 89.3%
# Общая ёмкость диска: 125.3Гб
# Занято на диске: 106.1Гб
# Свободно на диске: 12.8Гб
```

Проверка типа ОС
```python
psutil.WINDOWS  # False
```


#### Создание списка
```python
m = [1, 2, 4]
m = list(последовательность)
m = list(range(10))
arr = arr2[:]                      # Копирование значений посредством среза
```

###### Когда список не подходит
Например, если требуется сохранить 10 миллионов чисел с плавающей точкой, то тип `array` будет гораздо эффективнее, поскольку в нем хранятся не полные объекты `float`, а только упакованные байты, представляющие их машинные значения, – как в массиве в языке `C`. С другой стороны, если вы часто добавляете и удаляе­те элементы из того или другого конца списка, стоит вспомнить о типе `deque` (двусторонняя очередь) – более эффективной структуре данных `FIFO`.

###### Массивы (array.array)
Если список содержит только числа, то тип `array.array` эффективнее, чем `list`: он поддерживает все операции над изменяемыми последовательностями (включая `.pop`, `.insert `и `.extend`), а также дополнительные методы для быстрой загрузки и сохранения, например `.frombytes` и `.tofile`.
Массив Python занимает столько же памяти, сколько массив C.

```python
from array import array
from random import random

floats = array('d', (random() for i in range(10**7)))
```

Типы и их строковые коды для массивов
![[Array_types.png]]

#### Операции со списками
```python
m = [1, 2, 4]
m = m * 2                 # [1, 2, 4, 1, 2, 4]
m = m + [1,2]             # [1, 2, 4, 1, 2]
m.append(x)
m.remove(x)               # удаляет первый элемент с таким значением
m.pop(x)                  # удалить по индексу и вернуть значение
del list[0]               # удалить по индексу
m.count(i)
m.index(element)          # получить индекс по значению
m.clear
m.sort(reverse=True) 
m.copy()
m.extend()                # добавляет несколько элементов, а не один
m.reverse()               # разворачивает список
```

#### Кортежи (Быстрее итерируются, занимают меньше памяти)
```python
x = (9, 8, 6, 43)
x = tuple(<последовательность>)
x += (4, 5)               # к кортежу добавляем ещё один кортеж
one, two = (1, 2)         # распаковка кортежа
```

>[!tip] Имеет смысл использовать кортежи как записи, типа `lax_coordinates = (33.9425, -118.408056)`, либо как неизменяемые списки.

#### Срезы
```python
x = [1, 2, 3, 4, 5, 6, 7]
x[1:5]                  # От индекса 1 до 5(не включая) со сдвигом 1(по умолч.)
                        # результат [2,3,4,5]
```

Почему в срезы и диапазоны не включается последний элемент:
- Легко понять, какова длина среза или диапазона, если задана только конечная позиция: и `range(3)`, и `my_list[:3]` содержат три элемента.
- Легко вычислить длину среза или диапазона, если заданы начальная и конечная позиции, достаточно вычислить их разность `stop` - `start`.
- Легко разбить последовательность на две непересекающиеся части по любому индексу `x`: нужно просто взять `my_list[:x]` и `my_list[x:]`. 

Именованные срезы
```python
>>> invoice = """
...
0.....6.................................40........52...55........
... 1909 Pimoroni PiBrella                      $17.50 3 $52.50
... 1489 6mm Tactile Switch x20                  $4.95 2 $9.90
... 1510 Panavise Jr. - PV-201                  $28.00 1 $28.00
... 1601 PiTFT Mini Kit 320x240                 $34.95 1 $34.95
... """

SKU = slice(0, 6)
DESCRIPTION = slice(6, 40)
UNIT_PRICE = slice(40, 52)
QUANTITY = slice(52, 55)
ITEM_TOTAL = slice(55, None)
line_items = invoice.split('\n')[2:]

for item in line_items:
    print(item[UNIT_PRICE], item[DESCRIPTION])

# $17.50 Pimoroni PiBrella
# $4.95 6mm Tactile Switch x20
# $28.00 Panavise Jr. - PV-201
# $34.95 PiTFT Mini Kit 320x240
```

#### Memoryview
Встроенный класс `memoryview` – это тип последовательности в общей памяти, который позволяет работать со срезами массивов, ничего не копируя. 

>[!info] По существу, **memoryview** – это обобщенная структура массива NumPy, встроенная в сам язык Python (но без математических операций). Она позволяет разделять память между структурами данных (например, изображениями в библиотеке PIL, базами данных SQLlite, массивами NumPy и т. д.) без копирования. Для больших наборов данных это очень важно.

#### СОРТИРОВКА
`sort()`    сортирует сам список (сортировка на месте)
`sorted()` возвращает новый отсортированный список
параметр `reverse=True` для реверса и `key=len` ключ для сортировки с помощью функции(либо своей функции)

Регистронезависимая сортировка: `sorted(<string>, key=str.lower)`

Если в параметр key приходит кортеж, сортировка будет выполнена по первому элементу, а при равенстве первых элементов – по последующим.

Сортировка по двум параметрам:
```python
data = [('AB', 80), ('BAA', 80), ('AA', 100), ('AB', 100), ('A', 50)]
res = sorted(data, key=lambda x: (x[0], -x[1]))

# сортировка элемента в первую очередь по длине, а во вторую по алфавиту
sorted(words, key=lambda x: (len(x), x))
```

Можно переопределить в своём классе функцию `__lt__`, например:
```python
def __lt__(self, other):
    return other.b < self.b         # b это поле(свойство) класса
```
   И теперь, если у вас есть список экземпляров класса, их можно отсортировать по этому признаку.
   При вызове `my_class_list.sort()` операция сравнения использует метод экземпляров класса `__lt__` для определения порядка элементов.

#### Пересечение массивов с повторениями
```python
from collections import Counter

common_items = list((Counter(list1) & Counter(list2)).elements())  
```

#### Функции
Переменное кол-во аргументов
```python
def name(h, *args):             # * это распаковка списка
    print(args)                 # создаётся кортеж (7, 4)

name(6, 7, 4)
```
После `*args` могут аргументы. Обращатся к ним можно только по имени. Например:
```python
name(6, 7, 4, key)
```

#### Переменное кол-во ИМЕНОВАННЫХ аргументов
```python
def func(**kwargs):              # ** это распаковка словаря
    return kwargs
```
```python
>>>func(a=1, b=2, c=3)
{'a': 1, 'c': 3, 'b': 2}
```

#### Значение по умолчанию
```python
def func(a, x=2):
    pass
```
Необязательные значения(у которых есть знач. по умолчанию) ставят после обязательных.

#### Лямбда-функции(анонимные функции)
```python
lambda x: x/5 
```
```python
f = lambda x, y: x * y
```
это аналог
```python
def func(x, y):
    return x * y
```

#### ИТЕРАТОРЫ
>[!info] Это объект реализующий протокол(класс) итератора. Класс должен иметь метод `__next__()` и `__iter__()`.
- Метод `__next__()` должен вернуть следующий элемент или ошибку `StopIteration`
- Метод `__iter__()` возвращает сам объект итератора

Создание собственного итератора:
```python
class Series(object):
    def __init__(self, start, end_v):
        self.current = start
        self.end = end_v

    def __iter__(self):
        return self

    def __next__(self):
        if self.current > self.end:
            raise StopIteration
        else:
            self.current += 1
            return self.current - 1

n_list = Series(1,10)    
print(list(n_list))
```

Объект итератора можно пройти в цикле или вызвать функцию `next(<iter_obj>)`. Также можно создать список например `x = list(<iter_obj>)`

#### ГЕНЕРАТОРЫ
>[!info]  Генераторы в отличии от итераторов - это не протокол, а реализация. Генератор является разновидностью итератора. Отличие итератора от генератора в том, что ==итератор извлекает элементы из коллекции, а генератор порождает элеметы==. Последовательность генератора может быть бесконечна(напр. числа Фибоначчи).

Бывают 2 типа: 
- функция-генератор
- генераторное выражение.

###### Функция-генератор
`Yield` делает из функции объект-генератор. И выдаёт по одной итерации за раз.
>[!info] Вызов функции-генератора вообще не выполняет функцию. Он просто создает и возвращает объект-генератор. Программный код в функции-генератора исполняется только тогда, когда функция `next()` вызывается с объектом-генератором в качестве аргумента

```python
def some():
    with open('text.txt') as r:
        for i in r:
            yield i

p = some()               # p -это объект-генератор. Можно запустить лишь однажды
print(next(p))           # печатает по строке за раз
print(next(p))
print(next(p))
```
>[!info] Используется для возврата из функции с сохранением состояния ее локальных переменных, и при повторном вызове такой функции выполнение продолжается с оператора `yield`, на котором ее работа была прервана.

Генератор чисел фибоначчи:
```python
while True:
    yield b
    a, b = b, a+b
```

>[!warning] ВАЖНО! yield - не замена return. **yield передает управление назад источнику вызова функции — но она это делает лишь временно. инструкция yield приостанавливает функцию и сохраняет ее локальное состояние.**. Операторов yield может быть несколько. Может быть дальше код ПОСЛЕ yield. Функция `next()` проходит код до следующего yield. Также yield может не только отдавать, но и ПРИНИМАТЬ значение:
```python
def subgen():
    message = yield
    print('Subgen received:', message)

x = subgen()
x.send(None)      # нужно для первого перехода к yield, аналог next()
x.send('Okay')    # передача значения для yield
```

`yield from` специальная конструкция берёт на себя передачу данных в подгенератор, передачу исключений, получает результат передаваемый `return`. Передаёт данные из любой последовательности.

######  Выражение-генератор
>[!info] Почти не занимает место, т.к. это выражение, а не ссылка на словарь, лист и т.п.

```python
z = (x*2 for x in h if x % 2 == 0)   # выражение за каждое исполнение выдаёт по одной итерации
```
z - это генратор, далее его можно преобразовать:
```python
arr = list(z)
```
или пройти в цикле:
```python
for i in z:                                   
    print(i)
```
или вызвать следующий элемент:
```python
print(next(z))
```

>[!warning] Как только выражение-генератор было использовано, оно уже не может быть перезапущено или использовано заново.

#### Comprehensions(включения)
>[!info] Списковые включения и генераторные выражения – эффективный способ создания и инициализации последовательностей.

Включение списка:
```python
newlist = [x*2 for x in h]  # Проходим циклом for по послед. h и каждую итерацию x*2
```
Включение множества:
```python
newset = {x*2 for x in h}
```
Включение словаря:
```python
newdict = {x: x*2 for x in h}
newdict = {x: x*2 for x in zip([a, b], [c, d])}
```
Включение с условием:
```python
newlist = [x*2 for x in h if x % 2 == 0]  # Проходим циклом for по послед. h и каждую итерацию x*2, если x чётное число
```

Цикл в цикле 
```python
outer_loop = ('O1', 'O2', 'O3')  
inner_loop = ('I1', 'I2', 'I3')
x = [(outer_item, inner_item) for outer_item in outer_loop for inner_item in inner_loop]

print(x)
# [('O1', 'I1'),
# ('O1', 'I2'),
# ('O1', 'I3'),
# ('O2', 'I1'),
# ('O2', 'I2'),
# ('O2', 'I3'),
# ('O3', 'I1'),
# ('O3', 'I2'),
# ('O3', 'I3')]
```

```python
colors = ['black', 'white']
sizes = ['S', 'M', 'L']
tshirts = [{color, size} for color in colors for size in sizes]   
# [{'S', 'black'},
# {'M', 'black'},
# {'L', 'black'},
# {'S', 'white'}, 
# {'M', 'white'},
# {'L', 'white'}]
```

#### ЗАМЫКАНИЯ
>[!info] Это ВНУТЕННЯЯ функция, которая возвращается из ВНЕШНЕЙ функции и при этом использует чужие переменные(из внешней обл. видимости)
   - Разные замыкания, хранят РАЗНЫЕ состояния и не пересекаются. Можно использовать, чтобы не использовать global.
   - По сути замыкания - это полноценные объекты. У них есть данные и методы. И с этими данными можно работать только так, как указано в методе этой конструкции(т.е. предоставляет интерфейс для работы с данными). К глобальным данным же есть доступ у всех и все могут делать с ними всё что захотят.
```python
def counter():
    count = 0

    def inner(value: int) -> int:
        nonlocal count       # nonlocal для изменения не локальной переменной
        count += value
        return count

    return inner

cnt = counter()         # переменная хранит и данные(count=0) и результат                                #  выполнения(функция inner без выполнения)
print(cnt(1))           # 1 # выполняется функция inner с аргументом 1 и также замыкание УЖЕ хранит в себе данные(count=0)
print(cnt(1))           # 2 
print(cnt(5))           # 7

print(cnt.__closure__[0].cell_contents)   # так можно достучаться до первой                                                # ячейки замыкания, т.е. 7, поскольку                                    # это первый и единственный экземпляр счётчика
```

Это тоже замыкание, т.к. присутствует внутренняя функция, которая возвращается из внешней и при этом использует чужие переменные(передаваемое значение base):
```python
def pow_(base):
    return lambda value: value ** base
```

>[!info] Замыкание - это второй шаг к ООП после `namedtuple`. Хранит данные и предоставляет интерфейс для работы с ними.

#### Декораторы
>[!info] Функция, поволяющая обернуть другую функцию в свой код, тем самым модифицировав её поведение

(*args распаковывает список args, а **kwargs распаковывает словарь kwargs)

```python
def decor(func):                     # сюда попадает имя "make"
    def wrapper(*args, **kwargs):    # сюда попадают аргументы, поскольку на самом деле происходит вызов
                                     #   decor(make)("Hello"), а wrapper - это как раз второй уровень
                                     #   вложенности
        print('Decorator code')
        h = func(*args, **kwargs)    # результат выполнения make
        print('Decorator code')
        return h                     # возвращаем результат выполнения make

    return wrapper                   # возвращается сама функция, а не её результат, чтобы можно было
                                     #   запустить в другом месте
                                     # по сути передаётся невыполненная функция wrapper + данные функц.
                                     #  декор(переданный аргумент - функция make)
```

```python
@decor                  # эта директива означает запустить функцию decor с аргументом make
                        # Т.е. теперь УЖЕ есть данные(замыкание хранит в себе переданный
                        # аргумент make) и невыполненная функция wrapper. make становится 
                        # равной decor(make). Без этой записи можно было бы decor(make)("Hello") 
def make(strg):
    print("---->Original make", strg)

make(strg)              # здесь имя make уже является именем ЗАМЫКАНИЯ и исполняется именно
                        #  этот механизм. Т.е. выполняется функция wrapper с уже имеющимися 
                        #  данными(аргумент - невыполненная функция make)
```

Ещё пример декоратора с параметрами:
```python
def decorator(multiplier: int):
    def wrap(func):
        def inner(*args):
            res = []
            for _ in range(multiplier):
                res.append(f'{func(args[0])} Moto!')
            return res
        return inner
    return wrap


@decorator(3)            # Если без этого синтаксического сахара, то аналог  decorator(3)(my_func)("Hello!")
def my_func(s: str):
    return s[-3:]


print(my_func("Hello!"))             # ['lo! Moto!', 'lo! Moto!', 'lo! Moto!']
```
>Вложенность функций и их аргументы:
>==Аргумент декоратора -> Функция для декорирования -> Аргументы декорируемой функции==
>Т.е. без синт. сахара могут происходить такие вызовы(НА ЭТО НУЖНО ОБРАЩАТЬ ВНИМАНИЕ при построении декоратора):
>- decorator(3)(my_func)("Hello!")
>- decorator(my_func)("Hello!")
>- decorator(my_func)
>
>И соответственно нужно менять уровни вложенности

Если используются несколько декораторов для одной функции, то их последовательность нужно определять снизу вверх(как стек)
```python
@third
@second
@first
def test_func():  
    return 'test'
```

#### Словари
>[!info] Ключём словаря может быть только *хэшируемый объект*. Объект называется *хешируемым*, если имеет хеш-код, который не изменяется на протяжении всего времени его жизни (у него должен быть метод `__hash__()`), и допускает сравнение с другими объектами (у него дол-жен быть метод `__eq__()`). Если в результате сравнения хешируемых объектов оказывается, что они равны, то и их хеш-коды должны быть равны.

```python
d = {'a': 7}
d = dict(a=7)
d = dict.fromkeys([1,2,3,4,5],'value')
d = {a: a ** 2 for a in range(7)}

a = {'a': 1, 'b': 2, 'c': 3}
b = {'z': 0, 'x': -1, 'y': -2}
c = a | b         # Объединение словарей
d = {**a, **b}    # Объединение словарей (с помощью распаковки)
a |= b            # Объединение словарей на месте. Аналог a = a | b
```

###### Методы словарей
|Метод|Описание|
|------|-------|
|`.copy()`|Неполная копия словаря|
|`.items()`|Возвращает спиок кортежей ключ-значение(для циклов, т.е. `for k, v in d.items())`|
|`.keys()`|Возвращает объект-представление в виде списка из ключей|
|`.values()`|Возвращает объект-представление в виде списка из значений|
|`.update()`|Дополняет словарь другим словарём (не создаёт новый словарь!)|
|`.pop('x')`|Удаление с возвратом значения|
|`.popitem()`|Удаление с возвратом значения последнего ключа|
|`.clear()`|удалить все элементы словаря
|`.get('x', 0)`|Получить значение по ключу 'x', если его не существует, то возвращает 0|
|`.setdefault('x', 0)`|Установить значение (если нет ключа 'x', то ДОБАВЛЯЕТ элемент с ключом 'x' и значением 0)|
|`dict.fromkeys(sequence[, value])`|создаёт новый словарь с ключами из последовательности sequence и заданным значением (по умолчанию – None).

Получение значения по ключу
```python
dict[key]             # если ключ не существует, вернёт ошибку
```
```python
dict.get(key)         # если ключ не существует, вернёт None
```

Получение ключа и значения в цикле
```python
for key in dict:
    print(key + dict[key])
```
```python
for key, value in dict.items():
    print(key + value)
```

###### Неизменяемые словари
```python
from types import MappingProxyType  
  
d = {1: 'A', 2: 'B', 3: 'C'}  
d_proxy = MappingProxyType(d)
print(d_proxy)                 # {1: 'A', 2: 'B', 3: 'C'}
d_proxy[2] = 'x'               # TypeError: 'mappingproxy' object does not support item assignment

d[4] = 'D'  
print(d_proxy)                 # {1: 'A', 2: 'B', 3: 'C', 4: 'D'}
```
Произвести изменения через переменную  `d_proxy` невозможно.
Представление `d_proxy` динамическое: любое изменение сразу же отражается.

#### Множества
>[!info] Может состоять только из неизменяемых типов данных(число, кортеж, строка)
   Только уникальные элементы. Неупорядоченная последовательность.
```python
t = {}                             # это будет словарь
y = set(<последовательность>)    
```

###### Инфиксные операции
```python
a | b  # объединение множеств
a & b  # пересечение множеств
a - b  # разность множеств
```

###### Методы множеств
|Метод|Описание|
|------|-------|
|`.add()`|добавление|
|`.discard(x)`|удаление, без ошибок|
|`.remove(x)`|удаление, с ошибками|
|`.pop()`|удалить из множества и вернуть произвольный элемент; если множество пустое, возникает исключение KeyError
|`.clear()`|удалить все элементы множества
|`.union(x)`|объединение (также оператор |), создаёт новое множество
|`.update(x)`|объединение, изменяет множество
|`.intersection(x)`|пересечение множеств (также оператор &)
|`.difference(x)`|разница множеств, элементы, которых нет в X (также оператор -)
|`.issubset()`|является ли множество подмножеством другого
|`.isdisjoint(x)`|проверка того, что данное множество не имеет общих элементов с заданным

#### Строки
>[!info]    \     символ продолжения строки
```python
r'aasdf'                      # игнорирование экранирования
f'asdf{my_var}'               # f-строка
```
- Можно обращатся к строке по индексу. 
-  Можно использовать срезы.
-  Можно использовать in, чтобы узнать есть ли такая последовательность в строке.
```python
"abc" in "abcdefghij"         # проверка на вхождение подстроки
```

###### Методы строк
|Метод|Описание|
|------|-------|
|`.upper()`|перевод в верхний регистр
|`.lower()`|перевод в нижний регистр
|`.capitalize()`|первая буква строки - заглавная
|`.replace('a', 'b')`|замена одной подстроки на другую
|`.split('/')`|разбивает строку по разделителю, возвращает список
|`'/'.join(tuple)`|объединяет все значения в кортеже в строку, используя разделитель
|`.startswith("Че")`|проверка строки, начинается ли с "Че..."
|`.endswith("z")`|проверка строки, заканчивается ли на "z"
|`.find('abc')`|найти первое вхождение подстроки "abc", если нет то -1
|`.rfind('abc')`|найти последнее вхождение подстроки "abc", если нет то -1
|`.index('abc')`|найти первое вхождение подстроки "abc", если нет то исключение
|`.rindex('abc')`|найти последнее вхождение подстроки "abc", если нет то исключение
|`.count('o')`|посчитать сколько символов "o" в строке
|`.title()`|делает заглавной букву в каждом слове
|`.maketrans("a", "b")`|два аргумента одинаковой длины. какаие символы на какие заменять
|`.isupper()`|проверяет, что все буквы заглавные
|`.isalnum()`|возвращает `True`, если все символы в строке являются буквенно-цифровыми и есть хотя бы один символ
|`'*'.center(3, '=')`|устанавливает в центр строку/символ и дописывает кол-во символов(=) по сторонам. Желаемая длина - 3. Вывод =*=

Пример `maketrans()`:
```python
t=str.maketrans("aeiou", "12345")    # а затем транслируем по карте символов
"Hello".translate(t)                 # H2ll4
```

###### Форматирование строк
```python
f'Hello, {name}! I am {python}'
f'{intgr:,}'                        # форматировать число с разделителем, например 23,123,213.21
f'{intgr:.2f}'                      # число с плавающей точкой, выводимое с двумя знаками точности
f'{intgr:>3d}'                      # трехзначное десятичное число, выравниваемое по правому краю
f'{now=:%m-%d-%Y}'                  # форматирование даты now = datetime.datetime.now()
f"{x:0>2}"                          # заполнение нулями СЛЕВА, если меньше 2х цифр
f"{x:g}"                            # убрать лишние нули в дробной части
```

###### T-strings
Шаблонные стоки
```python
from string.templatelib import Interpolation  
  
name = 'Mike'  
profession = 'programmer'  
  
template = t'Hello, {name}, {profession}! How are you?'  
fstring = f'Hello, {name}, {profession}! How are you?'  
  
print(template)  # Template(strings=('Hello, ', ', ', '! How are you?'), interpolations=(Interpolation('Mike', 'name', None, ''), Interpolation('programmer', 'profession', None, '')))
print(fstring)  
  
print(type(template))  # <class 'string.templatelib.Template'>
print(type(fstring))  # <class 'str'>
  
final_parts = []  
  
for element in template:  
    if isinstance(element, Interpolation):
        final_parts.append(str(element.value).title())  
    else:  
        final_parts.append(element)  
  
print(''.join(final_parts))  # Hello, Mike, Programmer! How are you?
```

#### Файлы
###### Поиск путей файлов
```python
import os

for i in os.walk('C:\\Users\user\Desktop\New Folder'):
    print(i)
```
функция заходит по очереди во все подпапки и возвращает кортеж из значений:
(путь к папке, список папок внутри, список файлов)

###### Получить пути всех файлов в папке и подпапках
Пример:
```python
for root, dirs, files in os.walk(directory, topdown=False):
    for name in files:
        print(os.path.join(root, name))
```

Или так(`glob.glob()` получает список имён файлов):
```python
import os
import glob

# искать файлы с расширением `.txt` в текущей директории и всех поддиректориях.
files = [os.path.abspath(x) for x in glob.glob('**/*.txt', recursive=True)]
```

В модуле `glob` поддерживаются следующие символы-шаблоны:
- `*` — соответствует любому количеству символов (включая пустую строку)
- `?` — соответствует одному любому символу
- `[...]` — соответствует одному из символов, заключенных в квадратные скобки (например, `[abc]` соответствует символам `a`, `b` или `c`)

###### Файлы и папки в каталоге
```python
os.listdir('.')
os.path.exists('oops.txt')      # Существование пути
os.makedirs()                   # рекурсивное создание папки и всех подпапок(если они не существуют)
os.mkdir('poems')               # создание папки
os.rmdir('poems')               # удаление папки
os.path.isfile('venv')          # проверка, файл или нет
```

###### shutil
```python
import shutil
shutil.copy('original.txt' 'copy.txt')       # копирует
shutil.move('original.txt' 'copy.txt')       # копирует, а затем удаляет оригинал
```

###### Удаление
```python
os.remove('oops.txt')
```

###### Контекстный менеджер `with`
```python
with open('test.txt', 'w'):
    pass                        # не нужно закрывать файл
                                # когда возникает ошибка with-as позволяет безопасно закрыть файл
                                # и не потерять хотябы часть информации
```
Виды доступа к файлу:
- `'r' `чтение(по умолчанию)
- `'t'` текстовый режим(по умолчанию)
- `'w'` запись, содержимое удаляется, если файла нет, то создаётся
- `'a'` дозапись, если файла нет, то создаётся
- `'b'` бинарный режим
- `'+'` для чтения и записи
- `'b'` означает, что файл бинарный

#### Чтение/запись файла
###### Запись в файл
```
with open('test', 'w') as f:  
    f.write('asdf')
```

###### Запись в файл построчно
```python
with open('test', 'w') as f:  
    f.writelines(["a\n", "b\n", "c\n"])
```

###### Чтение из файла
```python
with open('test') as f:  
    f.write('asdf')
```

###### Чтение из файла построчно
```python
with open('test') as f:  
    while True:  
        line = f.readline()  
        if not line:  
            break
```

###### Копирование файла
```python
with open('test', 'rb') as f:  
    orginal = f.read()  
    with open('test(copy)', 'wb') as w:  
        w.write(orginal)
```

#### CSV
###### Создание csv-файла
```python
import csv
mass = [
    ['Doctor', 'No'],
    ['Rosa', 'Klebb'],
    ['Mister', 'Big'],
]

with open('file.csv', 'wt') as f:
    csvout = csv.writer(f)
    csvout.writerows(mass)  # каждый элемент массива - строчка в csv
    # или
    # csvout.writerow(mass) # весь массив - строчка, каждый элемент - ячейка
    
```

###### Чтение из csv-файла
```python
with open('file.csv', 'rt') as f:
    reader = csv.reader(f)
    return list(reader)
```

>[!info] Если возникает ошибка `_csv.Error: field larger than field limit (131072)` , то csv файл содержит очень большие поля. Программно увеличиваем лимит
```python
csv.field_size_limit(sys.maxsize)
```

###### Чтение в словарь
```python
with open('file.csv', 'rt') as f:
    reader = csv.DictReader(f, fieldnames=['first', 'last'])
    massive = [row for row in reader]
    print(massive)
```

#### Запись Excel файла
Пример записи excel файла. В данном случае аргумент(array) - это функция-генератор:
```python
import xlswriter

def writer(array):
    book = xlswriter.Workbook(r'/home/ufodriver/Documents/example.xlsx')
    page = book.add_worksheet('Товар')

    row = 0
    column = 0

    page.set_column('A:A', 20)
    page.set_column('B:B', 20)
    page.set_column('C:C', 50)
    page.set_column('D:D', 50)

    for item in array:
        page.write(row, column, item[0])
        page.write(row, column+1, item[1])
        page.write(row, column+2, item[2])
        page.write(row, column+3, item[3])
        row += 1

    book.close()
```

#### JSON
```python
import json
```

Вот так можно добавить совместимые с Python JSON псевдонимы. Теперь можно копировать JSON прямо в код.
```python
true, false, null = True, False, None
fruit = {
    "type": "banana",
    "avg_weight": 123.2,
    "edible_peel": false,
    "species": ["acuminata", "balbisiana", "paradisiaca"],
    "issues": null,
}

print(fruit)
#  {'type': 'banana', 'avg_weight': 123.2, 'edible_peel': False,
#  'species': ['acuminata', 'balbisiana', 'paradisiaca'], 'issues':
#  None}
```

###### Запись словаря в JSON-файл
```python
example_dict = {"a": "b", "c": "d"}
   with open("example.json", "w") as f:
       json.dump(example_dict, f, indent=4, ensure_ascii=False) # indent - это отступ, иначе 
                                # всё будет в одну строку; ensure_ascii=False не экранирует
                                # символы и помогает при работе с кириллицей
```

###### Преобразование словаря в JSON строку
```python
json.dumps(data_dict)
```

###### Преобразование JSON файла в словарь
```python
with open("example.json", "r") as f:
    data = json.load(f)                          # .load()      парсит JSON и возвращает словарь
```

###### Преобразование строки JSON в словарь
```python
my_dict = json.loads(input())                    # .loads()     преобразует строку JSON
                            # такой словарь нужно читать методом get(), это обеспечит отсутствие
                            # ошибок KeyErrors, если ключ отсутствует в словаре
```

#### Манипуляции со структурированными данными
Для этого есть Pandas
```python
import pandas

data = pandas.read_csv('file.csv')
print(data)
```

вывод
```
      Doctor     No
   0    Rosa  Klebb
   1  Mister    Big
```

#### PROGRESS BAR
```bash
pip install tqdm
```
```python
from tqdm import tqdm

for i in tqdm(range(1000000)):
    pass
```
вывод
```
73%|███████▎  | 728324/1000000 [00:07<00:02, 99528.93it/s] 
```

#### CONFIG
>[!info] Для удобного чтения/записи конфигов используют пакет configparser

###### запись .ini
```python
import configparser
settings = {}
cfg = configparser.ConfigParser()

cfg.add_section('settings')
cfg.set('settings', 'buff_interval', 60)  
cfg.set('settings', 'buff2_interval', 3600)  
cfg.set('settings', 'threshold', 2000)

with open('config.ini', 'w') as config:  
    cfg.write(config)
```
###### чтение значений
```python
cfg = configparser.ConfigParser()
cfg.read('config.ini')  
settings = cfg['settings']
```

#### Обработка ошибок
```python
try:
    pass
except ValueError:
    pass
except ZeroDivisionError:
    pass
else:
    pass             # здесь код выполняется только если не было ошибок
finally:
    pass             # исполняется ВСЕГДА
```

#### Возбуждение ошибки
```python
raise ValueError("На 32.5 делить нельзя!")
```

#### enumerate
1. Позволяет перебирать коллекцию элементов, отслеживая индекс текущего элемента
```python
enumerate(<iterable>, start=0)
```
>[!info] enumerate object - фактически итератор, генерирует свои выходные элементы лениво и один за другим, только когда их запрашивают

```python
names = ['Bob', 'Alice', 'Guido']
for index, value in enumerate(names):    # Если аргументом будет(names, 1) индексирование будет с 1
    print(f'{index}: {value}')  
```

2. Можно создать класс перечислений
```python
from enum import Enum  
  
class TrafficLight(Enum):  
    RED = "stop"  
    GREEN = "go"  
    YELLOW = "wait"  
  
print(TrafficLight.RED)          # TrafficLight.RED 
print(TrafficLight.RED.name)     # RED
print(TrafficLight.RED.value)    # stop
```
Также можно итерироваться по этому классу
```python
for light in TrafficLight:
    print(light.name, light.value)
```

#### zip() 
Параллельное итерирование по нескольким последовательностям
```python
days = ["sunday", "monday", "thuesday"]
fruits = ["banana", "orange", "peach"]
drinks = ["coffee", "tea", "beer"]
for x, y, z in zip(days, fruits, drinks):
    print(x + y + z)
```
Создание словаря из зип-объекта(созданного из 2х списков)
`new_dict = dict(zip(a, b))`

#### map()
Встроенная функция, которая позволяет обрабатывать и преобразовывать все элементы в итерируемом объекте без использования явного цикла for. Возвращает map object - объект итератора. Т.е. применяет функцию function к каждому элементу последовательности.
```python
map(function, iterable, ...)
```
Например подсчёт кол-ва символов в каждом элементе кортежа:
```python
x = map(len, ('apple', 'banana', 'cherry'))
print(list(x))                        # [5, 6, 6]
```

#### reduce()
Применяет указанную функцию к элементам последовательности, сводя её к единственному значению.
```python
reduce(function, iterable)
```
 func - функция с двумя аргументами
```python
from functools import reduce

f = reduce(lambda x, y: x * y, [2, 3, 4])            # 2 * 3, а потом результат * 4 = 24
```

#### filter()
Фильтрует последовательность. Применяет другую функцию к заданному итерируемому объекту (список, строка, словарь и так далее), проверяя, нужно ли сохранить конкретный элемент или нет.
```python
filter(func, iterable)
```

Например, получаем все слова из списка, начинающиеся на 'а':
```python
arr = ['aaa', 'bbb', 'bbb', 'aba']
f = filter(lambda word: word.startswith('a'), arr)   # возвращает объект-итератор
print(list(f))                                       # преобразуем его в список
      или
filter(str.isalpha, s)     # фильтрует строку по признаку буквы(isalpha возвращает True, если элемент буква)
```

#### all()
`all(iterable)`
Возвращает `True` если все элементы истинны в итерируемом объекте, в противном случае `False`

#### any()
`any(iterable)`
Возвращает `True` если любой из элементов истинный в итерируемом объекте, в противном случае `False`

#### Время и дата(naive)
```python
import datetime

today = datetime.date.today()
yesterday = today - datetime.timedelta(days=1)
tommorow = today + datetime.timedelta(days=1)

current_time = datetime.datetime.now()
print(current_time)                               # 2023-12-05 02:20:50.493830
print(current_time.year)
print(current_time.day)

current_timestamp = current_time.timestamp()      # 1701732256.798
in_str = current_time.strftime("%d_%m_%Y")        # 05_12_2023

   # или
   
import time

now = time.time()      # кол-во секунд с 1.1.1970
time.ctime(now)        # Tue Nov 29 07:17:14 2022
time.monotonic()       # значение, в долях секунды "монотонных" часов, то есть часов,
                       #   которые не могут двигаться назад, т. е. не зависят от
                       #   корректировки/обновления системных часов.

```

#### Временные зоны(aware)
```python
from pytz import timezone  
import datetime

now_with_tz = datetime.datetime.now(timezone('Europe/Moscow'))
```

Список временных зон
```python
import pytz

print(pytz.all_timezones)
```

#### Форматирование времени
```python
now_with_tz.strftime("%d.%m.%Y")            # преобразует кортеж или объект struct_time в строку,
                                            #  как указано в аргументе формата
   
# создание даты из строчки
datetime.datetime.strptime("28.03.2021", "%d.%m.%Y")

# получение строки вида 00:00:00 из переменной с количеством секунд
time.strftime("%H:%M:%S", time.gmtime(seconds))      # gmtime преобразует время, выраженное в
                                                     #  секундах с начала эпохи, в именованный 
                                                     #  кортеж структуры времени
                                                      time.struct_time в UTC
```

#### Измерение времени выполнения
###### timeit
```python
import timeit
timeit.timeit('a, b = 42, 101; a = a ^ b; b = a ^ b; a = a ^ b')
```
```python
timeit.timeit('random.randint(1, 100)', 'import random', number=10000000)  # number - количество раз, которое код прогоняется
```
```python
timeit.timeit(myfunc, number=1000)                    # выполнить функцию myfunc 1000 раз
```

###### cProfile
```python
import cProfile
def add_numbers():
    total = 0
    for i in range(1, 10000001):
        total += 1

cProfile.run('add_numbers()')
```
Обозначения в выводе:
- tottime    общее время выполнения, не считая времени в подфункциях
- cumtime    накопленное время для выполнения функции и ее подфункций

#### Random
```python
import random
.choice([1, 2, 3])                  # Случайное значение из любой последовательности
.sample([1, 2, 3], 2)               # Несколько значений из любой последовательности
.randint(1, 33)                     # Целое значение из диапазона
.randrange(1, 33, 2)                # Целое значение из диапазона с шагом
.random()                           # Вещественное число в диапазоне от 0.0 до 1.0
```

#### itertools
```python
import itertools
.chain()                # Проходит по аргументам, как если бы они были единой последовательностью
.cycle()                # Бесконечный итератор в цикле
.accumulate()           # Подсчитывает. По умолчанию сумму. Можно передать свою функцию, которая
                        #   будет принимать два аргумента и возвращать одно значение
.permutations()         # все возможные перестановки массива
.groupby()              # Создаёт итератор по сгруппированным элементам переданного объекта
                        #   Можно проходить по элементам, каждый раз встречая группу(т.е.
                        #   след одинаковые буквы подряд; или же одна буква), мы получаем 
                        #   ключ(букву) и всю группу x for x, group in groupby(string)]
.zip_longest()          # тоже что и zip(), только последовательности могут иметь разный размер
```

#### math
```python
import math
   .prod()                         # Перемножает элементы последовательности
```

#### COLLECTIONS
###### Counter
Счётчик
```python
from collections import Counter
c = Counter(['a', 'b', 'c', 'c', 'a'])
print(c.most_common(1))            # Два чаще всего встречающихся значения [('a', 2), ('c', 2)]
```

###### OrderedDict
Упорядоченный словарь. (Занимает в 2 раза больше памяти, чем обычный словарь)
```python
from collections import OrderedDict
odict = OrderedDict({1:1, 2:2, 3:3})

   .move_to_end('a')                       # Сдвинуть элемент(указан ключ) в конец
   .move_to_end('a', last=False)           # Сдвинуть элемент(указан ключ) в НАЧАЛО
```

Пример удаления дубликатов в списке *с сохранением порядка следования*
```python
from collections import OrderedDict  
x = [1, 2, 2, 3, 4, 4, 9, 0, 1, 1]  
print(list(OrderedDict(dict.fromkeys(x))))  # [1, 2, 3, 4, 9, 0]
```

###### ChainMap
Нужен для логичского объединения словарей. Не копирует сами словари, а только ссылки на них.
Лучше использовать только для чтения, а менять сами словари. Например для перебора всех словарей сразу.
```python
chain = ChainMap(first_dict, second_dict)
```

###### DefaultDict
Определяет значение по умолчанию для новых ключей заранее, при создании словаря
```python
from collections import defaultdict
new_dict = defaultdict(int)              # Здесь int будет возвращать 0
      
# Значение подставляется при обращении к несуществующему ключу
print(new_dict['sadasdasdas'])           # 0
 
for char in new_dict:
    new_dict[char] += 1                  # Значение уже равно 0

# Можно подставить свою функцию
new_dict = defaultdict(myfunc)           # без скобок в имени функции
```

###### Deque
```python
from collections import deque
```
Двунаправленная очередь. Быстрая вставка. Быстрое чтение. Потокобезопасная.
Создание из списка с ограничением максимальной длины. При вставке лишнего значения, уже существ. там выпадет с другой стороны.
```python
dec = deque(range(10), maxlen=10)  # deque([0, 1, 2, 3, 4, 5, 6, 7, 8, 9], maxlen=10)

dec.append(1)       # deque([1, 2, 3, 4, 5, 6, 7, 8, 9, 1], maxlen=10)
dec.appendleft(2)   # deque([2, 1, 2, 3, 4, 5, 6, 7, 8, 9], maxlen=10)
dec.pop()           # deque([2, 1, 2, 3, 4, 5, 6, 7, 8], maxlen=10)
dec.popleft()       # deque([1, 2, 3, 4, 5, 6, 7, 8], maxlen=10)
dec.rotate(3)       # deque([6, 7, 8, 1, 2, 3, 4, 5], maxlen=10)
```

Для доступа к элементам можно использовать синтаксис списка
```python
dec[-1]
```

###### namedtuple
Именованный кортеж. Промежуточная ступень между процедурным программирование и ООП
```python
Cat = namedtuple('Cat', 'name age color')
tom = Cat('Tom', 4, 'yellow')
ct = Cat._make()
  
print(tom)                            # Cat(name='Tom', age=4, color='yellow')

# Можно обращаться по индексу или по атрибуту
tom[0]
tom.name
```

#### Subprocess
Порождение новых процессов, соединение c потоками стандартного ввода, стандартного вывода, стандартного вывода сообщений об ошибках и получение кодов возврата от этих процессов.
```python
import subprocess

ret = subprocess.call(['date', '-u'])        # вызов комманды shell
ret = subprocess.getoutput('date -u')        # вызов комманды shell и получение результата
```

#### Multiprocessing(Процессы)
>[!info] Используйте процессы для задач, связанных с ограничениями процессора
```python
import multiprocessing
import time
   
def func(num):
    print(f"I am process {num}")

for n in range(4):
    p = multiprocessing.Process(target=func, args=(n,))  # передавать можно только кортеж
    p.start()
    time.sleep(1)                                        # чтобы успела выполнится функция
    p.terminate()
```

Пример с очередью:
```python
import multiprocessing as mp
dishes_list = ['salad', 'bread', 'entree', 'dessert']


def washer(dishes_list, queue):                          #эта функция будет работать из основного процесса
    for dish in dishes_list:
        print('Washing', dish, 'dish')
        queue.put(dish)                                  #положить в очередь

def dryer(queue):                                        #эта функция будет работать из другого процесса
    while True:
        dish = queue.get()                               #взять из очереди
        print('Drying', dish, 'dish')
        queue.task_done()                                #таск выполнен, переход к след. в очереди

dish_queue = mp.JoinableQueue()                           #создаём очередь
dryer_proc = mp.Process(target=dryer, args=(dish_queue,)) #создаём процесс
dryer_proc.daemon = True 
dryer_proc.start()

washer(dishes_list, dish_queue)                           #эта функция будет работать из основного потока
dish_queue.join()                                         #wait for this [thread/process] to complete
```

#### Threading(Потоки)
>[!info] Используйте потоки для задач, связанных с ограничениями ввода-вывода. Если время ОЖИДАНИЯ ответа(сети, диска и т.п) большое, нужно использовать асинхронку

```python
import threading
import time

def func(num):
    print(f"I am thread {num}")

for n in range(4):
    p = threading.Thread(target=func, args=(n,))  # передавать можно только кортеж
    p.start()

# модуль threading не имеет функции terminate()
```

#### Несколько интерпретаторов
>[!info] Также в 3.14 добавлена возможность запуска нескольких интерпретаторов. Они не имеют общей памяти, поэтому невозможна гонка состояний. Это похоже на Multiprocessing.

```python
import time  
from concurrent import interpreters  
  
  
def cpu_task(n: int):  
    return sum([i*i for i in range(n)])  
  
N = 20_000_000  
  
# --------------------------------------------  
# 1. Один интерпретатор, последовательно  
# --------------------------------------------  
  
t0 = time.perf_counter()  
res1 = cpu_task(N)  
res2 = cpu_task(N)  
t1 = time.perf_counter()  
print('Sequential:', round(t1 - t0, 2), 'seconds')  # Sequential: 3.08 seconds
  
# --------------------------------------------  
# 1. Два интерпретатора, параллельно  
# --------------------------------------------  
interp1 = interpreters.create()  
interp2 = interpreters.create()  
  
t0 = time.perf_counter()  
f1 = interp1.call_in_thread(cpu_task, N)  # запуск функции в новом потоке
f2 = interp2.call_in_thread(cpu_task, N)  
# прикол в том, что эти потоки не имеют общего GIL  
  
# Ждём результаты  
res1 = f1.join()  
res2 = f2.join()  
t1 = time.perf_counter()  
print('Parallel:', round(t1 - t0, 2), 'seconds')  # Parallel: 1.56 seconds
```

#### Асинхронный Python
>[!info] Применяется там, где используется I/O. Всё что касается работы с дисками, фаловой системой, БД, сетью. Наибольшая производительность достигается, когда есть БОЛЬШОЕ время ожидания ответа(сети, диска и т.п).
- Когда программа МНОГОПОТОЧНАЯ происходит постоянное преключение между потоками, и на это тратится время. При асинхронном подходе, всё исполняется в одном потоке, поэтому время на переключение НЕ ТРАТИТСЯ.
- При использовании асинхронного подхода, надо использовать АСИНХРОННЫЕ версии библиотек(aiosqlite, aiohttp, aiogram и т.д.), иначе какой-нибудь код подключения к БД станет БЛОКИРУЮЩИМ.

###### Использование asyncio
>[!info] `async def`  декларирование функции, как асинхронной. Такие функции имеют генераторную природу

>[!info] `await`  приостанавливает родительскую корутину до выполнения. При этом передаёт исполнение событийтому циклу(если результат не отдаётся сразу же). В этом случаем можно добавить `asyncio.sleep(0)`

>[!info] `acyncio.Task()` обёртка вокруг корутины, которая планирует запуск этой корутины в событийном цикле, также может отменять выполнение корутины

>[!info] `asyncio.run(main())` запускает событийный цикл с одной задачей -> main(и если main заверится ПРЕЖДЕ остальных адач, зарегистриованных в ней, например, то цикл завершится не дожидаясь их)

```python
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
1. только с версии 3.11
```python
async with asyncio.TaskGroup() as tg:    
    tg.create_task(print1())             
    tg.create_task(print2())
```
2.
```python
task1 = asyncio.create_task(print1())
task2 = asyncio.create_task(print2())

await task1
await task2
```
3.
```python
task1 = asyncio.create_task(print1())
task2 = asyncio.create_task(print2())

await asyncio.gather(task1, task2) 
```
- `.gather` отрабатывает исключения и возвращает их как результат вместе с другими результатами корутин, если добавить аргумент `return_exceptions=True`
-  Чтобы получить результат в порядке выполнения корутин(а не добавления, как обычно), нужно использовать `asyncio.as_completed()`. Она принимает список корутин, а возвращает объект генератора

###### Методы задач
|Метод|Описание|
|-------|----------|
|`.done()`|была ли задача выполнена(bool)
|`.cancelled()`|была ли задача отменена(bool)
|`.cancel()`|отменить задачу(с возбуждением исключения asyncio.CancelledError)
|`.wait_for(aw, timeout)`|ждет, пока awaitable объект `aw` завершится с таймаутом `timeout`. Если истекает таймаут, возбуждается `asyncio.TimeoutError`

Пример `.wait_for()`:
```python
try:
    result = await asyncio.wait_for(long_task, timeout=3)
except asyncio.TimeoutError:
    print('The long task cancelled')
```

Пример выполнения десяти задач асинхронно:
```python
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
```python
import aiohttp  
import asyncio  
from bs4 import BeautifulSoup  
import cProfile  
  
  
async def request(url, session):  
    async with session.get(url) as response:  
        html = await response.text()  
        soup = BeautifulSoup(html, 'lxml')  
        title = soup.find('title').text  
        return title  
  
  
async def request_manager():  
    url = 'https://www.mint-coast.ru/'  
    tasks = []  
  
    async with aiohttp.ClientSession() as session:  
        for _ in range(100):  
            task = asyncio.create_task(request(url, session))  
            tasks.append(task)  
  
        results = await asyncio.gather(*tasks)  
  
    return results  
  
  
cProfile.run('asyncio.run(request_manager())')  # 1.6 seconds average
```

#### Создание PDF из изображений
```python
pip install img2pdf

import img2pdf

img_list['media/1.jpg', 'media/2.jpg', 'media/3.jpg', 'media/4.jpg']
with open("result.pdf", "wb") as f:
    f.write(img2pdf.convert(img_list))
```

#### ZIP архиватор
Запись всех файлов в директории:
```python
import zipfile
import os

z = zipfile.ZipFile('spam.zip', 'w')        # Создание нового архива
for root, dirs, files in os.walk('folder'): # Список всех файлов и папок в директории folder
    for file in files:
        z.write(os.path.join(root,file))    # Создание относительных путей и запись файлов в архив

z.close()
```

Запись одного файла:
```python
with zipfile.ZipFile('spam.zip', 'w') as myzip:
    myzip.write('file')
```

Извлечение содержимого:
```python
import zipfile
z = zipfile.ZipFile('spam.zip', 'r')

z.extract('file')                           # Извлечь файл из архива
z.extractall()                              # Извлечь все файлы
```

#### Хэширование
```python
from hashlib import sha256

sha = sha256('test_string'.encode('utf-8')).hexdigest()
```

###### Надёжное хэширование пароля
```bash
pip install bcrypt
```

```python
import bcrypt
 
# Хэширование пароля
def hash_password(password: str) -> bytes:
    # Генерация соли
    salt = bcrypt.gensalt(rounds=12)
    return bcrypt.hashpw(password.encode('utf-8'), salt)

# Проверка пароля. Сравнение введенного пароля с хэшированным   
def check_password(password: str, hashed_password: bytes) -> bool:  
    return bcrypt.checkpw(password.encode('utf-8'), hashed_password)

password = "my_secure_password"
hashed = hash_password(password)  # b'$2b$12$kKZWLo8d8YJoszQUARJ3jOAD9ZTecGcfNWdiTW98IbOj5pUaZz89S'
is_valid = check_password("my_secure_password", hashed)  # True
```

>[!info] Для каждого пароля должна быть своя рандомная соль. Это делает бессмысленным применение к слитым хэшам радужных таблиц.  Соль - не является секретными данными и включена в итоговую строку хэша. При проверке достаём из БД итоговую хэш строку и получаем из неё соль и последнюю часть (которая хэш из пароля и соли).  Алгоритм `bcrypt`  хэширует пользовательский ввод + соль (это должно полностью совпасть с последней частью итоговой хэш строки из БД)

```
# строго 60 символов
$2b$12$S223D5sFpE9fTKl0xYbJZeX9Q8Wz7aCv1dO0rR4tGyB5nH6v.1

$2b$   12$   S223D5sFpE9fTKl0xYbJZe   X9Q8Wz7aCv1dO0rR4tGyB5nH6v.1

#   Алгоритм: $2b$
#   Сложность: 12$ (`12` - число итераций = 2¹² = 4096 раундов)
#   Соль: S223D5sFpE9fTKl0xYbJZe (22 символа) Соответствует 16 байтам случайных данных
#   Хэш: X9Q8Wz7aCv1dO0rR4tGyB5nH6v.1 (31 символ) bcrypt(пароль + соль)
```

#### Логгирование
```python
import logging

logger = logging.getLogger()
```

###### Уровни
|Уровень|Числовое значение|
|-------|-----------------|
|Critical|50
|Error|40
|Warning|30
|Info|20
|Debug|10
|Not set|0

>[!info] Логгеры и хэндлеры обрабатывают сообщения ОТ своего уровня и ВЫШЕ

>[!warning] У ЛОГГЕРОВ И ОБРАБОТЧИКОВ СВОИ НЕЗАВИСИМЫЕ УРОВНИ!


###### Loggers
>[!info] Создаёт объект LogRecord и передаёт его в обработчик(handler). Желательно давать имя AppModule, т.е. например MintappViews

```python
logging.basicConfig(level='DEBUG', filename='mylog.log')  # настройка root логгера(любой логгер - потомок root)
logger = logging.getLogger()                   # создаёт новый(если передано имя) или возвращает имеющийся
logger.addHandler(logging.StreamHandler())     # добавить обработчик
logger.propagate = False                       # Остановить распространение LogRecord по иерархии вверх
                                               #  (к root) .По умолчанию True
logging.getLogger('urllib3').setlevel('DEBUG') # получить логгер и установить ему уровень
logger.level                                   # получить уровень
logger.setlevel('DEBUG')                       # установить уровень
logger.handlers()                              # список хэндлеров
logging.Logger.manager.loggerDict              # словарь со всеми логгерами
```

###### Handlers
>[!info] Записывают сообщение туда, куда нам нужно, но прежде передают объект LogRecord в Formatter, чтобы получить строку. Для не сконфигурированного root логгера автоматически создаётся StreamHandler

```python
logging.StreamHandler()
logging.FileHandler('mylog.log')
```

Класс для кастомного обработчика:
```python
class MegaHandler(loggingHandler):
    def __init__(self, filename):
        logging.Handler.__init__(self)
        self.filename = filename

    def emit(self, record):
        msg = self.format(record)
        # Дальше можно с msg делать всё, что угодно, хоть в телегу слать, хоть SMSкой
```

###### Formatters
>[!info] Создают строку из объекта LogRecord

```python
f = logging.Formatter(fmt='{levelname} - {name} - {message}', style='{')
     # Значения для форматирования:  levelname      уровень
                                      name           имя логгера
                                      message        сообщение
                                      asctime        дата/время
                                      module
                                      funcName
                                      lineno
                                      
console_h = logging.StreamHandler()
console_h.setFormatter(f)    # подключаем форматтер не к логгеру, а к обработчику
```

###### Filters
```python
class NewFunctionFilter(logging.Filter):
    def filter(self, record):
        print(dir(record))
        return record.funcName == 'new_function'         # Вернёт True, только если находится
                                                         #  в функции new_function
```

###### Логгирование исключений
```python
try:
    for i in range(10):
        f = i / 0
except:
    logger.debug(f'Exception here i = {i}', exc_info=True)
```

Можно использовать словарь с настройками:
```python
   import logging.config
   logging.config.dictConfig(my_logger_config)

   # кастомный фильтр
   class NewFunctionFilter(logging.Filter):
        def filter(self, record):
            print(dir(record))
            return True

   my_logger_config = {
    'version': 1,
    'disable_existing_loggers': False,

    'formatters': {
        'std_format': {
            'format': '{asctime} - {levelname} - {name} - {message}',
            'style': '{'
        }
    },
    'handlers': {
        'console': {
            'class': 'logging.StreamHandler',
            'level': 'DEBUG',
            'formatter': 'std_format',
            'filters': ['my_filter']
        }
        'my_handler': {
            '()': 'MegaHandler',        # () означает создать экзепляр класса
            'level': 'DEBUG',
            'formatter': 'std_format',
            'filename': 'debug.log'
        }
    },
    'loggers': {
        'app_logger': {
            'level': 'DEBUG',
            'handlers': ['console']
            #'propagate': False
        }
    },
    'filters': {
        'my_filter': {
            '()': NewFunctionFilter      # () означает создать экзепляр класса
        }
    }
    }

```

Пример логгирования:
```python
logging.basicConfig(level='DEBUG', filename='site_log.log')              # Настройка рут логгера
logging.getLogger('urllib3').setLevel('CRITICAL')                        # url3lib логгер будет писать 
                                                                         # только critical, а не всё
logger = logging.getLogger('view_logger')                                # создаём свой логгер

cmd = "df -m / | awk 'NR==2 {print $4}'"                                 # комманда для терминала
output = os.popen(cmd).read()                               # отправляем комманду для ОС и читаем вывод

logger.warning(f'Free disk space is running out! {output} Mb left!')     # пишем в лог с уровнем warning
```
##### Loguru
[Документация](https://loguru.readthedocs.io/en/stable/overview.html#features)
```bash
pip install loguru
```

```python
from loguru import logger

logger.add('logs/main.log', rotation='10mb', level='DEBUG')
# rotation='1 day'
# rotation='12:00'
```
Уровень логгера `'DEBUG'` означает, что запишется `debug` и выше
###### Уровни в Loguru
```python
logger.debug('Hello!)
logger.info('Hello!)
logger.warning('Hello!)
logger.error('Hello!)
logger.critical('Hello!)
```

>[!tip] По умолчанию включён логгер с обработчиком `stderr`. Чтобы записи были без дублей, ДО `logger.add()` нужно удалить дефолтный логгер вызовом `logger.remove()`


###### Уведомления
Пример уведомления в Telegram
```bash
pip install notifiers
```

```python
import os
from loguru import logger
from notifiers.logging import NotificationHandler
from dotenv import load_dotenv                            # pip install python-dotenv

load_dotenv()

params = {
    'token': os.getenv("TOKEN"),
    'chat_id': os.getenv("CHAT_ID")
}

tg_handler = NotificationHandler('telegram', defaults=params)

logger.add(tg_handler, level='CRITICAL')

logger.add('logs/log.log', rotation='10 mb', level='DEBUG')

logger.critical("Не загружается страница")
```
Токен бота можно взять у `@BotFather`. Создать группу и пригласить туда бота.
[Как получить chat_id](https://stackoverflow.com/questions/32423837/telegram-bot-how-to-get-a-group-chat-id/32572159#32572159)

#### Pydantic
>[!info] **Pydantic** — это библиотека Python для валидации данных, использующая аннотации типов для проверки и управления данными. Она обеспечивает строгую валидацию, автоматическое преобразование типов и сериализацию. Она позволяет создавать **модели данных** (схемы), которые определяют, как должны выглядеть данные, и автоматически проверяют их на соответствие заданным правилам. Валидация в Pydantic позволяет гарантировать, что данные соответствуют заданным типам, ограничениям и пользовательским правилам.

[Документация](https://docs.pydantic.dev/latest/)
[Документация PyPI](https://pypi.org/project/pydantic/)
[Статья на Хабре](https://habr.com/ru/companies/amvera/articles/851642/)

```bash
pip install pydantic
```

Pydantic предоставляет возможность задать любую комбинацию следующих проверок:
- обязательные и необязательные
- значение по умолчанию, если не указано, но требуется
- ожидаемый тип или типы данных
- ограничения диапазона значеий
- другие проверки на основе функций, если необходимо
- сериализацию и десериализацию

###### Модели в Pydantic
Модели в Pydantic наследуются от класса `BaseModel`. Каждая модель описывает набор полей, которые представляют собой структуру данных и условия для их валидации.
```python
from pydantic import BaseModel

class Message(BaseModel):
    id: int
    content: str

# Пример использования
message = Message(id=1, content="Hello, Pydantic!")
print(message)  # Вывод: id=1 content='Hello, Pydantic!'
print(message.dict())  # Сериализация в словарь: {'id': 1, 'content': 'Hello, Pydantic!'}
print(message.json())  # Сериализация в JSON: {"id": 1, "content": "Hello, Pydantic!"}
```

###### Типы в Pydantic
**Базовые типы Python**
- `int`: Целое число (например, `id: int`).
- `str`: Строка (например, `content: str`).
- `float`: Число с плавающей точкой (например, `price: float`).
- `bool`: Логическое значение (например, `is_active: bool`).
- `list`: Список (например, `tags: list[str]` для списка строк).
- `dict`: Словарь (например, `metadata: dict[str, str]`).
- `set`: Множество (например, `unique_tags: set[str]`).

**Специализированные типы Pydantic**
Pydantic предоставляет встроенные типы для сложных случаев, которые автоматически проверяют данные:
- `EmailStr`: Проверяет, что строка — валидный email (требуется `pip install email-validator`).
- `HttpUrl`: Проверяет, что строка — валидный URL.
- `PositiveInt`: Проверяет, что число положительное.
- `NegativeFloat`: Проверяет, что число с плавающей точкой отрицательное.
- `constr`: Ограничивает строки (например, по длине или регулярному выражению).
- `conint`: Ограничивает целые числа (например, по диапазону).
```python
from pydantic import BaseModel, EmailStr, HttpUrl, PositiveInt

class User(BaseModel):
    email: EmailStr
    website: HttpUrl
    age: PositiveInt
```

**Опциональные поля и значения по умолчанию**
Вы можете сделать поле необязательным с помощью `Optional` из модуля `typing` или задать значение по умолчанию
```python
from pydantic import BaseModel
from typing import Optional

class Message(BaseModel):
    id: int
    content: str
    tags: Optional[list[str]] = None   # Необязательное поле, по умолчанию None
    priority: int = 0                  # Поле с значением по умолчанию
```

**Списки и словари с типами**
Pydantic позволяет задавать типы для элементов списков или словарей
```python
from pydantic import BaseModel
from typing import List, Dict

class Message(BaseModel):
    id: int
    content: str
    tags: List[str]            # Список строк
    metadata: Dict[str, int]   # Словарь с ключами-строками и значениями-числами
```

###### Вложенные модели
Модели могут содержать другие модели, что полезно для сложных структур данных
```python
from pydantic import BaseModel

class Author(BaseModel):
    name: str
    email: EmailStr

class Message(BaseModel):
    id: int
    content: str
    author: Author  # Вложенная модель
```
```python
message = Message(
    id=1,
    content="Hello",
    author=Author(name="Alice", email="alice@example.com")
)
```

###### Валидация полей
**Типы валидации в Pydantic**:
1. **Автоматическая валидация**: Проверка типов данных.
2. **Валидация через `Field`**: Ограничения на поля (например, длина строки, диапазон чисел).
3. **Кастомная валидация полей**: Пользовательские проверки с помощью `@field_validator`.
4. **Валидация на уровне модели**: Проверки, учитывающие несколько полей, с помощью `@model_validator`.

 **Валидация через `Field`**
 Модуль `pydantic.Field` позволяет задавать ограничения для полей, такие как длина строки, диапазон чисел, регулярные выражения и т.д. По умолчанию все поля в модели обязательные.

Основные параметры `Field`
- `default`: Значение по умолчанию.
- `default_factory`: Функция для генерации значения по умолчанию (например, `list`).
- `alias`: Альтернативное имя поля в JSON.
- `...` (ellipsis): Указывает, что поле обязательно.
- `min_length` / `max_length`: Минимальная/максимальная длина строки.
- `pattern`: Регулярное выражение для строки.
- `gt` / `ge`: Greater than (больше) / Greater or equal (больше или равно).
- `lt` / `le`: Less than (меньше) / Less or equal (меньше или равно).
- `multiple_of`: Число должно быть кратным указанному значению.
- `max_digits` / `decimal_places`: Ограничения для чисел с плавающей точкой.

```python
from pydantic import BaseModel, Field

class Message(BaseModel):
    id: int = Field(..., gt=0)  # Явно указываем что это обязательное поле, число больше 0
    content: str = Field(min_length=1, max_length=500, pattern=r"^[a-zA-Z0-9\s!,.?]*$")  # Строка 1-500 символов, только буквы, цифры, пробелы и знаки
    priority: float = Field(default=0.0, ge=0.0, le=10.0)  # Число от 0 до 10
```

Регулярные выражения
Параметр `pattern` заменил `regex` в Pydantic 2. Для валидации Slug мы можем использовать это регулярное выражение:

```python
from pydantic import BaseModel, Field, ValidationError

class Article(BaseModel):
    text: str
    slug: str = Field(pattern=r'^[-a-zA-Z0-9_]+$')

# Пример использования:
valid_article = Article(text="Some text", slug="valid-slug_123")
invalid_article = Article(text="Some text", slug="Invalid Slug!") # Ошибка
invalid_article2 = Article(text="Some text", slug=" ") # Ошибка
```

###### Декораторы в Pydantic
**Кастомная валидация с `@field_validator`**
Для сложных проверок, выходящих за рамки `Field`, используется декоратор `@field_validator`. Он позволяет писать пользовательские функции для проверки отдельных полей.
```python
from pydantic import BaseModel, Field, field_validator

class Message(BaseModel):
    content: str = Field(min_length=1, max_length=500)

    @field_validator("content")
    @classmethod
    def check_forbidden_words(cls, value):
        forbidden_words = ["spam", "offensive"]
        if any(word in value.lower() for word in forbidden_words):
            raise ValueError("Message contains forbidden words")
        return value
```
Как это работает
- `@field_validator("content")`: Применяет валидатор к полю `content`.
- Функция проверяет, содержит ли строка запрещенные слова.
- `raise ValueError`: Вызывает ошибку с пользовательским сообщением, если валидация не проходит.
- `return value`: Возвращает проверенное (или преобразованное) значение.

Ещё пример
```python
from pydantic import BaseModel, field_validator

class User(BaseModel):
    age: int
    
    @field_validator('age')
    def check_age(cls, value):
        if value < 18:
            raise ValueError('Возраст должен быть больше 18 лет')
        return value
```

**Кастомная валидация с `@computed_field`**
- `@computed_field` — поле, которое вычисляется на основе других данных в модели. Его можно использовать для автоматической генерации значений, а также для валидации.
```python
from pydantic import BaseModel, computed_field

class User(BaseModel):
    name: str 
    surname: str
    
	@computed_field
	def full_name(self) -> str:
	    return f"{self.name} {self.surname}"
```

###### Методы
`model_dump()` — преобразуют модель в словарь Python
```python
data = user.model_dump()
```

`model_dump_json()` — преобразуют модель в JSON-строку.
```python
json_data = user.model_dump_json()
```

###### Передача данных в модель
1. напрямую при создании экземпляра.
```python
user = User(name="Oleg", age=30)
```

 1. Можно передать значения полей с помощью распаковки словарей `**`
```python
user_data = {"name": "Oleg", "age": 30}
user = User(**user_data)
```

###### default_factory
>[!info] Иногда требуется генерировать значения для полей **динамически**. Для этого используется параметр `default_factory`, который принимает функцию для генерации значения.

```python
from uuid import uuid4

class Item(BaseModel):
    id: str = Field(default_factory=lambda: uuid4().hex)
```

###### Исключение полей из сериализации
```python
class User(BaseModel):
    password: str = Field(exclude=True)
```

###### Расширенные возможности через Annotated
Этот способ позволяет добавить метаданные и валидацию более гибко и точно.
```python
from typing_extensions import Annotated

class User(BaseModel):
    id: Annotated[int, Field(gt=0)]
    name: Annotated[str, Field(min_length=2, max_length=50)]
    email: Annotated[str, Field(regex=r"[^@]+@[^@]+\.[^@]+")]
    role: Annotated[str, Field(default="user")]
```

###### Конфигурация моделей в Pydantic
```python
from pydantic import BaseModel, ConfigDict

class MyModel(BaseModel):
    model_config = ConfigDict(from_attributes=True)
```

Основные опции ConfigDict
- `from_attributes=True` — позволяет создавать объект модели напрямую из атрибутов Python-объектов (например, когда поля модели совпадают с атрибутами другого объекта). Чаще всего опция применяется для преобразования моделей ORM к моделям Pydantic.
    
- `str_to_lower, str_to_upper` - преобразование всех строк модели в нижний или верхний регистр
    
- `str_strip_whitespace` - cледует ли удалять начальные и конечные пробелы для типов str (аналог strip)
    
- `str_min_length, str_max_length` - задает максимальную и минимальную длину строки для всех строковых полей
    
- `use_enum_values` - cледует ли заполнять модели значениями, выбранными из перечислений, вместо того чтобы использовать необработанные значения? Это часто требуется при работе с моделями ORM, в которых колонки определены как перечисления (ENUM).


#### ТЕСТЫ
###### Assert
>[!info] Работает как логическое выражение, проверяя, является ли заданное условие истинным или ложным. Если условие истинно, то ничего не происходит и выполняется следующая строка кода. Если же условие ложно, оператор assert останавливает выполнение программы и выдает ошибку `AssertionError`

>[!info] Это инструмент отладки, не следует использовать для простой проверки истинности(т.к. инструкции `assert` глобально отключаемы). Также убедитесь что не передаёте котеж, т.к. в таком случае `assert` всегда будет истинным

```python
def pw2(n):
    return n * n
 
assert(pw2(2) == 4)
assert(pw2(4) == 16)
assert(pw2(5) == 2)                                # Здесь будет AssertionError
assert(pw2(5) == 2, 'asdfg')                       # всегда будет истинно, т.к. происходит
                                                   #  проверка на истинность для кортежа 
```

###### Unittest
>[!info] Модульное тестирование. При модульном тестировании предполагается тестирование конкретного поведения конкретной функции. Имя каждого тестирующего метода обязательно должно начинаться со слова `test`. При отсутствии такого префикса тест не будет выполняться. Тесты запускаются при вызове метода `unittest.main`.

>[!info] Всякий раз, когда выполняется этот тест-кейс, сначала выполняется метод `setUp()`

Пример тестирования конкретного поведения гипотетического класса Calculator.
Создаём папку tests. Создаём файл calculator_test.py
```python
import unittest
from calculator import calc

class CalculatorTest(unittest.TestCase):
    def setUp(self):
        self.calculator = Calculator()        # инициализация перед тестами чего-то

    def test_plus(self): 
        self.assertEqual(calc(2, 2, '+'), 4)  # позитивный тест. Проверка, что 2 + 2 равняется 4

    def test_minus(self):
        self.assertEqual(calc(56, 32, '-'), 24)

    def test_no_sign(self):              # негативный тест. Проверка, что возбуждается ValueError
        with self.assertRaises(ValueError) as e:
            calc(2, 6, 'sdad')
        self.assertEqual('Допустимые знаки +-*/', e.exception.args[0])  # Проверка на соответствие текста ошибки

unittest.main()
```

>[!tip] Добавление автозапуска тестов. В PyCharm, там где запуск программы -> Edit configurations -> + -> Python tests -> unittests.
>Target: `custom`
>Additional arguments: `discover -s tests -p '*_test.py'`  (Найти все тесты в папке tests, которые соответствуют паттерну `'*_test.py')`

#### РАЗРАБОТКА ЧЕРЕЗ ТЕСТИРОВАНИЕ(Test-Driven Development - TDD)
>Написание тестов до начала написания исходного кода

**Red-Green-Refacto**r
- Красный (red): тесты не проходят. Так и должно быть. Ведь вы еще не написали код;
- Зеленый (green): все тесты проходят при изменении кода;
- Рефакторинг (refactor): чистка и улучшение кода, устранение дублирования.

#### FLET
>[!info] Это фреймворк для разработки кроссплатформенных приложений

[Документация](https://flet.dev/docs/)

```bash
pip install flet
```

Запуск десктоп приложения
```bash
flet run counter.py
```

Запуск в браузере
```bash
flet run --web counter.py
 # ИЛИ
flet run --web --port 8000 counter.py
```

###### Минимальное приложение
```python
import flet as ft

def main(page: ft.Page):
    page.title = "Flet App"

ft.app(target=main)
```

Добавление строчки с виджетами
```python
page.add(  
    ft.Row(  
        [  
            ft.IconButton(ft.Icons.REMOVE, on_click=minus_click),  
            txt_number,  
            ft.IconButton(ft.Icons.ADD, on_click=plus_click),  
            ft.Checkbox()  
        ],  
        alignment=ft.MainAxisAlignment.CENTER,  
    )  
)
```

###### Пример навигации
```python
def navigate(e):  
    index = page.navigation_bar.selected_index  
    page.clean()  
  
    if index == 0:  
        page.add(panel_1)  
    elif index == 1:  
        page.add(panel_2)  
  
panel_1 = ft.Row(  
    [  
        ft.IconButton(ft.Icons.REMOVE, on_click=minus_click),  
        txt_number,  
        ft.IconButton(ft.Icons.ADD, on_click=plus_click),  
        ft.Checkbox()  
    ],  
    alignment=ft.MainAxisAlignment.CENTER,  
)  
  
panel_2 = ft.Row(  
    [  
        ft.Text(value="HELLO!"),  
    ],  
    alignment=ft.MainAxisAlignment.CENTER,  
)  
  
page.add(panel_1)  
  
page.navigation_bar = ft.NavigationBar(  
    destinations=[  
        ft.NavigationDestination(icon=ft.Icons.VERIFIED, label='Registration'),  
        ft.NavigationDestination(icon=ft.Icons.VERIFIED_USER_OUTLINED, label='Something')  
    ], on_change=navigate  
)  
page.update()
```

![[2024-11-19_16-33.png|400]]

###### Пример File Picker
```python
import flet as ft  
  
def main(page: ft.Page):  
    page.title = "Flet picker example"  
    page.theme_mode = 'dark'  
    page.vertical_alignment = ft.MainAxisAlignment.CENTER  
  
    page.window.width = 850  
    page.window.height = 600  
    page.window.resizable = False  
  
    selected_files = ft.Text(value='Uploaded file')  
  
    def pick_result(e: ft.FilePickerResultEvent):  
        if not e.files:  
            selected_files.value = 'Ничего не выбрано'  
        else:  
            selected_files.value = ''  
            path = ''  
            for elem in e.files:  
                path = elem.path  
  
            selected_files.value = path  
            with open(path, 'r') as f:  
                print(f.read())  
  
        page.update()  
  
    pick_dialog = ft.FilePicker(on_result=pick_result)  
    page.overlay.append(pick_dialog)  
    page.add(  
        ft.Row(  
            [  
                ft.ElevatedButton(  
                    'Выбрать файл',  
                    icon=ft.Icons.UPLOAD_FILE,  
                    on_click=lambda _: pick_dialog.pick_files(allow_multiple=False)  
                ),  
                selected_files  
            ],  
            alignment=ft.MainAxisAlignment.CENTER,  
        )  
    )  
  
  
ft.app(main)
```

![[2024-11-20_11-30.png|400]]

###### Делаем билд через PyInstaller
```bash
pip install pyinstaller
```

```bash
flet pack main.py
```

#### Переменные в Python
В Python **всё является объектом**. 
Когда мы пишем `x = 42`, мы не создаём переменную `x`, которая содержит значение 42. Вместо этого происходит следующее:

1. Python создаёт объект типа `int` со значением 42 в куче (heap)
2. Создаётся имя `x` в пространстве имён (namespace)
3. Имя `x` связывается с объектом через ссылку
    
Это принципиальное отличие от языков вроде C, где переменная - это именованная область памяти, содержащая значение.

###### Объекты изнутри
Каждый объект в Python имеет три обязательных атрибута:
```python
pythonx = 42
print(f"Значение: {x}")             # 42

print(f"Тип: {type(x)}")            # <class 'int'>
print(f"ID (адрес): {id(x)}")       # Уникальный идентификатор в памяти
print(f"Размер: {x.__sizeof__()}")  # Размер в байтах
```

ID объекта - это его адрес в памяти (в CPython)

###### Архитектура памяти Python: многоуровневая система
Python использует сложную систему управления памятью, состоящую из нескольких уровней:
1. Системный уровень (malloc/free)

На самом низком уровне Python взаимодействует с системными функциями выделения памяти. Однако прямое обращение к malloc/free было бы неэффективно для множества мелких объектов.

2. Менеджер памяти Python (PyMalloc)

Python реализует собственный аллокатор памяти, оптимизированный для работы с объектами размером до 512 байт.

```python
import sys  
  
# Информация о состоянии менеджера памяти  
def memory_info():  
    import gc  
    print(f"Количество объектов: {len(gc.get_objects())}")  
    print(f"Статистика GC: {gc.get_stats()}")  
  
    # В Python 3.13 добавлены новые методы мониторинга  
    if hasattr(sys, 'getallocatedblocks'):  
        print(f"Выделено блоков: {sys.getallocatedblocks()}")
```

3. Объектные аллокаторы

Каждый тип объекта может иметь свой специализированный аллокатор:
- **Integers**: кеширование малых чисел (-5 до 256)    
- **Strings**: интернирование строк    
- **Lists**: предварительное выделение места для роста
- **Dicts**: оптимизированные структуры с Python 3.6+

В Python **всё передаётся по ссылке на объект** (object reference). Но поведение зависит от того, изменяемый объект или нет.
