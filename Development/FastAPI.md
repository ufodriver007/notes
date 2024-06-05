![[fastapi.png]]

```
pip install fastapi[all]
```


Минимальное приложение
```
from fastapi import FastAPI  
  
app = FastAPI()  
  
@app.get('/some')  
def getsome():  
    return 'Some text'
```

###### Запуск
>[!info]  `--reload`: перезапуск сервера после изменения кода. Делайте это только во время разработки.
```
uvicorn main:app --reload
```

Автоматическая документация `/docs`

#### Urls
###### Динамический путь

Пример получения динамической части url (например `/some/33`)
```
@app.get('/some/{new_id}')  
def getsome(new_id: int):  
    return f'Your new ID: {new_id}'
```

###### Получение GET параметров
```
@app.get('/some}')  
def getsome(getparam):  
    return f'Your get param: {getparam}'
```

  Опциональные параметры
```
from fastapi import FastAPI  
from typing import Optional  
  
app = FastAPI()  
  
@app.get('/some')  
def getsome(has_param: Optional[bool] = None):  
    return f'Your get param: {has_param}'
```

Валидация возвращаемых типов в GET запросах
```
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

###### Typing
- Дата
```
from datetime import date
```

- Ограничения
Пример GET-параметр `new_id`, который должен быть больше 1, но меньше 5
```
from fastapi import Query

@app.get('/some')  
def getsome(new_id: int = Query(ge=1, le=5)):  
    return f'Your new ID: {new_id}'
```

###### POST запросы
```
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

