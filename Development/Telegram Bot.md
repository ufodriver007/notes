![[aiogram.png]]

>[!info] В его основе диспетчер, который распределяет апдейты, полученные с серверов телеграм, по хэндлерам.

![[Pasted image 20240104135335.png]]

>[!warning] Храните токен в переменной окружения

```
pip install python-dotenv
```
Создаём файл `.env` и записываем туда все переменные окружения, которые будут добавляться каждый раз(не забываем закинуть его в `.gitignore`). В Python:

```
import os
from dotenv import load_dotenv

load_dotenv()                                # take environment variables from .env.
PASSWORD = os.getenv('PASSWORD')
```

#### Установка Aiogram 3
``` 
pip install -U --pre aiogram                 # версия 3
```

#### Взаимодействие с TelegramBotAPI
```
https://api.telegram.org/bot<token>/<method>
```

Например:
```
https://api.telegram.org/bot5588819648:AAFFt8LyIdI337y9Uv2iG3bj7lO7vbMG85E/getMe
```

Возвращает json. Некоторые методы:

|Метод|Описание|
|--------|-----------|
|getMe|                          Информация о боте
|getUpdates|                     Получение всех апдейтов
|getUpdates?offset=-1|           Последний апдейт

**2 режима работы:**
1) **LongPolling**
   Программа постоянно опрашивает сервера Telegram. Подключается к серверу, если в течении заданного таймаута есть апдейт, прога получает его и  соединение закрывается. Если апдейта нет, соединение висит в течении таймаута.
2) **Webhook**
   Работает только тогда, когда Telegram присылает сообщение программе
   
#### Регистрация хендлеров
![[basics_middleware.webp]]
>[!info] Чтобы не писать декораторы к каждой функции, типа `@dp.message_handler(commands='moderator')`, можно объеденить это одной функцией

```
def register_handlers_admin(dp: Dispatcher):
    dp.message.register(process_start_command, Command(commands=['start']))
    dp.message.register(process_help_command, Command(commands=['help']))
    dp.message.register(send_echo)
```

###### Пример хендлера
```
from aiogram.filters import Command       # чтобы фильтровать апдейты по наличию в них команд,
                                          # то есть сочетаний символов, начинающихся со знака "/".

# В хэндлер будут попадать /start и |start
# добавляет знак | как префикс для команды
async def default_handler(Command(commands=['start'], prefix='|')):
    # ответ в тот же чат
    await message.answer('Какой-то текст')      
     
    # сообщение в любой чат                    
    await bot.send_message(chat_id='ID или название чата', text='Какой-то текст')    
```

#### Фильтры
>[!info] Встроенная или своя функция(можно лямбду), которая возвращает True или False. Также фильтром может быть класс.

Есть фильтр отдельно на команду `start`:
```
from aiogram.filters import CommandStart
@dp.message(CommandStart())
```

