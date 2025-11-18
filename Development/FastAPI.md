![[fastapi.png]]

[Документация](https://fastapi.tiangolo.com/ru/)
[Metanit](https://metanit.com/python/fastapi/)

![[fastapi_request.webp]]

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

 2. Можно передать значения полей с помощью распаковки словарей `**`
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
Класс `Path` используется для определения параметров пути в маршрутах FastAPI. Он не только указывает, что параметр является частью пути, но и позволяет задавать правила валидации и метаданные, которые отображаются в документации Swagger UI и ReDoc.

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

#### SQLAlchemy. Работа с БД
SQLAlchemy - это популярная ORM.

![[sqla_arch_small.png]]

```bash
pip install sqlalchemy
```

Драйвер для асинхронного подключения
```bash
pip install asyncpg
```

SQLAlchemy использует библиотеку `greenlet` для управления асинхронными операциями.
```bash
pip install greenlet
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
# --------------- Асинхронное подключение к PostgreSQL -------------------------
from sqlalchemy.ext.asyncio import create_async_engine, async_sessionmaker, AsyncSession
from sqlalchemy.orm import DeclarativeBase

# Строка подключения для SQLite
DATABASE_URL = "postgresql+asyncpg://ecommerce_user:xxxxxxxx@localhost:5432/ecommerce_db"

# Создаём Engine
async_engine = create_async_engine(DATABASE_URL, echo=True)

# Настраиваем фабрику сеансов
async_session_maker = async_sessionmaker(async_engine, expire_on_commit=False, class_=AsyncSession)


class Base(DeclarativeBase):
    pass

```

```
dialect+driver://username:password@host:port/database
```
- **dialect**: Тип базы данных (например, `sqlite`, `postgresql`, `mysql`).
- **driver**: DBAPI-драйвер (для SQLite дополнительный драйвер не нужен, так как SQLAlchemy использует встроенный модуль `sqlite3`. Но для других баз данных, таких как PostgreSQL, потребуется установить драйвер, например, `psycopg2`)
- **username:password@host:port/database**: Параметры подключения (для SQLite не требуются).

Создаём зависимость, которая внедряет асинхронную сессию.
```python
from typing import AsyncGenerator
from fastapi import HTTPException, status
from sqlalchemy.ext.asyncio import AsyncSession
from app.database import async_session_maker


async def get_async_db() -> AsyncGenerator[AsyncSession, None]:
    """
    Предоставляет асинхронную сессию SQLAlchemy для работы с базой данных PostgreSQL.
    """
    async with async_session_maker() as session:
        yield session
```

Далее для работы с БД занимаемся миграциями [[FastAPI#Alembic]]

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
Создаём файл с моделями SQLAlchemy
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

#### Связи в ORM
**ForeignKey**
Используются для связи таблиц. В один к одному и один ко многим внешний ключ находится в дочерней таблице (например, `profiles.user_id` или `orders.user_id`). В многие ко многим внешние ключи находятся в промежуточной таблице (например, `order_items.order_id` и `order_items.product_id`).

*Параметры*
- `column`: Имя столбца в родительской таблице, на который ссылается внешний ключ (например, `"users.id"`).
- `ondelete`: Определяет поведение при удалении записи в родительской таблице (например, `"CASCADE"` для удаления дочерней записи).
- `onupdate`: Задаёт поведение при обновлении первичного ключа в родительской таблице.
- `constraint_name`: Указывает имя ограничения внешнего ключа.

```python
playlist = Column(ForeignKey("playlists.id", ondelete="CASCADE"))
```

**relationship**
Функция `relationship` используется для установления связи между моделями на уровне ORM, что позволяет автоматически загружать связанные данные. Для отношения один к одному критически важно установить параметр `uselist=False`, чтобы связь возвращала одиночный объект, а не коллекцию.

```python
relationship("Profile", uselist=False, back_populates="user")
```

*Параметры*
- `argument`: Имя или класс связанной модели (например, `"Profile"` или `Profile`).  
- `back_populates`: Параметр `back_populates` обеспечивает двустороннюю связь между моделями, позволяя обращаться к связанным объектам с обеих сторон (например, `user.profile` и `profile.user`). Он требует указания имени атрибута в связанной модели и должен быть синхронизирован в обеих моделях.  
- `backref`: Параметр, который создаёт двунаправленную связь с минимальным кодом. Если определить её в одном классе, она автоматически настроит обратную связь в связанном классе. Например: `profile = relationship("Profile", backref="user")`  
- `uselist`: Булево значение, которое для один к одному должно быть `False`. Неправильная настройка `uselist` может привести к созданию отношения один ко многим вместо один к одному.  
- `cascade`: Параметр `cascade` управляет поведением связанных записей при выполнении операций, таких как сохранение, обновление или удаление. Например, значение `"all, delete-orphan"` указывает, что при удалении родительской записи все связанные дочерние записи также будут удалены, включая те, которые больше не связаны с родителем. Неправильная настройка каскадов может привести к нежелательному удалению данных, поэтому следует использовать их с осторожностью.  
- `lazy`: Параметр `lazy` определяет, когда и как загружаются связанные данные. Возможные значения включают:    
    - `"select"`: Данные загружаются при первом обращении (ленивая загрузка).
    - `"joined"`: Данные загружаются сразу в одном SQL-запросе.
    - `"subquery"`: Данные загружаются через отдельный подзапрос.
    - `"dynamic"`: Позволяет динамически запрашивать данные.
    Использование ленивой загрузки (`lazy="select"`) может привести к проблеме N+1, когда для загрузки связанных данных выполняется множество дополнительных запросов. Для оптимизации производительности рекомендуется использовать `"joined"` или `"subquery"` в зависимости от контекста.

###### Один к одному
```python
Column(Integer, ForeignKey("users.id"), unique=True, nullable=False)
```

Пример. Каждый пользователь имеет ровно один профиль, а каждый профиль принадлежит ровно одному пользователю.
```python
from sqlalchemy import Column, Integer, String, ForeignKey
from sqlalchemy.orm import DeclarativeBase, relationship


# Базовый класс для всех моделей
class Base(DeclarativeBase):
    pass


# Модель пользователя
class User(Base):
    # Имя таблицы в базе данных
    __tablename__ = "users"
    
    # Первичный ключ, целое число, автогенерируемое
    id = Column(Integer, primary_key=True)
    
    # Имя пользователя, строка до 50 символов, уникальное и обязательное
    username = Column(String(50), unique=True, nullable=False)
    
    # Связь один-к-одному с профилем, возвращает одиночный объект Profile
    profile = relationship(
        "Profile",                    # Имя связанной модели
        back_populates="user",       # Имя атрибута обратной связи в Profile
        uselist=False,               # Указывает, что это одиночный объект
        cascade="all, delete-orphan" # Удаляет профиль при удалении пользователя
    )


# Модель профиля
class Profile(Base):
    # Имя таблицы в базе данных
    __tablename__ = "profiles"
    
    # Первичный ключ профиля
    id = Column(Integer, primary_key=True)
    
    # Биография, строка до 500 символов, может быть NULL
    bio = Column(String(500))
    
    # Внешний ключ на users.id, уникальный и обязательный
    user_id = Column(Integer, ForeignKey("users.id"), unique=True, nullable=False)
    
    # Обратная связь с пользователем, возвращает одиночный объект User
    user = relationship(
        "User",                     # Имя связанной модели
        back_populates="profile"    # Имя атрибута обратной связи в User
    )
```

Тот же пример но с использованием современного подхода с `mapped_column()` и аннотациями типов `Mapped[]`. Это делает код более строгим и совместимым с инструментами проверки типов.
```python
from sqlalchemy import ForeignKey, String
from sqlalchemy.orm import DeclarativeBase, Mapped, mapped_column, relationship
from typing import Optional


# Базовый класс для всех моделей, связывающий их с метаданными базы данных
class Base(DeclarativeBase):
    pass


# Модель пользователя
class User(Base):
    # Имя таблицы в базе данных
    __tablename__ = "users"
    
    # Первичный ключ, целое число, автогенерируемое
    id: Mapped[int] = mapped_column(primary_key=True)
    
    # Имя пользователя, строка до 50 символов, уникальное и обязательное
    username: Mapped[str] = mapped_column(String(50), unique=True, nullable=False)
    
    # Связь один-к-одному с профилем, возвращает одиночный объект Profile
    profile: Mapped["Profile"] = relationship(
        "Profile",                   # Имя связанной модели
        back_populates="user",       # Имя атрибута обратной связи в Profile
        uselist=False,               # Указывает, что это одиночный объект, а не список
        cascade="all, delete-orphan" # Удаляет профиль при удалении пользователя
    )


# Модель профиля
class Profile(Base):
    # Имя таблицы в базе данных
    __tablename__ = "profiles"
    
    # Первичный ключ профиля, целое число
    id: Mapped[int] = mapped_column(primary_key=True)
    
    # Биография, строка до 500 символов, может быть NULL
    bio: Mapped[Optional[str]] = mapped_column(String(500))
    
    # Внешний ключ на users.id, уникальный и обязательный
    user_id: Mapped[int] = mapped_column(ForeignKey("users.id"), unique=True, nullable=False)
    
    # Обратная связь с пользователем, возвращает одиночный объект User
    user: Mapped["User"] = relationship(
        "User",                     # Имя связанной модели
        back_populates="profile"    # Имя атрибута обратной связи в User
    )
```

Ещё пример
```python
class User(Base):
    __tablename__ = "users"
    id: Mapped[int] = mapped_column(primary_key=True)
    profile: Mapped["Profile"] = relationship("Profile", uselist=False, back_populates="user")


class Profile(Base):
    __tablename__ = "profiles"
    id: Mapped[int] = mapped_column(primary_key=True)
    user_id: Mapped[int] = mapped_column(ForeignKey("users.id"), unique=True, nullable=False)
    user: Mapped["User"] = relationship("User", back_populates="profile")
```

###### Один ко многим
В ORM SQLAlchemy параметр `uselist` либо не указывается (по умолчанию `True`), либо явно устанавливается в `True`, что позволяет атрибуту связи возвращать коллекцию объектов (например, список). Например, в модели `User` атрибут `posts` может возвращать список объектов `Post`, связанных с пользователем.

**Основной синтаксис**:
- Внешний ключ: `Column(Integer, ForeignKey("users.id"), nullable=False)`.
- Связь один ко многим: `relationship("Order", back_populates="user", cascade="all, delete-orphan")`.
- Обратная связь: `relationship("User", back_populates="orders")`.

Пример
```python
from sqlalchemy import Column, Integer, String, DateTime, Numeric, ForeignKey
from sqlalchemy.orm import DeclarativeBase, relationship
from datetime import datetime


# Базовый класс для всех моделей
class Base(DeclarativeBase):
    pass


# Модель пользователя
class User(Base):
    # Имя таблицы в базе данных
    __tablename__ = "users"
    
    # Первичный ключ, целое число, автогенерируемое
    id = Column(Integer, primary_key=True)
    
    # Имя пользователя, строка до 50 символов, уникальное и обязательное
    username = Column(String(50), unique=True, nullable=False)
    
    # Связь один-ко-многим, возвращает список объектов Order
    orders = relationship(
        "Order",                     # Имя связанной модели
        back_populates="user",       # Имя атрибута обратной связи в Order
        cascade="all, delete-orphan" # Удаляет заказы при удалении пользователя
    )


# Модель заказа
class Order(Base):
    # Имя таблицы в базе данных
    __tablename__ = "orders"
    
    # Первичный ключ заказа
    id = Column(Integer, primary_key=True)
    
    # Дата создания заказа, по умолчанию текущая дата и время
    order_date = Column(DateTime, default=datetime.now)
    
    # Сумма заказа, десятичное число с 2 знаками, обязательное
    total = Column(Numeric(10, 2), nullable=False)
    
    # Внешний ключ на users.id, обязательный
    user_id = Column(Integer, ForeignKey("users.id"), nullable=False)
    
    # Обратная связь с пользователем, возвращает одиночный объект User
    user = relationship(
        "User",                    # Имя связанной модели
        back_populates="orders"    # Имя атрибута обратной связи в User
    )
```

Пример с использованием `mapped_column`
```python
from sqlalchemy import ForeignKey, String, DateTime, Numeric
from sqlalchemy.orm import DeclarativeBase, Mapped, mapped_column, relationship
from typing import List
from datetime import datetime


# Базовый класс для всех моделей
class Base(DeclarativeBase):
    pass


# Модель пользователя
class User(Base):
    # Имя таблицы в базе данных
    __tablename__ = "users"
    
    # Первичный ключ, целое число, автогенерируемое
    id: Mapped[int] = mapped_column(primary_key=True)
    
    # Имя пользователя, строка до 50 символов, уникальное и обязательное
    username: Mapped[str] = mapped_column(String(50), unique=True, nullable=False)
    
    # Связь один-ко-многим, возвращает список объектов Order
    orders: Mapped[List["Order"]] = relationship(
        "Order",                     # Имя связанной модели
        back_populates="user",       # Имя атрибута обратной связи в Order
        cascade="all, delete-orphan" # Удаляет заказы при удалении пользователя
    )


# Модель заказа
class Order(Base):
    # Имя таблицы в базе данных
    __tablename__ = "orders"
    
    # Первичный ключ заказа
    id: Mapped[int] = mapped_column(primary_key=True)
    
    # Дата создания заказа, по умолчанию текущая дата и время
    order_date: Mapped[datetime] = mapped_column(DateTime, default=datetime.now)
    
    # Сумма заказа, десятичное число с 2 знаками, обязательное
    total: Mapped[float] = mapped_column(Numeric(10, 2), nullable=False)
    
    # Внешний ключ на users.id, обязательный
    user_id: Mapped[int] = mapped_column(ForeignKey("users.id"), nullable=False)
    
    # Обратная связь с пользователем, возвращает одиночный объект User
    user: Mapped["User"] = relationship(
        "User",                    # Имя связанной модели
        back_populates="orders"    # Имя атрибута обратной связи в User
    )
```

###### Многие ко многим
Используется функция `relationship` с параметром `secondary`, который указывает на промежуточную таблицу, позволяя автоматически загружать связанные данные, такие как список товаров в заказе или список заказов, содержащих определённый товар.

Промежуточная таблица обычно определяется как объект `Table`, а не как полноценная модель, поскольку её основная задача — хранить связи между сущностями, а не предоставлять сложную бизнес-логику. Такой подход упрощает код и снижает издержки, связанные с созданием полноценного ORM-класса.

Пример
```python
from sqlalchemy import Column, ForeignKey, String, Integer, Numeric, Table, DateTime
from sqlalchemy.orm import DeclarativeBase, relationship
from datetime import datetime


# Базовый класс для всех моделей
class Base(DeclarativeBase):
    pass


# Промежуточная таблица для связи заказов и товаров
order_items = Table(
    "order_items",
    Base.metadata,
    Column("order_id", Integer, ForeignKey("orders.id"), primary_key=True, index=True),
    Column("product_id", Integer, ForeignKey("products.id"), primary_key=True, index=True),
    Column("quantity", Integer, default=1, nullable=False),
    Column("price", Numeric(10, 2), nullable=False)
)


# Модель товара
class Product(Base):
    __tablename__ = "products"

    id = Column(Integer, primary_key=True)
    name = Column(String(100), nullable=False)
    orders = relationship(
        "Order",
        secondary=order_items,
        back_populates="products"
    )


# Модель заказа
class Order(Base):
    __tablename__ = "orders"

    id = Column(Integer, primary_key=True)
    order_date = Column(DateTime, default=datetime.now)
    total = Column(Numeric(10, 2), nullable=False)
    products = relationship(
        "Product",
        secondary=order_items,
        back_populates="orders"
    )
```
Промежуточная таблица `order_items` используется только для хранения связей между таблицами `orders` и `products` (через внешние ключи `order_id` и `product_id`) и дополнительных данных, таких как `quantity` и `price`. Она не требует сложной бизнес-логики или ORM-методов, как полноценные модели `Product` и `Order`. Поэтому в SQLAlchemy её часто определяют как объект `Table`, а не как полноценную модель, чтобы упростить код и избежать избыточной функциональности.

Таблица `order_items` имеет один составной первичный ключ, состоящий из двух столбцов: `Column(Integer, ForeignKey(...), primary_key=True, index=True)`. Вместе они обеспечивают уникальность каждой записи. Параметр `index=True` ускоряет поиск по этим столбцам.

Здесь `PRIMARY KEY (order_id, product_id)` явно указывает, что первичный ключ составной и включает оба столбца. Это стандартный подход для промежуточных таблиц в отношениях многие ко многим, чтобы гарантировать уникальность каждой связи.

Пример с использованием `mapped_column` и `Mapped`
```python
from sqlalchemy import Column, ForeignKey, String, Integer, Numeric, Table, DateTime
from sqlalchemy.orm import DeclarativeBase, Mapped, mapped_column, relationship
from typing import List
from datetime import datetime


# Базовый класс для всех моделей
class Base(DeclarativeBase):
    pass


# Промежуточная таблица для связи заказов и товаров
order_items = Table(
    "order_items",
    Base.metadata,
    Column("order_id", Integer, ForeignKey("orders.id"), primary_key=True, index=True),
    Column("product_id", Integer, ForeignKey("products.id"), primary_key=True, index=True),
    Column("quantity", Integer, nullable=False, default=1),
    Column("price", Numeric(10, 2), nullable=False),
)


# Модель товара
class Product(Base):
    __tablename__ = "products"

    id: Mapped[int] = mapped_column(primary_key=True)
    name: Mapped[str] = mapped_column(String(100), nullable=False)
    orders: Mapped[List["Order"]] = relationship(
        "Order",
        secondary=order_items,
        back_populates="products",
        cascade="all, delete-orphan"
    )


# Модель заказа
class Order(Base):
    __tablename__ = "orders"

    id: Mapped[int] = mapped_column(primary_key=True)
    order_date: Mapped[datetime] = mapped_column(DateTime, default=datetime.now)
    total: Mapped[float] = mapped_column(Numeric(10, 2), nullable=False)
    products: Mapped[List["Product"]] = relationship(
        "Product",
        secondary=order_items,
        back_populates="orders"
    )
```

##### SQLAlchemy. Запросы
>[!info] В SQLAlchemy запросы сначала **составляются** как объекты (типа `Select`), а затем **выполняются** с помощью методов, таких как `session.execute()`. Это разделение позволяет гибко формировать запросы, модифицировать их в зависимости от условий и выполнять только тогда, когда это необходимо.

1. **Составление запроса**:
    - Вы создаёте объект запроса с помощью функции `select()`.
    - Добавляете модификаторы, такие как `.where()`, `.order_by()`, `.join()`, `.limit()`, чтобы уточнить, какие данные нужны.
    - На этом этапе запрос **не выполняется** — вы просто строите инструкцию для базы данных.
    - Объект запроса можно передавать между функциями, модифицировать или сохранять для повторного использования.
2. **Выполнение запроса**:
    - Когда запрос готов, вы передаёте его в метод `session.execute()`.
    - Только на этом этапе SQLAlchemy отправляет SQL-запрос в базу данных и возвращает результаты.
    - Вы можете извлечь данные с помощью методов `.scalars()`, `.all()`, `.first()` и других.

###### Составление запроса
>[!info] **Метод `select()`** — основа для создания запросов в SQLAlchemy. Он определяет, какие сущности (таблицы, модели ORM) или столбцы вы хотите извлечь из базы данных. Это аналог `SELECT` в SQL. Также существуют и `.add()`, `.update()`, `.delete()`.

```python
from sqlalchemy import select
from app.models.categories import Category

# Выборка всех столбцов модели Category
stmt = select(Category)

# Выборка конкретных столбцов
stmt = select(Category.id, Category.name)
```

**Where** поддерживает операторы сравнения (`==`, `>`, `<`, `!=`), `like()`, `in_()` и логические операции (`and_`, `or_`)
```python
# Фильтрация активных категорий
stmt = select(Category).where(Category.is_active == True)

# Множественные условия
from sqlalchemy import and_
stmt = select(Category).where(and_(Category.is_active == True, Category.name.like('%tech%')))

# Альтернатива с несколькими where()
stmt = select(Category).where(Category.is_active == True).where(Category.name.like('%tech%'))
```
Методы `like()` и `ilike()` (регистронезависимый поиск) используются для поиска строк по шаблону.
[Описание оператора LIKE](https://tproger.ru/articles/like-sql)
```python
# Выборка категорий, имена которых начинаются с "Tech"
stmt = select(Category).where(Category.name.like('Tech%'))

# Выборка продуктов, в имени которых есть "phone" (регистронезависимо)
stmt = select(Product).where(Product.name.ilike('%phone%'))

# Выборка категорий, имена которых не содержат "Old"
stmt = select(Category).where(~Category.name.like('%Old%'))

# Также есть аналоги - startswith, endswith и contains
stmt = select(Post).where(Post.title.startswith('Python'))
stmt = select(User).where(User.email.endswith('@gmail.com'))
stmt = select(Post).where(Post.content.contains('important'))
```
Методы `is_()` и `is_not()` используются для проверки значений `NULL`
```python
# Выборка продуктов, у которых category_id равен NULL
stmt = select(Product).where(Product.category_id.is_(None))

# Выборка продуктов, у которых category_id не NULL
stmt = select(Product).where(Product.category_id.is_not(None))
```
Метод `in_()` проверяет, входит ли значение столбца в список, а `not_in()` — не входит.
```python
# Выборка категорий с id 1, 2 или 3
stmt = select(Category).where(Category.id.in_([1, 2, 3]))

# Выборка продуктов, цена которых не в списке [10.0, 20.0, 30.0]
stmt = select(Product).where(Product.price.not_in([10.0, 20.0, 30.0]))

# найти всех пользователей, чей возраст находится в диапазоне от 20 до 30 лет
stmt = select(User).where(User.age.between(20, 30))
```
Логические комбинации с `and_`, `or_`, `not_`
```python
from sqlalchemy import and_, or_, not_

# Выборка активных категорий с именем, начинающимся на "Tech"
stmt = select(Category).where(and_(Category.is_active == True, Category.name.like('Tech%')))

# Выборка продуктов с ценой > 100 или количеством на складе < 10
stmt = select(Product).where(or_(Product.price > 100.0, Product.stock < 10))

# Выборка категорий, которые НЕ являются активными
stmt = select(Category).where(not_(Category.is_active == True))
```
Фильтрация по датам
```python
from datetime import datetime, timedelta

# Выборка категорий, созданных после 1 января 2025 года
stmt = select(Category).where(Category.created_at > datetime(2025, 1, 1))

# Выборка категорий, созданных за последние 30 дней
stmt = select(Category).where(Category.created_at >= datetime.now() - timedelta(days=30))
```

**order_by()** задает сортировку результатов
```python
from sqlalchemy import desc

# Сортировка по имени по возрастанию
stmt = select(Category).order_by(Category.name)

# Выборка категорий с именем "Electronics" 
stmt = select(Category).where(Category.name == 'Electronics')

# Выборка продуктов с ценой меньше или равно 50
stmt = select(Product).where(Product.price <= 50.0)

# Сортировка по имени по убыванию
stmt = select(Category).order_by(desc(Category.name))

# Множественная сортировка
stmt = select(Category).order_by(Category.is_active, desc(Category.name))
```

**join()** позволяет объединять таблицы, аналогично `JOIN` в SQL
```python
# JOIN между Category и Product по связи
stmt = select(Category).join(Product, Category.id == Product.category_id)

# Явное указание связи через ORM
stmt = select(Category).join(Category.products)

# INNER JOIN (по умолчанию)
stmt = select(User, Order).join(Order)

# LEFT OUTER JOIN
stmt = select(User, Order).outerjoin(Order)

# RIGHT OUTER JOIN (не все БД поддерживают)
stmt = select(User, Order).join(Order, isouter=False)  # или через dialect-specific методы

# FULL OUTER JOIN
stmt = select(User, Order).join(Order, full=True)
```


**limit()** ограничивает количество возвращаемых строк
```python
# Получить только 5 категорий
stmt = select(Category).limit(5)
```

**offset()** пропускает указанное количество строк перед возвратом результатов
```python
# Пропустить первые 10 категорий и взять следующие 5
stmt = select(Category).offset(10).limit(5)
```

###### Выполнение запроса
```python
from typing import AsyncGenerator  
from sqlalchemy.ext.asyncio import AsyncSession  
from app.database import async_session_maker  
  
  
async def get_async_db() -> AsyncGenerator[AsyncSession, None]:  
    """  
    Предоставляет асинхронную сессию SQLAlchemy для работы с базой данных PostgreSQL.    """    async with async_session_maker() as session:  
        yield session
```
Методы **AsyncSession**
**await execute()** отправляет запрос в базу данных и возвращает объект Result, содержащий результаты. Без дополнительных методов, таких как `scalars()`, результат будет содержать объекты `Row`(список кортежей).
```python
async def get_all_categories(db: AsyncSession = Depends(get_async_db)):
...
    stmt = select(Category).where(Category.is_active == True)
    result = await session.execute(stmt)
    return result
```

**await scalars()** извлекает первый столбец из каждой строки результата, возвращая объекты ScalarResult. Обычно используется для получения ORM-объектов. Используйте `scalars()` для получения списка ORM-объектов или скалярных значений (например, `id` или `name`).

Преобразует результат запроса (объект `Result`) в итератор, содержащий либо объекты модели (если в запросе выбрана модель, например, `select(Category)`), либо скалярные значения (если выбраны отдельные столбцы, например, `select(Category.id)`).

```python
result = await session.execute(stmt).scalars()
```

>[!tip] В SQLAlchemy 2.0 метод `scalars()` можно использовать как через `db.execute(stmt).scalars()`, так и напрямую через `db.scalars(stmt)`, что делает код более компактным.

**await db.delete(obj)**: Помечает объект для удаления и выполняет запрос `DELETE` асинхронно. `await` ожидает завершения удаления, синхронизируя сессию с базой.

**await db.commit()**: Фиксирует изменения (обновление или удаление) в базе асинхронно. `await` гарантирует, что транзакция завершена перед продолжением.

**await db.refresh(obj)**: Обновляет объект данными из базы (например, после обновления `is_active`). `await`ожидает завершения операции.

**all()** возвращает все результаты запроса в виде списка.
```python
result = await session.execute(stmt).scalars()
categories = result.all()
# Возвращает: [<Category 1>, <Category 2>, ...]
```

**first()** возвращает первый результат запроса или `None`, если результат пустой.
```python
result = await session.execute(stmt).scalars()
categories = result.first()
# Возвращает: <Category 1> или None
```

**scalar()** возвращает первое значение первого столбца первой строки или `None`, если результат пустой.
```python
count = session.execute(select(func.count(Category.id))).scalar()
# Возвращает: 10 (например, количество категорий)
```

**unique()** Если запрос может вернуть дубликаты (например, при использовании `join`), метод `.unique()` помогает получить только уникальные результаты.
```python
result = await db.scalars(stmt)
return result.unique().all()
```

**fetch(n)** для получения ограниченного числа результатов
```python
result = await db.scalars(stmt)
return result.fetch(7).all()
```

###### Объект `func` в SQLAlchemy
>[!info] Объект `func` в SQLAlchemy — это универсальный интерфейс для вызова SQL-функций

Агрегатные функции (`COUNT`, `SUM`, `AVG`, `MAX`, `MIN`) используются для вычислений над набором строк.
```python
stmt = select(func.count(User.id))
total_users = session.execute(stmt).scalar()
```

```python
stmt = select(func.min(Post.created_at), func.max(Post.created_at))
min_date, max_date = session.execute(stmt).first()
```

Строковые функции (`UPPER`, `LOWER`, `LENGTH`, `CONCAT`) используются для преобразования или анализа строковых данных.
```python
stmt = select(Post.title, func.length(Post.title))
result = session.execute(stmt).all()  # Для получения обеих колонок
```

```python
stmt = select(Book).where(func.length(Book.title) < 10)
```

#### Alembic
Alembic для миграций, работает в связке с SQLAlchemy.

>[!info] Существует библиотека от создателя FastAPI, которая представляет собой связку из SQLAlchemy и Pydantic - [SQLModel](https://sqlmodel.tiangolo.com/)

```bash
pip install sqlalchemy alembic asyncpg
```
```bash
pip install pydantic-settings
```

##### Alembic
**Alembic** — это библиотека Python, разработанная для управления миграциями базы данных в проектах, использующих SQLAlchemy. Миграции — это способ отслеживать и применять изменения в структуре базы данных, такие как создание новых таблиц, добавление столбцов, изменение типов данных, создание индексов или удаление таблиц.

- **Автоматизация изменений**: Генерирует миграции на основе моделей SQLAlchemy, учитывая новые поля, таблицы или связи (например, самоссылающийся `parent_id` в `Category`).
- **История и откат**: Сохраняет изменения в скриптах (`app/migrations/versions`) и позволяет откатить их.
- **Командная работа**: Упрощает синхронизацию схемы через миграции, которыми можно поделиться.
- **Гибкость**: Поддерживает сложные изменения, такие как добавление внешних ключей, индексов или реорганизация данных.

```bash
pip install alembic
```

1. Инициализируем Alembic, чтобы создать структуру для миграций.
```bash
alembic init migrations
  # Для асинхронного SQLAlchemy
alembic init -t async migrations
```

В результате будут созданы следующие файлы:
- `alembic.ini`: Конфигурационный файл в корне проекта, задающий настройки подключения к базе данных и пути к миграциям.
- `app/migrations/env.py`: Определяет, как Alembic подключается к базе данных и какие модели SQLAlchemy использовать.
- `app/migrations/script.py.mako`: Шаблон для генерации скриптов миграций, задающий их структуру (функции `upgrade()` и `downgrade()`).
- `app/migrations/versions`: Папка для хранения скриптов миграций (пока пустая).
- `app/migrations/README`: Краткое описание директории миграций.

2. Файл `alembic.ini` содержит основные настройки Alembic. Откройте его в корне проекта и найдите строку с `sqlalchemy.url`. Замените её, чтобы указать путь к нашей базе данных, определённой в `app/database.py`
```python
# Например
sqlalchemy.url = sqlite:///ecommerce.db
```

Ключевые настройки в `alembic.ini`:
- `script_location = app/migrations`: Указывает папку для хранения миграций. Мы выбрали `app/migrations`, чтобы миграции были частью структуры проекта.
- `sqlalchemy.url = sqlite:///ecommerce.db`: Задаёт подключение к базе данных SQLite (файл `ecommerce.db` в корне проекта).
- `file_template`: Определяет формат именования файлов миграций (по умолчанию `%(rev)s_%(slug)s`, например, `123456_create_tables`). Можно раскомментировать и настроить, чтобы добавить дату (например, `%(year)d_%(month).2d_%(day).2d_%(rev)s_%(slug)s`).

3. Перед тем как начнем подключение наших моделей в Alembic, давайте создадим файл `__init__.py` в папке `app/models`. Это нужно чтобы удобно было импортировать модели в файле `env.py`
```python
from .categories import Category
from .products import Product 

__all__ = ["Category", "Product"]  # Перечисляем свои модели
```

4. Теперь перейдем к файлу `app/migrations/env.py`, он указывает Alembic, как подключаться к базе данных и какие модели использовать.
```python
# env.py
from app.database import Base      # Базовый класс, который наследуется от DeclarativeBase
from app import models             # пакет с моделями
```

И устанавливаем
```python
# env.py
target_metadata = Base.metadata
```

5. Проводим миграцию коммандой
```bash
# генерация миграции, где Initial migration - это комментарий
alembic revision --autogenerate -m "Initial migration"
```

```bash
# применение миграции
alembic upgrade head
```

Откатить миграцию
```bash
alembic downgrade -1
```

###### Основные команды Alembic

|комманда|описание|пример|
|---|---|---|
|`alembic revision`|Создаёт новый скрипт миграции. Используется для генерации Python-файла в папке `app/migrations/versions`, описывающего изменения в базе данных. Флаг `--autogenerate` позволяет Alembic сравнить модели SQLAlchemy с текущей базой данных и автоматически сгенерировать изменения.|`alembic revision --autogenerate -m "Create tables"` создаёт скрипт для создания таблиц с сообщением «Create tables».|
|`alembic upgrade`|Применяет миграции к базе данных. Выполняет функцию `upgrade()` из скриптов миграций, обновляя базу до указанной версии.|`alembic upgrade head` применяет последнюю миграцию, создавая или изменяя таблицы. А команда `alembic upgrade +2`  применит две версии включая текущую для апгрейда|
|`alembic downgrade`|Откатывает миграции. Выполняет функцию `downgrade()` из скриптов миграций, возвращая базу данных к предыдущей версии|`alembic downgrade -1` откатывает последнюю применённую миграцию|
|`alembic history`|Показывает историю миграций. Выводит список всех миграций в папке `app/migrations/versions` с их идентификаторами (revision ID) и описаниями|`alembic history` покажет все миграции, например, `xxxxxxx -> yyyyyyy (head), Create tables`|
|`alembic heads`|Показывает текущую последнюю миграцию (head). Полезно для проверки, какая миграция считается последней|`alembic heads` выведет revision ID последней миграции|
|`alembic current`|Показывает текущую версию базы данных. Указывает, какая миграция применена к базе в данный момент|`alembic current` выведет revision ID текущей миграции или ничего, если база пустая|

#### Аутентификация и авторизация
Общая информация о JWT-токенах [[Django, DRF#Использование JWT-токенов]]

[Пример учебного проекта на FastAPI с использованием авторизации по JWT токенам](https://github.com/Permin0ff/fastapi_ecommerce)
###### Схема аутентификация с использованием access-токена и refresh-токена
Эта схема позволяет разделить ответственность между краткосрочным доступом и долгосрочным управлением сессией, обеспечивая баланс между безопасностью и удобством. Давайте разберём, как работает взаимодействие с использованием двух видов токенов.

1. **Вход пользователя (логин)**: Пользователь вводит логин и пароль в клиентском приложении, которое отправляет их на сервер через HTTPS-запрос к эндпоинту `/login`.
    
2. **Проверка учетных данных**: Сервер проверяет логин и пароль, сверяя их с данными в базе данных. Если аутентификация успешна, сервер переходит к генерации токенов.
    
3. **Генерация двух токенов**: Сервер создаёт два токена:
    
    - **Access-токен**: Короткоживущий (например, 15–60 минут), содержит информацию о пользователе (например, `user_id`, `role`) и подписывается секретным ключом. Он используется для доступа к защищённым эндпоинтам.
    - **Refresh-токен**: Долгоживущий (например, 7 дней или месяц), содержит минимум данных (например, идентификатор сессии или пользователя) и также подписывается. Refresh-токен сохраняется на сервере (например, в базе данных или Redis) с привязкой к пользователю и, возможно, устройству.
4. **Отправка токенов клиенту**: Сервер возвращает оба токена клиенту в ответе на запрос `/login`, например: `{"access_token": "eyJ...", "refresh_token": "abc..."}`. Refresh-токен обычно хранится в безопасном месте, например, в HttpOnly-куки или защищённом хранилище мобильного приложения.
    
5. **Использование access-токена для запросов**: Как и в первой схеме, клиент прикрепляет access-токен к каждому запросу к защищённым эндпоинтам в заголовке `Authorization: Bearer <token>`. Сервер проверяет валидность токена (подпись и срок действия) и обрабатывает запрос.
    
6. **Истечение срока действия access-токена**: Когда access-токен истекает, сервер возвращает ошибку 401 Unauthorized. Вместо того чтобы запрашивать у пользователя повторный ввод логина и пароля, клиент отправляет refresh-токен на специальный эндпоинт, например, `/refresh`.
    
7. **Обновление access-токена**: Клиент отправляет refresh-токен (обычно в теле POST-запроса, а не в заголовке) на эндпоинт `/refresh`. Сервер проверяет:
    
    - Валидность refresh-токена (подпись, срок действия).
    - Его наличие в базе данных и статус (не отозван ли он).  
        Если проверка успешна, сервер генерирует новый access-токен (и, при необходимости, новый refresh-токен, чтобы реализовать ротацию токенов). Новый access-токен возвращается клиенту, например: `{"access_token": "eyJ...", "refresh_token": "xyz..."}`.
8. **Продолжение работы**: Клиент использует новый access-токен для дальнейших запросов. Пользователь не замечает процесса обновления, так как всё происходит в фоновом режиме.
    
9. **Завершение сессии или отзыв токена**: Если пользователь выходит из аккаунта (например, через эндпоинт `/logout`) или сервер обнаруживает подозрительную активность, refresh-токен помечается как недействительный в базе данных. Это делает невозможным его использование для получения новых access-токенов, эффективно завершая сессию.
    
10. **Истечение refresh-токена**: Если срок действия refresh-токена истекает или он отозван, клиент получает ошибку при попытке обновления. В этом случае пользователю придётся заново пройти аутентификацию, отправив логин и пароль на `/login`.

- **Высокая безопасность:** Access token: 15 минут, Refresh token: 7 дней.
- **Средняя безопасность:** Access token: 1 час, Refresh token: 30 дней.
- **Низкая безопасность (не рекомендуется):** Access token: 1 день, Refresh token: 90 дней.

>[!tip] Рефреш-токены можно хранить в HTTP-only cookies для защиты от XSS-атак

###### JWT токены
```bash
pip install PyJWT python-multipart
```

Генерируем любой секретный ключ и записываем в `.env`
```bash
openssl rand -hex 32
```

Создаём модель пользователя
```python
from sqlalchemy import Boolean, Integer, String
from sqlalchemy.orm import Mapped, mapped_column

from app.database import Base


class User(Base):
    __tablename__ = "users"

    id: Mapped[int] = mapped_column(Integer, primary_key=True)
    email: Mapped[str] = mapped_column(String, unique=True, index=True, nullable=False)
    hashed_password: Mapped[str] = mapped_column(String, nullable=False)
    is_active: Mapped[bool] = mapped_column(Boolean, default=True)
    role: Mapped[str] = mapped_column(String, default="buyer") # "buyer" or "seller"
```

Генерируем и применяем миграцию
```bash
alembic revision --autogenerate -m "Add user model"
alembic upgrade head
```

Добавляем Pydantic схемы для валидации
```python
from pydantic import BaseModel, Field, ConfigDict, EmailStr
from typing import Optional


class UserCreate(BaseModel):
    email: EmailStr = Field(description="Email пользователя")
    password: str = Field(min_length=8, description="Пароль (минимум 8 символов)")
    role: str = Field(default="buyer", pattern="^(buyer|seller)$", description="Роль: 'buyer' или 'seller'")


class User(BaseModel):
    id: int
    email: EmailStr
    is_active: bool
    role: str
    model_config = ConfigDict(from_attributes=True)
```

Устанавливаем `bcrypt` для хэширования пароля с автоматической солью
```bash
pip install passlib "bcrypt==4.0.1"
```

Создаём `auth.py`
```python
from passlib.context import CryptContext
from fastapi.security import OAuth2PasswordBearer
from datetime import datetime, timedelta, timezone
import jwt
from fastapi import Depends, HTTPException, status
from sqlalchemy.ext.asyncio import AsyncSession
from sqlalchemy import select

from app.models.users import User as UserModel
from app.config import SECRET_KEY, ALGORITHM
from app.db_depends import get_async_db


# Создаём контекст для хеширования с использованием bcrypt
pwd_context = CryptContext(schemes=["bcrypt"], deprecated="auto")

ACCESS_TOKEN_EXPIRE_MINUTES = 30
REFRESH_TOKEN_EXPIRE_DAYS = 7

oauth2_scheme = OAuth2PasswordBearer(tokenUrl="users/token")

def hash_password(password: str) -> str:
    """
    Преобразует пароль в хеш с использованием bcrypt.
    """
    return pwd_context.hash(password)


def verify_password(plain_password: str, hashed_password: str) -> bool:
    """
    Проверяет, соответствует ли введённый пароль сохранённому хешу.
    """
    return pwd_context.verify(plain_password, hashed_password)


def create_access_token(data: dict):
    """
    Создаёт JWT с payload (sub, role, id, exp).
    """
    to_encode = data.copy()
    expire = datetime.now(timezone.utc) + timedelta(minutes=ACCESS_TOKEN_EXPIRE_MINUTES)
    to_encode.update({"exp": expire})
    return jwt.encode(to_encode, SECRET_KEY, algorithm=ALGORITHM)


def create_refresh_token(data: dict):  
    """  
    Создаёт рефреш-токен с длительным сроком действия.    
    """    
    to_encode = data.copy()  
    expire = datetime.now(timezone.utc) + timedelta(days=REFRESH_TOKEN_EXPIRE_DAYS)  
    to_encode.update({"exp": expire})  
    return jwt.encode(to_encode, SECRET_KEY, algorithm=ALGORITHM)
    
    
async def get_current_user(token: str = Depends(oauth2_scheme),
                           db: AsyncSession = Depends(get_async_db)):
    """
    Проверяет JWT и возвращает пользователя из базы.
    """
    credentials_exception = HTTPException(
        status_code=status.HTTP_401_UNAUTHORIZED,
        detail="Could not validate credentials",
        headers={"WWW-Authenticate": "Bearer"},
    )
    try:
        payload = jwt.decode(token, SECRET_KEY, algorithms=[ALGORITHM])
        email: str = payload.get("sub")
        if email is None:
            raise credentials_exception
    except jwt.ExpiredSignatureError:
        raise HTTPException(
            status_code=status.HTTP_401_UNAUTHORIZED,
            detail="Token has expired",
            headers={"WWW-Authenticate": "Bearer"},
        )
    except jwt.PyJWTError:
        raise credentials_exception
    result = await db.scalars(
        select(UserModel).where(UserModel.email == email, UserModel.is_active == True))
    user = result.first()
    if user is None:
        raise credentials_exception
    return user
    
    
async def get_current_seller(current_user: UserModel = Depends(get_current_user)):
    """ 
    Проверяет, что пользователь имеет роль 'seller'. 
    """ 
    if current_user.role != "seller": 
        raise HTTPException(status_code=status.HTTP_403_FORBIDDEN, detail="Only sellers can perform this action") 
    return current_user
```

Создаём эндпоинт для регистрации и получения токена. Не забудте подключить роутер в `main.py`!
```python
import jwt
from fastapi import APIRouter, Depends, HTTPException, status
from sqlalchemy.ext.asyncio import AsyncSession
from sqlalchemy import select
from fastapi.security import OAuth2PasswordRequestForm

from app.models.users import User as UserModel
from app.schemas import UserCreate, User as UserSchema
from app.db_depends import get_async_db
from app.auth import hash_password, verify_password, create_access_token, create_refresh_token
from app.config import SECRET_KEY, ALGORITHM  # ALGORITHM = "HS256"


router = APIRouter(prefix="/users", tags=["users"])


@router.post("/", response_model=UserSchema, status_code=status.HTTP_201_CREATED)
async def create_user(user: UserCreate, db: AsyncSession = Depends(get_async_db)):
    """
    Регистрирует нового пользователя с ролью 'buyer' или 'seller'.
    """
    # Проверка уникальности email
    result = await db.scalars(select(UserModel).where(UserModel.email == user.email))
    if result.first():
        raise HTTPException(status_code=status.HTTP_400_BAD_REQUEST,
                            detail="Email already registered")

    # Создание объекта пользователя с хешированным паролем
    db_user = UserModel(
        email=user.email,
        hashed_password=hash_password(user.password),
        role=user.role
    )

    # Добавление в сессию и сохранение в базе
    db.add(db_user)
    await db.commit()
    return db_user


@router.post("/token")  
async def login(form_data: OAuth2PasswordRequestForm = Depends(),  
                db: AsyncSession = Depends(get_async_db)):  
    """  
    Аутентифицирует пользователя и возвращает access_token и refresh_token   
    """    
    result = await db.scalars(select(UserModel).where(UserModel.email == form_data.username))  
    user = result.first()  
    if not user or not verify_password(form_data.password, user.hashed_password):  
        raise HTTPException(  
            status_code=status.HTTP_401_UNAUTHORIZED,  
            detail="Incorrect email or password",  
            headers={"WWW-Authenticate": "Bearer"},  
        )  
    access_token = create_access_token(data={"sub": user.email, "role": user.role, "id": user.id})  
    refresh_token = create_refresh_token(data={"sub": user.email, "role": user.role, "id": user.id})  
    return {"access_token": access_token, "refresh_token": refresh_token, "token_type": "bearer"}


@router.post("/refresh-token")  
async def refresh_token(refresh_token: str, db: AsyncSession = Depends(get_async_db)):  
    """  
    Обновляет access_token с помощью refresh_token.
    """
    credentials_exception = HTTPException(  
        status_code=status.HTTP_401_UNAUTHORIZED,  
        detail="Could not validate refresh token",  
        headers={"WWW-Authenticate": "Bearer"},  
    )  
    try:  
        payload = jwt.decode(refresh_token, SECRET_KEY, algorithms=[ALGORITHM])  
        email: str = payload.get("sub")  
        if email is None:  
            raise credentials_exception  
    except jwt.exceptions:  
        raise credentials_exception  
    result = await db.scalars(select(UserModel).where(UserModel.email == email, UserModel.is_active == True))  
    user = result.first()  
    if user is None:  
        raise credentials_exception  
    access_token = create_access_token(data={"sub": user.email, "role": user.role, "id": user.id})  
    return {"access_token": access_token, "token_type": "bearer"}
```

Защита эндпоинтов. Используйте зависимость, типа созданной ранее `get_current_seller` из `auth.py`
```python
@router.delete("/{product_id}", response_model=ProductSchema) 
async def delete_product( 
    product_id: int, 
    db: AsyncSession = Depends(get_async_db), 
    current_user: UserModel = Depends(get_current_seller) ):
    ...
```



### Особенности схемы с refresh-токеном

Эта схема сложнее, так как требует хранения refresh-токенов на сервере (stateful) и реализации дополнительного эндпоинта `/refresh`. Однако она имеет значительные преимущества:

- **Безопасность**: Короткий срок жизни access-токена минимизирует ущерб при его утечке. Refresh-токен, используемый редко, можно хранить более безопасно и отозвать при необходимости.
- **Удобство**: Пользователь не вынужден часто вводить логин и пароль, так как клиент автоматически обновляет access-токен в фоновом режиме.
- **Контроль**: Хранение refresh-токенов на сервере позволяет управлять сессиями, отзывать токены и вести аудит активных устройств.
- **Гибкость**: Поддержка ротации refresh-токенов (выдача нового при каждом рефреше) дополнительно повышает безопасность.

Эта схема идеально подходит для современных приложений, где важны как безопасность, так как и пользовательский опыт, например, в мобильных приложениях, SPA или корпоративных системах.

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

#### Версионирование API
###### Включение номера версии в URL
```python
from fastapi import FastAPI

app = FastAPI()


@app.get("/v1/products")
async def get_products_v1():
    return {"message": "Products API Version 1"}


@app.get("/v2/products")
async def get_products_v2():
    return {"message": "Products API Version 2"}
```

###### Добавление версии в качестве параметра запроса
```python
from fastapi import FastAPI

app = FastAPI()


@app.get("/products/")
async def get_products(version: int = 1):
    if version == 1:
        return {"message": "Products API Version 1"}
    elif version == 2:
        return {"message": "Products API Version 2"}
```

###### Версионирование на основе пути с использованием префикса URL
Лучшее решение - использовать подприложения для разделения каждой версии вашего API на собственный объект приложения. Таким образом, вы можете иметь больший контроль и гибкость над каждой версией, а также иметь независимую документацию для каждой версии. Например, вы можете иметь /v1/docs и /v2/docs в качестве разных эндпоинтов для разных версий вашей документации.

```python
from fastapi import FastAPI


app = FastAPI()
app_v1 = FastAPI( 
    title="My API v1", 
    description="The first version of my API", 
)
app_v2 = FastAPI( 
    title="My API v2", 
    description="The second version of my API", 
)


@app_v1.get("/products")
async def get_products_v1():
    return {"message": "Products API Version 1"}


@app_v2.get("/products")
async def get_products_v2():
    return {"message": "Products API Version 2"}

app.mount("/v1", app_v1)
app.mount("/v2", app_v2)
```

Теперь будет две версии документации по адресам `http://localhost:8000/v1/docs` и  `http://localhost:8000/v2/docs`
![[versions.png]]

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
# celery -A my_file:celery_instance worker
celery -A celery_config:celery worker --loglevel=INFO
```

Запуск Flower
```bash
celery -A celery_config:celery flower
```

Выполнить задачу через N времени
```python
call_background_task.apply_async(args=[message], countdown=60*5)
```

Выполнить задачу в определенное время
```python
task_datetime = datetime.now(timezone.utc) + timedelta(minutes=10) call_background_task.apply_async(args=[message], eta=task_datetime)
```

Отмена задачи, если она ещё на начала выполняться
```python
​​​​​​​from celery.result import AsyncResult

# Получаем task_id при вызове задачи
result = call_background_task.apply_async(args=[message], countdown=60*5)
task_id = result.id

# Отменяем задачу
AsyncResult(task_id).revoke()
```

###### Переодические задачи. Celery Beat
>[!info] В Celery периодические задачи (periodic tasks) — это задачи, которые выполняются автоматически через определённые интервалы времени или в запланированные моменты. Для их реализации в Celery используется компонент  `celery beat`.

```python
# main.py
from fastapi import FastAPI
from celery import Celery
from task import call_background_task

app = FastAPI()

celery = Celery(
    __name__,
    broker='redis://127.0.0.1:6379/0',
    backend='redis://127.0.0.1:6379/0',
    broker_connection_retry_on_startup=True
)



@app.get("/")
async def hello_world(message: str):
    call_background_task.apply_async(args=[message], expires=3600)
    return {'message': 'Hello World!'}


celery.conf.beat_schedule = {
    'run-me-background-task': {
        'task': 'task.call_background_task',
        'schedule': 60.0,
        'args': ('Test text message',)
    }
}
```

```python
# task.py
import time  
from celery import shared_task  
  
@shared_task()  
def call_background_task(message):  
    time.sleep(3)  
    print(f"Background Task called!")  
    print(message)
```

Запуск
```bash
celery -A main.celery beat --loglevel=info
```
```bash
celery -A main.celery worker --loglevel=info
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

#### Middleware
>[!info] В FastAPI промежуточное программное обеспечение находится перед эндпоинтами API, обрабатывая запросы и ответы. Когда поступает запрос, он проходит через уровень Middleware, прежде чем достичь эндпоинта API. Аналогичным образом, когда ответ готов, он проходит через уровень Middleware, прежде чем быть отправленным обратно клиенту.

![[middleware-flow.png]]

Встроенное промежуточное программное обеспечение в FastAPI и его функциональные возможности:
1. **CORSMiddleware**: Включает необходимые заголовки CORS в исходящих ответах для включения запросов с перекрестным исходным доступом из веб-браузеров.
2. **TrustedHostMiddleware**: Проверяет заголовок хоста входящих запросов для предотвращения потенциальных атак заголовка хоста HTTP.
3. **HTTPSRedirectMiddleware**:  Проверяет входящие запросы, чтобы они были на HTTPS. В ином случае выполнит редирект.
4. **SessionMiddleware**: Реализует подписанные HTTP-сеансы на основе файлов cookie, в которых данные сеанса читаемы, но не редактируются.
5. **GZip Middleware**: сжимает ответы для снижения использования трафика, что приводит к более быстрой передаче данных.

###### Custom middleware на основе класса
```python
from fastapi import FastAPI
import time


class TimingMiddleware:
    def __init__(self, app):
        self.app = app

    async def __call__(self, scope, receive, send):
        start_time = time.time()
        await self.app(scope, receive, send)
        duration = time.time() - start_time
        print(f"Request duration: {duration:.10f} seconds")


app = FastAPI()
app.add_middleware(TimingMiddleware)


@app.get("/hello")
async def greeter():
    return {"Hello": "World"}


@app.get("/goodbye")
async def farewell():
    return {"Goodbye": "World"}

```

###### Custom middleware на основе функции
```python
from fastapi import FastAPI, Request
import time

app = FastAPI()


@app.middleware("http")
async def modify_request_response_middleware(request: Request, call_next):
    start_time = time.time()
    response = await call_next(request)
    duration = time.time() - start_time
    print(f"Request duration: {duration:.10f} seconds")
    return response


@app.get("/hello")
async def greeter():
    return {"Hello": "World"}


@app.get("/goodbye")
async def farewell():
    return {"Goodbye": "World"}
```

###### CORS
>[!info] CORS нужен только в случае, если ваш бэкенд обслуживает запросы от фронтенда, который находится на другом домене, порту.

```python
from fastapi import FastAPI
from fastapi.middleware.cors import CORSMiddleware

app = FastAPI()

origins = [ 
    "http://localhost:3000", 
    "https://example.com",
]

app.add_middleware(
    CORSMiddleware,
    allow_origins=origins,
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)
```
