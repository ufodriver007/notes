![[fastapi.png]]

[Документация](https://fastapi.tiangolo.com/ru/)
[Metanit](https://metanit.com/python/fastapi/)

FastAPI
```bash
pip install "fastapi[standard]"
    # или
pip install fastapi[all]
```

Uvicorn(ASGI-сервер)
```bash
pip install uvicorn
```

Минимальное приложение
```python
from fastapi import FastAPI  
  
app = FastAPI()  
  
@app.get('/some')  
def getsome():  
    return 'Some text'
```

###### Параметры для приложения
Дебаг режим
```python
from fastapi import FastAPI

app = FastAPI(debug=True)
```

Название API , отображаемое в Swagger UI и ReDoc
```python
app = FastAPI(title="My First Project")
```

Краткое описание API, которое будет отображаться в документации
```python
app = FastAPI(summary="My CRUD application.")
```

Подробное описание API, которое будет отображаться в документации
```python
app = FastAPI(description="The CRUD application supports **writing**, *reading*, updating, and deleting posts.")
```

Версия вашего API задаётся через параметр `version`
```python
app = FastAPI(version="0.0.1")
```

Параметр `openapi_url` определяет URL, по которому доступна схема OpenAPI в формате JSON. По умолчанию это `"/openapi.json"`
```python
app = FastAPI(openapi_url="/api/v1/openapi.json")
```

Для группировки операций в документации используется параметр `openapi_tags`. Он принимает список словарей, где каждый тег описывает группу маршрутов, например, `[{"name": "users", "description": "User operations"}]`. Это помогает организовать документацию, особенно если вы используете теги в маршрутах, таких как `@app.get("/users/", tags=["users"])`:
```python
app = FastAPI(openapi_tags=[{"name": "users", "description": "User operations"}])
```

Автоматическая документация доступна через Swagger UI и ReDoc, пути к которым задаются параметрами `docs_url` и `redoc_url`. По умолчанию они установлены как `"/docs"` и `"/redoc"`, но их можно изменить или отключить, установив `None`:
```python
app = FastAPI(docs_url="/custom-docs", redoc_url=None)
```

Контактная информация для API указывается через параметр `contact`, который принимает словарь с полями `name`, `url` и `email`. Это улучшает документацию, показывая, к кому обращаться:
```python
app = FastAPI(contact={
    "name": "John Doe",
    "url": "https://example.com",
    "email": "john.doe@example.com"
})
```

###### Запуск
Внешний
>[!info]  `--reload`: перезапуск сервера после изменения кода. Делайте это только во время разработки.

Здесь `uvicorn` запускает модуль(файл) `main`, а в нём экземпляр приложения `app`
```bash
uvicorn main:app --reload
```

Приямо из кода
```python
from fastapi import FastAPI  
import uvicorn
  
app = FastAPI()  
  
@app.get('/some')  
def getsome():  
    return 'Some text'

if __name__ == "main":
    uvicorn.run("main:app")  # файл main, приложение app
```

###### Структура приложения
**1. Простая структура (для маленьких проектов)**
Этот подход подходит для небольших приложений с ограниченным количеством endpoints. Все файлы находятся в одном каталоге.
```python
myproject/
├── main.py         # Основной файл приложения
└── models.py       # Модели данных (Pydantic)
```

**2. Структура по функциональным областям**
Для более крупных проектов лучше разделить код по функциональным областям.
```python
myproject/
├── main.py         # Основной файл приложения
├── users/          # Модуль, отвечающий за пользователей
│   ├── __init__.py
│   ├── routes.py   # Маршруты, связанные с пользователями
│   ├── models.py   # Модели пользователей (Pydantic)
│   └── services.py # Бизнес-логика для пользователей
├── products/       # Модуль, отвечающий за продукты
│   ├── __init__.py
│   ├── routes.py
│   ├── models.py
│   └── services.py
└── database.py     # Подключение к базе данных
```

- Каждый подкаталог (`users`,  `products`) содержит код, связанный с конкретной функциональной областью.
- `routes.py`: Определяет маршруты FastAPI для данной функциональной области.
- `models.py`: Определяет модели Pydantic для данных.
- `services.py`: Содержит бизнес-логику, не связанную непосредственно с FastAPI.
- `database.py`: Содержит функции для взаимодействия с базой данных.

**3. Расширенная структура с использованием слоёв (Layers)**
Для очень больших и сложных проектов рекомендуется использовать слоистую архитектуру, чтобы обеспечить лучшую организацию кода и повысить его поддерживаемость.
```python
myproject/
├── main.py         # Основной файл приложения
├── api/            # API слой (FastAPI)
│   ├── __init__.py
│   └── routes.py
├── application/    # Прикладной слой (бизнес-логика)
│   ├── __init__.py
│   ├── users/
│   │   └── services.py
│   └── products/
│       └── services.py
├── infrastructure/ # Инфраструктурный слой (взаимодействие с внешними системами)
│   ├── __init__.py
│   ├── database.py
│   └── external_api.py
└── models/         # Модели данных (Pydantic)
    ├── __init__.py
    ├── user.py
    └── product.py
```
- `api`: Содержит только код FastAPI и определяет маршруты.
- `application`: Содержит бизнес-логику приложения.
- `infrastructure`: Взаимодействие с базой данных, внешними API и другими внешними системами.
- `models`: Определяет модели Pydantic.

#### Части запроса
FastAPI легко может извлечь все части запроса:
1. Header
2. Path
3. Query
4. Body

###### Path. Динамический путь

Пример получения динамической части url (например `/some/33`)
```python
@app.get('/some/{new_id}')  
def getsome(new_id: int):  
    return f'Your new ID: {new_id}'
```

Если есть функция для обработки маршрута `/some/test`, то она дожна быть *ВЫШЕ В КОДЕ*, иначе её обработкой займётся функция `getsome`.

###### Query. Получение GET параметров
```python
@app.get('/some}')            # поскольку в пути нет {} FastAPI понимает,
def getsome(getparam):        #   что это query параметры
    return f'Your get param: {getparam}'
```

  Опциональные (необязательные) параметры
```python
from fastapi import FastAPI  
from typing import Optional  
  
app = FastAPI()  
  
@app.get('/hi')  
def great(who: Optional[str] = 'Default'):  
    return f"Hello, {who}!"
```

**Получение списков значений**
Класс Query из модуля fastapi позволяет указать, что параметр запроса является списком, используя тип `list` с аннотацией типа (например, `list[str]`). FastAPI автоматически собирает все значения параметра в список.

Например, запрос `[http://127.0.0.1:8000/user?people=Tom&people=Sam&people=Bob](http://127.0.0.1:8000/user?people=Tom&people=Sam&people=Bob)` передаёт три значения для параметра `people`, которые FastAPI интерпретирует как список.

```python
from typing import Annotated

from fastapi import FastAPI, Path, Query

app = FastAPI()


@app.get("/user")
async def search(people: Annotated[list[str], Query()]) -> dict:
    return {"user": people}
```

###### Body. Извлечение информации из тела запроса
`Body(embed=True)` означает, что мы получаем значение `who` из тела запроса в формате JSON. Если этого не будет, FastAPI будет ожидать просто данные, не JSON.

Получение конкретного значения
```python
from fastapi import FastAPI, Body

app = FastAPI()

@app.post('/hi')  
def great(who: str = Body(embed=True)):  
    return f"Hello, {who}!"
```

Получение всех значений
```python
from fastapi import FastAPI, Body  
from typing import Optional, Any, Dict

app = FastAPI()

@app.post('/hi')  
def great(body: Dict[str, Any] = Body()):  
    return body
```

Здесь параметр `message: str = Body(...)` указывает, что строка ожидается в теле запроса. Символ `...`означает, что поле обязательно. Это делает запрос безопасным и позволяет передавать большие данные.
```python
@app.post("/messages", status_code=status.HTTP_201_CREATED) 
async def create_message(message: str = Body(...)) -> str: 
    current_index = max(messages_db) + 1 if messages_db else 0 
    messages_db[current_index] = message 
    return "Message created!"
```

###### Header. Получение конкретного заголовка
Возврат заголовка с именем `User-Agent`
```python
from fastapi import FastAPI, Header

app = FastAPI()

@app.post('/hi')  
def great(user_agent: str = Header()):  
    return user_agent
```

>[!info] FastAPI переводит ключи HTTP заголовков в нижний регистр и преобразует дефис(-) в подчёркивание(_)

##### HTTP-ответы
По-умолчанию FastAPI преобразует всё, что вы возвращаете из функции в формат JSON. Поэтому HTTP-ответ содержит строку заголовка `Content-type: application/json`.

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
###### Валидация ответа
**Pydantic и `response_model`**
Параметр **`response_model`** в декораторах FastAPI (например, `@app.get(..., response_model=...)`) указывает, какая модель Pydantic будет использоваться для формирования и валидации ответа API.

**Валидация параметров пути**
Класс `**Path**` используется для определения параметров пути в маршрутах FastAPI. Он не только указывает, что параметр является частью пути, но и позволяет задавать правила валидации и метаданные, которые отображаются в документации Swagger UI и ReDoc.

*Параметры класса Path*
- **Метаданные**:
    - `title`: Название параметра для документации.
    - `description`: Подробное описание параметра.
    - `examples`: Пример значения параметра.
    - `include_in_schema`: Логический параметр, определяющий, включать ли параметр в схему OpenAPI (по умолчанию True).
        
- **Правила валидации**    
    - `min_length`: Минимальная длина строки.
    - `max_length`: Максимальная длина строки.
    - `pattern`: Регулярное выражение для проверки строки.
    - `gt`: Значение должно быть больше указанного (для чисел).
    - `ge`: Значение должно быть больше или равно указанному.
    - `lt`: Значение должно быть меньше указанного.
    - `le`: Значение должно быть меньше или равно указанному.
    
```python
from fastapi import FastAPI, Path

app = FastAPI() 

@app.get("/user/{username}/{age}") 
async def login(username: str = Path(min_length=3, max_length=15, description='Enter your username', example='Ilya'),
                age: int = Path(ge=0, le=100, description="Enter your age")) -> dict:
     return {"user": username, "age": age}
```

Пример валидации по паттерну регулярного выражения
```python
from typing import Annotated
from fastapi import FastAPI, Path, Query

app = FastAPI()

@app.get("/user/{username}")
async def login(
        username: Annotated[
            str, Path(min_length=3, max_length=15, description='Enter your username',
                      example='permin0ff')],
        first_name: Annotated[
            str | None, Query(max_length=10, pattern="^J|s$")] = None) -> dict:
    return {"user": username, "Name": first_name}
```

**Валидация Query-параметров**
FastAPI предоставляет класс `Query` из модуля `fastapi` для определения и валидации параметров запросов. Он позволяет задавать правила валидации и метаданные, которые улучшают документацию API в Swagger UI и ReDoc.

Параметры класса Query такие же как у Path(см. выше)

Пример
```python
from typing import Annotated
from fastapi import FastAPI, Path, Query

app = FastAPI()

@app.get("/user/{username}")
async def login(
        username: Annotated[
            str, Path(min_length=3, max_length=15, description='Enter your username',
                      example='permin0ff')],
        first_name: Annotated[str | None, Query(max_length=10)] = None) -> dict:
    return {"user": username, "Name": first_name}
```

Ещё примеры
```python
class ShemeHotel(BaseModel):
    address: str
    name: str
    stars: str
    has_spa: bool

@app.get("/hotels", response_model=list[ShemeHotel])
def get_hotels(
    location:str,
    date_from: date,
    date_to: str,
    has_spa: Optional[bool] = None,
    stars: Optional[int] = Query(None, ge=1, le=5),
):
    hotels = [
        {
            "address": "ул.Гагарина 1",
            "name": "Super Hotel",
            "stars": 5,
        },
    ]
    return hotels
```

Или же как обычно
```python
@app.get("/hotels")
def get_hotels(
    location:str,
    date_from: date,
    date_to: str,
    has_spa: Optional[bool] = None,
    stars: Optional[int] = Query(None, ge=1, le=5),
) -> list[ShemeHotel]:
```

Валидация возвращаемых типов в GET запросах
```python
from fastapi import FastAPI, Query, Depends
  
app = FastAPI()  
  
class GetSomeSearchArgs:  
    def __init__(self, name: str, is_name: bool, new_id: int = Query(ge=1, le=5)):  
        self.new_id = new_id  
        self.name = name  
        self.is_name = is_name  
  
@app.get('/some')  
def getsome(search_args: GetSomeSearchArgs = Depends()):  
    return (f'Your name: {search_args.name},'  
            f'Your new_id: {search_args.new_id}')
```

###### Статус коды
Код в случае успеха
```python
from fastapi import FastAPI, status

app = FastAPI() 

@app.get('/happy', status_code=201)  
def happy():  
    return ":)"
```

Код для исключения
```python
from fastapi import FastAPI, HTTPException, status

app = FastAPI()

@app.get("/items/{item_id}") 
async def read_item(item_id: int):
    if item_id == 1:
        return {"item": "Item 1"} 
    else:
        raise HTTPException(status_code=status.HTTP_404_NOT_FOUND, detail="Item not found")
```

###### Установка заголовков или куки
```python
from fastapi import FastAPI, Response

app = FastAPI()

@app.get('/setheader')  
def header(response: Response):  
    response.headers['MyKey'] = 'example'
    
    # или например установка куки
    response.set_cookie('MyCookie', '12345')
    return "normal body"
```

###### Типы ответов
Типы ответов (импортируйте эти классы из `fastapi.responses`):
1. JSONResponse(по умолчанию)
2. HTMLResponse
3. PlainTextResponse
4. RedirectResponse
5. FileResponse
6. StreamingResponse

#### Внедрение зависимостей
>[!info] *Внедрение зависимостей* - это передача функции любой требующейся ей специфичной информации. Традиционный способ сделать это - передать вспомогательную функцию, которую вы затем вызываете для получения конкретных данных.

Простой пример
```python
from fastapi import FastAPI, Depends

app = FastAPI()

from fastapi import FastAPI, Depends

app = FastAPI()

# функция зависимости
def get_message():
    return "Hello from dependency!"

# эндпоинт
@app.get("/welcome")
def welcome(message: str = Depends(get_message)) -> dict:
    return {"message": message}
```

Пример зависимости с возвращаемым значением
```python
from fastapi import FastAPI, Depends, Query

app = FastAPI()

# Функция зависимости  
def user_dep(name: str = Query(...), password: str = Query(...)):  
    return {"name": name, "valid": True}  
  
# Эндпоинт  
@app.get("/user")  
def get_user(user: dict = Depends(user_dep)) -> dict:  
    return user
```

Если зависимость не возвращает значение, можно определить зависимость в декораторе эндпоинта
```python
from fastapi import FastAPI, Depends, Params

app = FastAPI()

# функция зависимости
def check_dep(name: str = Params, password: str = Params):
    if not name:
        raise

# эндпоинт
@app.get("/check_user", dependencies=[Depends(check_dep)])
def check_user()) -> bool:
    return True
```

Зависимости субмаршрута
```python
from fastapi import FastAPI, Depends, APIRouter

# это приведёт к вызрву dep_func() для всех функций эндпоинтов ниже объекта router
router = APIRouter(... , dependencies=[Depends(dep_func)])
```

Глобальное внедрение зависимостей(все эндпоинты)
```python
from fastapi import FastAPI, Depends

def depfunc1():
    pass

def depfunc2():
    pass

app = FastAPI(dependencies=[Depends(depfunc1), Depends(depfunc2)])

@app.get("/main")
def get_main():
    pass
```

##### POST запросы
```python
from fastapi import FastAPI
from pydantic import BaseModel  
  
app = FastAPI()  

# схема (модель данных)
class SBooking(BaseModel):  
    room_id: int  
    rating: str  
  
@app.post('/bookings')  
def add_booking(booking: SBooking) -> SBooking:  
    print("BOOKING WAS ADDED!")
    return booking
```

##### Routers
Файл с роутером
```python
from fastapi import APIRouter  
  
router = APIRouter(prefix='/test', tags=['Тест',])  
  
@router.get('')  
def get_test():  
    pass
```

Подключаем его в основной файл
```python
from test_router import router as t_router

app = FastAPI()  
app.include_router(t_router)
```

#### Typing
- Дата
```python
from datetime import date
```

- Ограничения
Пример GET-параметр `new_id`, который должен быть больше 1, но меньше 5
```python
from fastapi import Query

@app.get('/some')  
def getsome(new_id: int = Query(ge=1, le=5)):  
    return f'Your new ID: {new_id}'
```

#### Работа с БД
```bash
pip install sqlalchemy
```

###### Engine
>Объект **Engine** — это центральный компонент SQLAlchemy, который управляет подключением к базе данных. Он действует как посредник между вашим кодом и базой данных, обеспечивая:
>- **Пул подключений**: Хранит открытые соединения, чтобы избежать затрат на их повторное создание, повышая производительность.
>- **Диалект**: Определяет правила взаимодействия с конкретной базой данных (например, SQLite, PostgreSQL). Диалект переводит команды SQLAlchemy в SQL, понятный базе данных.
>- **DBAPI**: Интерфейс Python для взаимодействия с базой данных. Для SQLite это встроенный модуль `sqlite3`, для PostgreSQL — `psycopg2`, и так далее.

###### Сеанс
>Сеанс (`Session`) в SQLAlchemy — это объект, который управляет операциями с базой данных, такими как добавление, обновление или удаление данных. Представьте сеанс как "тетрадь для черновиков": он записывает все изменения, которые вы хотите внести в базу (например, создание категории или изменение цены товара), проверяет их корректность и решает, применить их или отменить. Это реализация шаблона проектирования **Unit of Work**, который гарантирует согласованность данных. Сеанс:
>- Отслеживает изменения в объектах (например, добавление нового товара).
>- Проверяет, что изменения допустимы (например, соблюдены уникальные ключи).
>- Координирует выполнение операций через объект `Engine`, настроенный в `app/database.py`.
>- Поддерживает атомарность транзакций: Это означает, что все операции, выполняемые в рамках одной транзакции, либо полностью применяются (commit), либо полностью отменяются (rollback) в случае ошибки или сбоя. Это гарантирует согласованность данных в базе, исключая ситуации, когда изменения применяются частично.

SQLAlchemy использует **фабрику сеансов** (`sessionmaker`), которая создаёт экземпляры сеансов, привязанные к нашему `Engine`. Это позволяет эффективно управлять подключениями, используя пул соединений из `Engine`.

Создаём `db.py`
```python
from sqlalchemy.ext.asyncio import AsyncSession, create_async_engine  
from sqlalchemy.orm import DeclarativeBase, sessionmaker  
from config import settings

# dialect+driver://username:password@host:port/database
engine = create_async_engine(settings.DATABASE_URL)  # например "sqlite:///test.db"
# Для абсолютного пути используйте четыре слэша

# создание сессии (фабрика сеансов)
async_session_maker = sessionmaker(engine, class_=AsyncSession, expire_on_commit=False)  
  
# класс для миграций
class Base(DeclarativeBase):  
    pass
```

```
dialect+driver://username:password@host:port/database
```
- **dialect**: Тип базы данных (например, `sqlite`, `postgresql`, `mysql`).
- **driver**: DBAPI-драйвер (для SQLite дополнительный драйвер не нужен, так как SQLAlchemy использует встроенный модуль `sqlite3`. Но для других баз данных, таких как PostgreSQL, потребуется установить драйвер, например, `psycopg2`)
- **username:password@host:port/database**: Параметры подключения (для SQLite не требуются).

Создаём `config.py` и читаём .env с помощью Pydantic
```bash
pip install pydantic-settings
```

```python
from pydantic_settings import BaseSettings  
  
class Settings(BaseSettings):  
    DB_HOST: str  
    DB_PORT: int  
    DB_USER: str  
    DB_PASS: str  
    DB_NAME: str  
  
    class Config:  
        env_file = ".env"  
  
    @property  
    def DATABASE_URL(self) -> str:  
        return f"postgresql+asyncpg://{self.DB_USER}:{self.DB_PASS}@{self.DB_HOST}:{self.DB_PORT}/{self.DB_NAME}"  

settings = Settings()
```

Для каждой модели создаём свой Data Access Object (т.е. например будет директория `horoscopes`, в ней будет файл `models.py`, файл `dao.py` , файл `router.py` и файл `schemas.py`)
```python
# dao.py
from database import async_session_maker  
from sqlalchemy import select  
from horoscopes.models import Horoscope  
  
# DAO = Data Access Object  
class HoroscopeDAO:  
  
    @classmethod  
    async def find_all(cls):  
        async with async_session_maker() as session:  
            query = select(Horoscope)  
            horoscopes = await session.execute(query)  
            return horoscopes.scalars().all()
```

```python
# router.py
from fastapi import APIRouter  
from horoscopes.dao import HoroscopeDAO  
  
router = APIRouter(prefix='/horoscopes', tags=['Гороскопы', ])  
  
@router.get('')  
async def get_horoscopes():  
    return await HoroscopeDAO.find_all()
```

###### Модели
Создаём файл с моделями
```python
from sqlalchemy import JSON, Column, Integer, String  
from db import Base  
  
class MyUser(Base):  
    __tablename__ = "users"  
  
    id = Column(Integer, primary_key=True)  
    name = Column(String, nullable=False)
    room_id = Column(ForeignKey("rooms.id"))  # должна существовать таблица rooms
```

Или самый новый вариант (использование `mapped_column`)
```python
from sqlalchemy import String
from sqlalchemy.orm import DeclarativeBase, Mapped, mapped_column
from typing import Optional


class Base(DeclarativeBase):
    pass


class Product(Base):
    __tablename__ = "products"
    
    id: Mapped[int] = mapped_column(primary_key=True)
    name: Mapped[str] = mapped_column(String(50), nullable=False)
    description: Mapped[Optional[str]] = mapped_column(String(200))
    price: Mapped[float] = mapped_column(nullable=False)
```
- `Mapped` указывает тип данных столбца через аннотации Python.
- `mapped_column` автоматически определяет тип столбца на основе аннотации (например, `Mapped[int]`преобразуется в `Integer`).
- `Optional[str]` указывает, что поле `description` может быть `NULL` в базе данных.

###### Параметры столбцов таблицы
Параметры столбцов в SQLAlchemy позволяют настраивать поведение и ограничения столбцов таблицы. Они задаются как аргументы в `Column` или `mapped_column` (в SQLAlchemy 2.0+) и определяют, как база данных будет обрабатывать данные в столбце.

1. **`primary_key=True`** 
Указывает, что столбец является первичным ключом таблицы. Первичный ключ уникально идентифицирует каждую запись и не может быть `NULL`. Обычно используется для столбца `id`

2. **`unique=True`**
Требует, чтобы значения в столбце были уникальными для каждой записи. Это полезно для полей, таких как `email`, `username` или `sku` (артикул товара).

3. **`nullable=False`**
Указывает, что столбец не может содержать значение `NULL`, делая поле обязательным. По-умолчаню стоит `True`

4. **`default=<значение>`**
Задает значение по умолчанию для столбца, если оно не указано при создании записи. Значение может быть константой или функцией. Пример:
`is_active = Column(Boolean, default=True)`
Пример с функцией: `created_at = Column(DateTime, default=datetime.now)` или 
`created_at = mapped_column(DateTime, nullable=False, default=func.now)`

5. **`index=True`**
Создает индекс для столбца, что ускоряет операции поиска и сортировки по этому столбцу, но может замедлить операции записи и увеличить объем базы данных. Примечание: *Используйте индексы для столбцов, по которым часто выполняются запросы (например, фильтрация или сортировка).*

6. **`autoincrement=True`**
Указывает, что значения столбца (обычно первичного ключа) должны автоматически увеличиваться при добавлении новой записи. Применяется к целочисленным столбцам. Примечание: *В большинстве баз данных (например, SQLite, PostgreSQL) автоинкремент включается автоматически для первичных ключей типа `Integer`, но явное указание может улучшить читаемость кода.*

7. **`check=<выражение>`**
Добавляет ограничение на значения столбца, используя выражение SQL. Это позволяет задавать дополнительные проверки на уровне базы данных. Пример: 
`price = Column(Float, check="price >= 0")`
Примечание: *Поддержка параметра `check` зависит от базы данных (например, SQLite поддерживает ограниченно, а PostgreSQL — полностью).*

Пример
```python
from sqlalchemy import Column, Integer, String, Float, Boolean, DateTime
from sqlalchemy.orm import DeclarativeBase
from datetime import datetime


class Base(DeclarativeBase):
    pass


class Product(Base):
    __tablename__ = "products"
    
    id = Column(Integer, primary_key=True, autoincrement=True, index=True)
    sku = Column(String(20), unique=True, nullable=False)
    name = Column(String(100), nullable=False, index=True)
    description = Column(String(500), default="")
    price = Column(Float, nullable=False, check="price >= 0")
    is_available = Column(Boolean, default=True)
    created_at = Column(DateTime, default=datetime.now)
```

#### Типы данных в SQLAlchemy

Типы данных в SQLAlchemy определяют, какие значения могут храниться в столбце и как они преобразуются между Python и базой данных. SQLAlchemy предоставляет широкий набор типов данных, которые соответствуют типам данных в реляционных базах данных. Выбор правильного типа данных критически важен для обеспечения производительности, целостности данных и совместимости с базой данных.

###### Основные типы данных SQLAlchemy

|**Тип данных SQL**|**Тип данных SQLAlchemy**|**Описание**|**Пример использования**|
|---|---|---|---|
|`INTEGER`|**`Integer`**|Целое число (обычно 32-битное)|`id = Column(Integer, primary_key=True)`|
|`BIGINT`|**`BigInteger`**|Большое целое число (64-битное)|`user_id = Column(BigInteger)`|
|`SMALLINT`|**`SmallInteger`**|Малое целое число (16-битное)|`order_status = Column(SmallInteger)`|
|`VARCHAR(length)`|**`String(length)`**|Строка фиксированной длины|`name = Column(String(100))`|
|`TEXT`|**`Text`**|Строка переменной длины (без ограничения длины)|`description = Column(Text)`|
|`BOOLEAN`|**`Boolean`**|Булево значение (`True`/`False`)|`is_active = Column(Boolean, default=True)`|
|`DATE`|**`Date`**|Дата (без времени)|`birthdate = Column(Date)`|
|`DATETIME`|**`DateTime`**|Дата и время|`created_at = Column(DateTime)`|
|`TIME`|**`Time`**|Время (без даты)|`login_time = Column(Time)`|
|`FLOAT`|**`Float`**|Число с плавающей точкой|`price = Column(Float)`|
|`DECIMAL(p,s)`|**`Numeric(precision, scale)`**|Десятичное число с заданной точностью|`amount = Column(Numeric(10, 2))`|
|`JSON`|**`JSON`**|Данные в формате JSON (поддерживается в PostgreSQL, MySQL)|`data = Column(JSON)`|
|`BLOB`|**`LargeBinary`**|Двоичные данные (например, изображения)|`image = Column(LargeBinary)`|

###### Дополнительные типы данных

- **`Enum`**: Позволяет задавать перечислимые значения для столбца. Полезно для ограниченного набора строковых значений, таких как статус заказа.
    - Пример: `status = Column(Enum('pending', 'completed', 'cancelled', name='order_status'))`
- **`Interval`**: Для хранения временных интервалов (поддерживается в PostgreSQL).
    - Пример: `duration = Column(Interval)`
- **`UUID`**: Для хранения уникальных идентификаторов UUID.
    - Пример: `uuid = Column(UUID(as_uuid=True))`

###### Важные моменты при выборе типов данных
- Для строк используйте `String(length)` с указанием максимальной длины (например, `String(100)`), чтобы оптимизировать использование памяти. Используйте `Text` только для строк переменной длины без ограничений.
-  Для финансовых данных (цены, суммы) используйте `Numeric(precision, scale)` вместо `Float`, чтобы избежать ошибок округления. Например, `Numeric(10, 2)` для хранения цен с двумя знаками после запятой.
- Избегайте избыточных индексов, чтобы не перегружать базу данных.

#### SQLAlchemy + Alembic
SQLAlchemy - это популярная ORM.
Alembic для миграций, работает в связке с SQLAlchemy.

>[!info] Существует библиотека от создателя FastAPI, которая представляет собой связку из SQLAlchemy и Pydantic - [SQLModel](https://sqlmodel.tiangolo.com/)

```bash
pip install sqlalchemy alembic asyncpg
```
```bash
pip install pydantic-settings
```

##### Alembic
Миграции
```bash
alembic init migrations
```

В появившейся папке `migrations` в файле `env.py` добавляем импорты
```python
from db import Base, DATABASE_URL        
from models import MyUser
```

И также изменяем
```python
target_metadata = Base.metadata
...
config.set_main_option('sqlalchemy.url', f'{DATABASE_URL}?async_fallback=True')
```

Проводим миграцию
```bash
alembic revision --autogenerate -m "Initial migration"
```
```bash
alembic upgrade head
```

Откатить миграцию
```bash
alembic downgrade -1
```

##### SQLAlchemy
###### Выборка
Выборка всех записей
```python
async with async_session_maker() as session:  
    query = select(Horoscope)  
    horoscopes = await session.execute(query)  
    return horoscopes.scalars().all()
```

Выборка одной записи
```python
async with async_session_maker() as session:  
    query = select(Horoscope).filter_by(id=1)  
    horoscopes = await session.execute(query)  
    return horoscopes.scalar_one_or_none()
```

Фильтры
```python
async with async_session_maker() as session:  
    query = select(Horoscope).filter_by(user_id=1, price=24500)  
    horoscopes = await session.execute(query)  
    return horoscopes.scalars().all()
```

##### Валидация
Чтобы `Pydantic` мог корректно читать данные SQLAlchemy и валидировать их мы создаём схему с режимом ORM
```python
# schemas.py
from datetime import date  
from pydantic import BaseModel  
  
class SHoroscope(BaseModel):  
    id = int  
    sign = str  
    text = str  
    date = date  
  
    # Чтобы Pydantic смог получить список данных Алхимии  
    class Config:  
        orm_mode = True
```

И теперь проставляем в роутере тип возвращаемого значения
```python
@router.get('')  
async def get_horoscopes() -> list[SHoroscope]:  
    return await HoroscopeDAO.find_all()
```

Теперь благодаря схеме в автодокументации будет пример возвращаемого значения

#### Аутентификация и авторизация

###### AuthX
[GitHub](https://github.com/yezz123/authx)
[Документация](https://authx.yezz.me/get-started/installation/)

```bash
pip install authx
```

```python
from fastapi import FastAPI, Depends, HTTPException
from authx import AuthX, AuthXConfig, RequestToken
from pydantic import BaseModel

app = FastAPI()

config = AuthXConfig()
config.JWT_SECRET_KEY = 'Secret_Key'  
config.JWT_ACCESS_COOKIE_NAME = 'my_access_token'  
config.JWT_TOKEN_LOCATION = ['cookies']

auth = AuthX(config=config)

class UserLoginSchema(BaseModel):  
    username: str  
    password: str

@app.exception_handler(MissingTokenError)  
async def auth_exception_handler(request: Request, exc: Exception):  
    raise HTTPException(status_code=status.HTTP_403_FORBIDDEN, detail="Access denied")
    
@app.post('/login')  
def login(credentials: UserLoginSchema, response: Response):  
    if credentials.username == 'user' and credentials.password == 'test':  
        token = security.create_access_token(uid='12345')  
        response.set_cookie(config.JWT_ACCESS_COOKIE_NAME, token)  
        return {'access_token': token}  
    raise HTTPException(status_code=401, detail="Incorrect username or password")

@app.get("/protected", dependencies=[Depends(security.access_token_required)])
def get_protected(token: RequestToken = Depends()):
     pass
```

###### FastAPI Users
[Документация](https://fastapi-users.github.io/fastapi-users/latest/)

```bash
pip install 'fastapi-users[sqlalchemy]'
```

###### Keycloak
Keycloak – это бесплатное решение с открытым исходным кодом для управления идентификацией и доступом (Identity and Access Management, IAM). Основная задача Keycloak – обеспечить механизм единого входа (Single Sign-On, SSO), позволяющий пользователям аутентифицироваться один раз и получать доступ к различным приложениям без необходимости повторного ввода учетных данных.

[Статья](https://habr.com/ru/companies/amvera/articles/907990/)

#### Celery
```bash
pip install celery flower redis
```

Создаём файл с конфигурацией Celery `celery_config.py`
```python
from celery import Celery  

celery = Celery(  
    'tasks',  
    broker='redis://localhost:6379/1',  
    backend='redis://localhost:6379/1',  
    include=['tasks']  
)
```

Создаём файл с тасками `tasks`
```python
from celery_config import celery

@celery.task
def request():
    pass
```

Эндпоинты
```python
from celery.result import AsyncResult
from celery_config import celery

@app.get('/search')  
def search(q: str):
    task = vk_search.delay(q)
    return {"task_id": task.id}

@app.get('/result/{task_id}')  
def result(task_id: str):  
    task_result = AsyncResult(task_id, app=celery)  
    if task_result.ready():  
        return {"status": "completed", "result": task_result.result}  
    else:  
        return {"status": "pending"}
```

Запуск Celery
```bash
celery -A celery_config:celery worker --loglevel=INFO
```

Запуск Flower
```bash
celery -A celery_config:celery flower
```

#### Шаблоны
>[!info] Используется шаблонизатор `Jinja2`. В FastAPI шаблоны рендерятся с помощью `TemplateResponse`, куда передается объект `Request` и контекст — словарь с данными.

```bash
pip install jinja2
```

Чтобы в дальнейшем использовать формы, сразу установите `python-multipart`.
```bash
pip install python-multipart
```

Создаём директорию `templates` и в ней `index.html`
```python
from fastapi import FastAPI, Request
from fastapi.templating import Jinja2Templates

app = FastAPI()
templates = Jinja2Templates(directory="templates")

@app.get("/")
def read_root(request: Request):
    return templates.TemplateResponse("index.html", {"request": request, "user_name": "Alice"})
```

##### Статика для шаблонизатора
Создаём директорию `static`
```python
from fastapi.staticfiles import StaticFiles

app = FastAPI()

app.mount('/static', StaticFiles(directory='app /static'), 'static')
```

#### Загрузка файлов
```python
import os  
  
from fastapi import APIRouter, UploadFile, File  
from fastapi.responses import JSONResponse  
  
  
router = APIRouter(prefix='/upload', tags=['Upload', ])  
  
UPLOAD_DIRECTORY = "./uploads"  
os.makedirs(UPLOAD_DIRECTORY, exist_ok=True)  
  
  
@router.post("")  
async def upload_file(file: UploadFile = File(...)):  
    file_location = os.path.join(UPLOAD_DIRECTORY, file.filename)  
  
    with open(file_location, "wb") as f:  
        contents = await file.read()  
        f.write(contents)  
  
    return JSONResponse(content={"filename": file.filename, "file_location": file_location})
```

#### CORS
```python
from fastapi import FastAPI
from fastapi.middleware.cors import CORSMiddleware

app = FastAPI()

origins = ["*"]

app.add_middleware(
    CORSMiddleware,
    allow_origins=origins,
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)
```