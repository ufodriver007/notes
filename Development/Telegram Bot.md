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
https://api.telegram.org/bot1234567890:ASDF12345678qwerty/getMe
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

###### Фильтр текста
|Фильтр|Описание|
|-------|-----------|
|`Text(text='какой-то текст')`|полностью совпадает с каким-то текстом
|`Text(startswith='начало какого-то текста')`|начинается с какого-то конкретного текста
|`Text(endswith='конец какого-то текста')`|заканчивается каким-то текстом
|`Text(contains='какой-то текст')`|содержит в себе какой-то текст

Также у фильтра Text есть параметр `ignore_case`, который по умолчанию `False`. Например:
```
@dp.message(Text(endswith=['бот'], ignore_case=True))
```

Чтобы бот реагировал на фото, нужно импортировать маг. фильтр F, создать хэндлер, зарегистрировать хэндлер с фильтром фото.
```
from aiogram import F

# Этот хэндлер будет срабатывать на отправку боту фото
async def send_photo_echo(message: Message):
    await message.reply_photo(message.photo[0].file_id)  # фото хранится в 3х разрешениях(0 самое
                                                         #  маленькое, 2 - большое)

dp.message.register(send_photo_echo, F.photo)            # или же @dp.message(F.photo)
                                                         # также вместо фильтра можно поставить 
                                                         #  свою функцию, возвращающую bool
                                                         # также можно использовать лямбды dp.message.register(send_photo_echo, lambda msg: msg.text == '/start'))
```

```
from aiogram.types import Message, PhotoSize

@router.message(F.photo[-1].as_("largest_photo"))  # проталкивание переменной из фильтра в хэндлер
async def forward_from_channel_handler(message: Message, largest_photo: PhotoSize) -> None:
    print(largest_photo.width, largest_photo.height)
```

>[!info] Можно также применять фильтр `F.content_type == ContentType.PHOTO`, вместо `F.photo`

Можно в фильтре сразу указывать размер фото:
```
from aiogram.types import PhotoSize
@dp.message(F.photo[0].as_('photo_min'))
# передача данных из маг. метода в хэндлер
async def process_photo_send(message: Message, photo_min: PhotoSize): 
    print(photo_min)
```

Или фильтрация по нескольким типам:
```
F.content_type.in_({'voice', 'video', 'text'})
```

>[!info] Документация по типам: https://docs.aiogram.dev/en/dev-3.x/api/enums/content_type.html#module-aiogram.enums.content_type

Примеры магических фильтров:
```
F.photo                                    # Фильтр для фото
F.voice                                    # Фильтр для голосовых сообщений
F.content_type.in_({ContentType.PHOTO,
                    ContentType.VOICE,
                    ContentType.VIDEO})    # Фильтр на несколько типов контента
F.text == 'привет'                         # Фильтр на полное совпадение текста
F.text.startswith('привет')                # Фильтр на то, что текст сообщения начинается с 'привет'
~F.text.endswith('bot')                    # Инвертирование результата фильтра(побитовое отрицание)
F.from_user.id == 173901673                # фильтрует только апдейты от пользователя 173901673
F.from_user.id.in_({193905674, 173901673, 144941561}) # только апдейты от людей из списка
```

>[!info] Фреймворк aiogram предписывает нам создавать свои фильтры, наследуясь от классa `BaseFilter`, который находится в `aiogram.filters`. Магический метод `__call__ `должен быть асинхронным и должен возвращать либо `True/False`, либо словарь. 

```
from aiogram.filters import BaseFilter

admin_ids: list[int] = [173901673, 178876776, 197177271]


class IsAdmin(BaseFilter):
    def __init__(self, admin_ids: list[int]) -> None:
        self.admin_ids = admin_ids

    async def __call__(self, message: Message) -> bool:
        return message.from_user.id in self.admin_ids
```

#### Комбинирование фильтров
1. Если фильтры указать через запятую, то между ними будет проверяться условие "И"
```
@dp.message(F.photo, F.from_user.id == 173901673)    # True and True == True
```
2. Если нужно, чтобы фильтры работали по условию "ИЛИ", то есть хэндлер срабатывал бы тогда, когда хотя бы один фильтр из цепочки возвращал `True` - нужно регистрировать хэндлер столько раз, сколько фильтров в цепочке.
3. Если нужно инвертировать результат работы фильтра - используется знак ~ перед фильтром.
```
@dp.message(~Text(startswith='привет'))
```
4. Магические фильтры можно комбинировать, используя операции побитового сравнения И/ИЛИ (& / |).
```
@dp.message((F.text | F.photo) & F.from_user.id == 173901673)
```


#### Типы апдейтов
- Audio
- Video
- Sticker
- Animation
- Document
- Voice

>[!info] Message - универсальный класс, который принимает любые типы апдейтов.

Можно получить представление JSON с отступом 4:
```
print(message.json(indent=4, exclude_none=True))  
# exclude_none: bool  - если True, пропускает всё равное None
```

Для изучения объекта message, можно в хэндлере просто принтом вывести этот объект.
```
async def send_photo_echo(message: Message):
    print(message)
```

#### Структура бота
Пример возможной структуры бота:

|Файлы/Папки|Описание|
|------|------------|
|.env|секретные данные(пароли, токены и тп)|
|.env.example|пример файла с секретными данными, которые нужно хранить на гитхабе, чтобы была понятна структура|
|bot.py|основной файл проекта - точка входа в проект|
|(d)config_data|Пакет для хранения файлов с конфигурационными данными|
|(d)errors|Пакет с модулями для обработки исключений, возникающих в процессе работы бота.
|(d)external_services|Пакет с модулями для работы с API внешних сервисов.
|(d)filters|Пакет с кастомными фильтрами, если не хватает встроенных фильтров самого aiogram
|(d)handlers|Пакет, в котором хранятся обработчики апдейтов.
|(d)keyboards|Пакет с модулями, в которых хранятся и/или динамически формируются клавиатуры, отправляемые пользователям
|(d)exicon|Пакет для хранения текстов - ответов бота(возможно на нескольких языках)
|(d)midlewares|Пакет, в котором хранятся мидлвари, то есть программы, которые работают с апдейтами до того момента, как они попадут в хэндлер.
|(d)models|Пакет с модулями для взаимодействия с базой данных
|(d)services|Пакет с модулями для реализации какой-то бизнес-логики бота.
|(d)states|Пакет с модулями, в которых описаны классы, отражающими возможные состояния пользователей, в процессе взаимодействия с ботом, для реализации машины состояний.
|(d)tests|Пакет с тестами
|(d)utils|Пакет для хранения вспомогательных модулей

#### Пример файла с конфигом
```
from dataclasses import dataclass
from environs import Env


@dataclass
class DatabaseConfig:
    database: str         # Название базы данных
    db_host: str          # URL-адрес базы данных
    db_user: str          # Username пользователя базы данных
    db_password: str      # Пароль к базе данных


@dataclass
class TgBot:
    token: str            # Токен для доступа к телеграм-боту
    admin_ids: list[int]  # Список id администраторов бота


@dataclass
class Config:
    tg_bot: TgBot
    db: DatabaseConfig


def load_config(path: str | None) -> Config:

    env: Env = Env()
    env.read_env(path)

    return Config(tg_bot=TgBot(token=env('BOT_TOKEN'),
                               admin_ids=list(map(int, env.list('ADMIN_IDS')))),
                               db=DatabaseConfig(database=env('DATABASE'),
                               db_host=env('DB_HOST'),
                               db_user=env('DB_USER'),
                               db_password=env('DB_PASSWORD')))
```

Теперь осталось только дописать в `bot.py`:
```
from config_data.config import load_config

config = load_config('<путь к файлу .env>')
```

И далее можно использовать `config` там, где нужно:
```
bot_token = config.tg_bot.token           # Сохраняем токен в переменную bot_token
superadmin = config.tg_bot.admin_ids[0]   # Сохраняем ID админа в переменную superadmin
```

#### Точка входа
```
import asyncio

from aiogram import Bot, Dispatcher
from config_data.config import Config, load_config


# Функция конфигурирования и запуска бота
async def main() -> None:

    # Загружаем конфиг в переменную config
    config: Config = load_config()

    # Инициализируем бот и диспетчер
    bot: Bot = Bot(token=config.tg_bot.token)
    dp: Dispatcher = Dispatcher()

    # Регистриуем роутеры в диспетчере
    dp.include_router(user_handlers.router)
    dp.include_router(other_handlers.router)

    # Пропускаем накопившиеся апдейты и запускаем polling
    await bot.delete_webhook(drop_pending_updates=True)
    await dp.start_polling(bot)


if __name__ == '__main__':
    asyncio.run(main())
```

#### Роутеры
Диспетчер - корневой роутер. Можно и нужно создавать дополнительные роутеры(последовательно, деревом).
![[Pasted image 20240104211243.png]]
На роутеры можно вешать фильтры(например только администраторы или польз. из списка) и мидлвари.

Вот пример фильтра на роутер:
```
router: Router = Router()
router.message.filter(IsAdmin(config.tg_bot.admin_ids))
```

В файле с хэндлером:
```
from aiogram import Router
 
# Инициализируем роутер уровня модуля
router: Router = Router()

# Этот хэндлер срабатывает на команду /start
@router.message(CommandStart())        # теперь декорируем через объект router, а не dp как раньше
async def process_start_command(message: Message):
    await message.answer(text=LEXICON_RU['/start'])
```

В точке входа:
```
# Регистриуем роутеры в диспетчере. ПОРЯДОК ВАЖЕН!
dp.include_router(user_handlers.router)
dp.include_router(other_handlers.router)
```

#### Кнопки
###### Обычные кнопки
```
from aiogram.types import (KeyboardButton, ReplyKeyboardMarkup,
                              ReplyKeyboardRemove)

# Создаем объекты кнопок
button_1: KeyboardButton = KeyboardButton(text='Собак 🦮')
button_2: KeyboardButton = KeyboardButton(text='Огурцов 🥒')

# Создаем объект клавиатуры, добавляя в него кнопки
keyboard: ReplyKeyboardMarkup = ReplyKeyboardMarkup(
                                keyboard=[[button_1, button_2]],
                                resize_keyboard=True,            # кнопки меньше обычного
                                one_time_keyboard=True,          # клавиатура сворачивается
                                input_field_placeholder='Нажмите кнопку 1')  # плейсхолдер в строке ввода      

# Этот хэндлер будет срабатывать на команду "/start" и отправлять в чат клавиатуру
@dp.message(CommandStart())
async def process_start_command(message: Message):
    await message.answer(text='Чего кошки боятся больше?',
                        reply_markup=keyboard)

# Этот хэндлер будет срабатывать на ответ "Собак 🦮" и удалять клавиатуру
@dp.message(Text(text='Собак 🦮'))
async def process_dog_answer(message: Message):
    await message.answer(text='Да, несомненно, кошки боятся собак. '
                            'Но вы видели как они боятся огурцов?',
                            reply_markup=ReplyKeyboardRemove())
```

###### Расположение кнопок
```
from aiogram.utils.keyboard import ReplyKeyboardBuilder

# инициализируем билдер
kb_builder: ReplyKeyboardBuilder = ReplyKeyboardBuilder()

# создаём список с кнопками
buttons: list[KeyboardButton] = [KeyboardButton(text=f'Кнопка {i + 1}') for i in range(10)]
   
# добавляем в билдер кнопки
kb_builder.row(*buttons, width=4)     # width - желаемое кол-во кнопок в ряду(другие переносятся на след.)
kb_builder.add(*buttons)              # метод добавляет в строку до 8ми кнопок
# Явно сообщаем билдеру сколько хотим видеть кнопок в 1-м и 2-м рядах. Используем в связке .add и .adjust
#  а также говорим методу повторять такое размещение для остальных рядов
kb_builder.adjust(1, 3, repeat=True)

# передаём клавиатуру
await message.answer(text='Вот такая получается клавиатура', reply_markup=kb_builder.as_markup())
```

###### Специальные кнопки
```
contact_btn: KeyboardButton = KeyboardButton(
                                   text='Отправить телефон',
                                   request_contact=True)
geo_btn: KeyboardButton = KeyboardButton(
                                   text='Отправить геолокацию',
                                   request_location=True)
poll_btn: KeyboardButton = KeyboardButton(
                                   text='Создать опрос/викторину',
                                   request_poll=KeyboardButtonPollType(type='quiz'))  
                                   # type='quiz' - викторина; type='regular' - опрос
```

###### Кнопка для веб-приложения
```
from aiogram.types.web_app_info import WebAppInfo


# Создаем кнопку
web_app_btn: KeyboardButton = KeyboardButton(
                                   text='Start Web App',
                                   web_app=WebAppInfo(url="https://stepik.org/"))

# Создаем объект клавиатуры
web_app_keyboard: ReplyKeyboardMarkup = ReplyKeyboardMarkup(
                                               keyboard=[[web_app_btn]],
                                               resize_keyboard=True,
                                               input_field_placeholder='Нажмите кнопку 1')


# Этот хэндлер будет срабатывать на команду "/web_app"
@dp.message(Command(commands='web_app'))
async def process_web_app_command(message: Message):
    await message.answer(text='Экспериментируем', reply_markup=web_app_keyboard)
```

#### Инлайн кнопки
###### URL-кнопки(переводит в браузер по ссылке)
```
from aiogram.types import InlineKeyboardButton, InlineKeyboardMarkup

# Создаем объекты инлайн-кнопок
url_button_1: InlineKeyboardButton = InlineKeyboardButton(
             text='Курс "Телеграм-боты на Python и AIOgram"',
             url='https://stepik.org/120924')
url_button_2: InlineKeyboardButton = InlineKeyboardButton(
             text='Документация Telegram Bot API',
             url='https://core.telegram.org/bots/api')

# Создаем объект инлайн-клавиатуры
keyboard: InlineKeyboardMarkup = InlineKeyboardMarkup(
             inline_keyboard=[[url_button_1],
                              [url_button_2]])

# Этот хэндлер будет срабатывать на команду "/start"
# и отправлять в чат клавиатуру c url-кнопками
@dp.message(CommandStart())
async def process_start_command(message: Message):
    await message.answer(text='Это инлайн-кнопки с параметром "url"',
                               reply_markup=keyboard)
```

###### Callback-кнопки
```
from aiogram.types import InlineKeyboardButton, InlineKeyboardMarkup
from aiogram.filters import Text
from aiogram.types import CallbackQuery

# Создаем объекты инлайн-кнопок
button_1: InlineKeyboardButton = InlineKeyboardButton(
             text='Кнопка 1',
             callback_data='button_1_pressed')
button_2: InlineKeyboardButton = InlineKeyboardButton(
             text='Кнопка 2',
             callback_data='button_2_pressed')

# Создаем объект инлайн-клавиатуры
keyboard: InlineKeyboardMarkup = InlineKeyboardMarkup(
             inline_keyboard=[[button_1],
                              [button_2]])
markup: InlineKeyboardMarkup = InlineKeyboardMarkup(
             inline_keyboard=keyboard)

# Этот хэндлер будет срабатывать на команду "/start"
# и отправлять в чат клавиатуру c url-кнопками
@dp.message(CommandStart())
async def process_start_command(message: Message):
    await message.answer(text='Это инлайн-кнопки',
                               reply_markup=keyboard)      

# Этот хэндлер будет срабатывать на апдейт типа CallbackQuery
# с data 'button_1_pressed' или 'big_button_2_pressed'
@dp.callback_query(Text(text=['button_1_pressed',
                                    'button_2_pressed']))
async def process_buttons_press(callback: CallbackQuery):
    await callback.answer()                # убирает часики. Также есть необязательные аргументы:
                                           # show_alert=True и/или text='Ура! Нажата кнопка 1'
    # await callback.message.delete()      # Удаляем сообщение, в котором была нажата кнопка
    # await callback.message.edit_text(text='Edited', reply_markup=markup)      
```

- Создаем инлайн-кнопки с текстом, который будет отображаться на кнопке, и текстом в callback_data, который будет приходить в апдейте типа CallbackQuery в поле data.
- Создаем объект инлайн-клавиатуры и добавляем в него массив массивов кнопок (параметр inline_keyboard)
- Отправляем инлайн-клавиатуру, вместе с текстом сообщения, пользователю
- Методом callback_query у диспетчера (роутера) ловим апдейт типа CallbackQuery, отфильтровываем его по полю data и направляем в соответствующий хэндлер
- В хэндлере либо модифицируем сообщение (текст и/или кнопки), либо отправляем пустой ответ callback.answer(), чтобы у пользователя не было ощущения, что бот завис в задумчивости

Также есть билдер и для инлайн кнопок:
```
from aiogram.utils.keyboard import InlineKeyboardBuilder

# инициализируем билдер
kb_builder: InlineKeyboardBuilder = InlineKeyboardBuilder()

# создаём список с кнопками
buttons: list[InlineKeyboardButton] = [InlineKeyboardButton(text=f'Кнопка {i + 1}') for i in range(10)]

# добавляем в билдер кнопки
kb_builder.row(*buttons, width=4)     # width - желаемое кол-во кнопок в ряду(другие переносятся на след.)
kb_builder.add(*buttons)              # метод добавляет в строку до 8ми кнопок

# передаём клавиатуру
await message.answer(text='Вот такая получается клавиатура', reply_markup=kb_builder.as_markup())
```

#### Calback Factory
