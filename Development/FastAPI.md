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

Автоматическая документация `/docs`

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

###### Query. Получение GET параметров
```python
@app.get('/some}')            # поскольку в пути нет {} FastAPI понимает,
def getsome(getparam):        #   что это query параметры
    return f'Your get param: {getparam}'
```

  Опциональные параметры
```python
from fastapi import FastAPI  
from typing import Optional  
  
app = FastAPI()  
  
@app.get('/hi')  
def great(who: Optional[str] = 'Default'):  
    return f"Hello, {who}!"
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

###### Валидация ответа
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

Пример зависимости с возвращаемым значением
```python
from fastapi import FastAPI, Depends, Params

app = FastAPI()

# функция зависимости
def user_dep(name: str = Params, password: str = Params):
    return {"name": name, "valid": True}

# эндпоинт
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

# схема
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

Создаём `db.py`
```python
from sqlalchemy.ext.asyncio import AsyncSession, create_async_engine  
from sqlalchemy.orm import DeclarativeBase, sessionmaker  
from config import settings
  
engine = create_async_engine(settings.DATABASE_URL)

# создание сессии
async_session_maker = sessionmaker(engine, class_=AsyncSession, expire_on_commit=False)  
  
# класс для миграций
class Base(DeclarativeBase):  
    pass
```

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
Чтобы Pydantic мог корректно читать данные SQLAlchemy и валидировать их мы создаём схему с режимом ORM
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
>[!info] Используется шаблонизатор `Jinja2`

Создаём директорию `templates` и в ней `test.html`
```python
from fastapi import APIRouter, Request  
from fastapi.templating import Jinja2Templates 
  

page_router = APIRouter(prefix='/pages', tags=['Шаблоны', ])  

templates = Jinja2Templates(directory='templates') 
  
@page_router.get('')  
async def get_page(request: Request):  
    return templates.TemplateResponse(name='test.html', context={'request': request})
```

Не забываем, что роутер ещё нужно подключить

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