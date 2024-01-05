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

#### Callback Factory
Фабрику коллбэков удобно использовать тогда, когда требуется создавать динамические клавиатуры, меняющиеся в ходе жизни проекта. Например, если кнопки зависят от того, что в данный момент лежит в динамически меняющейся базе данных.

```
from aiogram.filters.callback_data import CallbackData

class MyCallbackFactory(CallbackData, prefix='any'):
        a: str
        b: int
        c: int
        d: str

class GoodsCallbackFactory(CallbackData, prefix='goods', sep='|'):
        category_id: int
        subcategory_id: int
        item_id: int

button_1: InlineKeyboardButton = InlineKeyboardButton(
                        text='Категория 1',
                        callback_data=GoodsCallbackFactory(
                                                category_id=1,
                                                subcategory_id=0,
                                                item_id=0).pack())

button_2: InlineKeyboardButton = InlineKeyboardButton(
                        text='Категория 2',
                        callback_data=GoodsCallbackFactory(
                                                category_id=2,
                                                subcategory_id=0,
                                                item_id=0).pack())

# Этот хэндлер ловит только категорию 1 и будет распечатывать данные callback
@dp.callback_query(GoodsCallbackFactory.filter(F.category_id == 1)))
async def process_category_press(callback: CallbackQuery,
                                     callback_data: GoodsCallbackFactory):
    await callback.message.answer(await callback.message.answer(
                                     text=f'Категория товаров: {callback_data.category_id}\n'\
                                     f'Подкатегория товаров: {callback_data.subcategory_id}\n'\
                                     f'Товар: {callback_data.item_id}'))
    await callback.answer()
```

И если сформировать объект фабрики таким образом:
```
my_callback_data_1 = GoodsCallbackFactory(category_id=2,
                                          subcategory_id=0,
                                          item_id=0)
```

то callback будет таким:
```
print(my_callback_data_1.pack())       # goods|1|0|0
```

###### Особенности
1. Длина callback_data для инлайн-кнопок ограничена 64 байтами. Это не очень много, но в целом, достаточно для реализации многих задумок.
2. В качестве разделителя данных в callback_data, по умолчанию используется двоеточие. Но можно заменить на другой символ или группу символов. За это отвечает параметр sep. Имеет смысл менять разделитель в том случае, если у вас могут оказаться двоеточия в данных, из которых вы строите callback_data.
3. Если вы формируете инлайн-клавиатуру как список списков кнопок, то экземпляр класса вашей фабрики необходимо упаковать в строку с помощью метода pack().
4. Если вы формируете инлайн-клавиатуру с помощью билдера, добавляя аргументы в метод button() - упаковывать экземпляр класса фабрики в строку не надо.
5. Использование незащищенной фабрики коллбэков - это потенциальная угроза безопасности для вашего сервиса, потому что недобросовестные пользователи могут подменять `callback_data`, отправляя вашему боту запросы через сервера телеграм с данными кнопок, которые вы пользователю не отправляли.

#### Кнопка "Меню"
```
from aiogram.types import BotCommand

# Создаем асинхронную функцию
async def set_main_menu(bot: Bot):

# Создаем список с командами и их описанием для кнопки menu
main_menu_commands = [
        BotCommand(command='/help',
                   description='Справка по работе бота'),
        BotCommand(command='/support',
                   description='Поддержка'),
        BotCommand(command='/contacts',
                   description='Другие способы связи'),
        BotCommand(command='/payments',
                   description='Платежи')]

await bot.set_my_commands(main_menu_commands)

if __name__ == '__main__':
    # Регистрируем асинхронную функцию в диспетчере,
    # которая будет выполняться на старте бота
    dp.startup.register(set_main_menu)
```
###### Удаление кнопки "Меню"
```
await bot.delete_my_commands()
```

#### Форматирование сообщений
Telegram поддерживает три способа разметки текста:
1. HTML style
2. Markdown style(не поддерживается с 3ей версии aiogram)
3. MarkdownV2 style

###### HTML
>[!info] Параметр parse_mode указываем 1 раз при создании экземпляра Bot и/или в отдельном сообщении.

```
bot: Bot = Bot(BOT_TOKEN, parse_mode='HTML')
```

```
# Этот хэндлер будет срабатывать на команду "/html"
@dp.message(Command(commands='html'))
async def process_html_command(message: Message):
    await message.answer(
                text='Это текст, демонстрирующий '
                     'как работает HTML-разметка:\n\n'
                     '<b>Это жирный текст</b>\n'
                     '<i>Это наклонный текст</i>\n'
                     '<u>Это подчеркнутый текст</u>\n'
                     '<span class="tg-spoiler">А это спойлер</span>\n\n'
                     'Чтобы еще раз посмотреть список доступных команд - '
                     'отправь команду /help',
                parse_mode='HTML')
 
# Этот хэндлер будет срабатывать на команду "/markdownv2"
@dp.message(Command(commands='markdownv2'))
async def process_markdownv2_command(message: Message):
    await message.answer(
                text='Это текст, демонстрирующий '
                     'как работает MarkdownV2\-разметка:\n\n'
                     '*Это жирный текст*\n'
                     '_Это наклонный текст_\n'
                     '__Это подчеркнутый текст__\n'
                     '||А это спойлер||\n\n'
                     'Чтобы еще раз посмотреть список доступных команд \- '
                     'отправь команду /help',
                parse_mode='MarkdownV2')
```

###### MarkdownV2
```
*Жирный текст*
_Наклонный текст_
__Подчеркнутый текст__
~Перечеркнутый текст~
||Спойлер||
[Внешняя ссылка](https://stepik.org/120924)
[Внутренняя ссылка](tg://user?id=173901673)
`Моноширинный текст`
```
<code>
```
   Предварительно отформатированный текст
```
</code>

<code>
```python
Предварительно отформатированный блок кода на языке Python
```
</code>

Возможно комбинирование, например:
```
*_Жирный наклонный текст_*
```

>[!info] Символы при работе в режиме MarkdownV2, которые не должны восприниматься телеграмом как часть разметки, должны быть экранированы символом '\'

###### Полный список emoji
https://www.unicode.org/emoji/charts/full-emoji-list.html

#### Машина состояний(Конечный автомат)
>[!info] Подробнее https://stepik.org/lesson/759409/step/5?unit=761425

```
from aiogram.filters import StateFilter
from aiogram.filters.state import State, StatesGroup
from aiogram.fsm.context import FSMContext
from aiogram.fsm.state import default_state
from aiogram.fsm.storage.memory import MemoryStorage   # не рекомендуется использовать его в реальных проектах, т.к. MemoryStorage хранит все данные в оперативной памяти без сброса на диск.

# Инициализируем хранилище (создаем экземпляр класса MemoryStorage)
storage: MemoryStorage = MemoryStorage()

dp: Dispatcher = Dispatcher(storage=storage)

# Cоздаем класс, наследуемый от StatesGroup, для группы состояний нашей FSM
class FSMFillForm(StatesGroup):
    # Создаем экземпляры класса State, последовательно
    # перечисляя возможные состояния, в которых будет находиться
    # бот в разные моменты взаимодейтсвия с пользователем
    fill_name = State()        # Состояние ожидания ввода имени
    fill_age = State()         # Состояние ожидания ввода возраста
    fill_gender = State()      # Состояние ожидания выбора пола
    upload_photo = State()     # Состояние ожидания загрузки фото
    fill_education = State()   # Состояние ожидания выбора образования
    fill_wish_news = State()   # Состояние ожидания выбора получать ли новости

# Сбрасываем состояние
await state.clear()

# Устанавливаем состояние ожидания ввода имени
await state.set_state(FSMFillForm.fill_name)

# Этот хэндлер будет срабатывать, если введен корректный возраст
# и переводить в состояние выбора пола
@dp.message(StateFilter(FSMFillForm.fill_age), lambda x: x.text.isdigit() and 4 <= int(x.text) <= 120)
async def process_age_sent(message: Message, state: FSMContext):
    # Cохраняем возраст в хранилище по ключу "age"
    await state.update_data(age=message.text)

    # Создаем объекты инлайн-кнопок
    male_button = InlineKeyboardButton(text='Мужской ♂', callback_data='male')
    female_button = InlineKeyboardButton(text='Женский ♀', callback_data='female')
    undefined_button = InlineKeyboardButton(text='🤷 Пока не ясно',                     callback_data='undefined_gender')

    # Добавляем кнопки в клавиатуру (две в одном ряду и одну в другом)
    keyboard: list[list[InlineKeyboardButton]] = [[male_button, female_button], [undefined_button]]

    # Создаем объект инлайн-клавиатуры
    markup = InlineKeyboardMarkup(inline_keyboard=keyboard)

    # Отправляем пользователю сообщение с клавиатурой
    await message.answer(text='Спасибо!\n\nУкажите ваш пол', reply_markup=markup)

    # Устанавливаем состояние ожидания выбора пола
    await state.set_state(FSMFillForm.fill_gender)

# фильтр default_state
@dp.message(Command(commands='cancel'), StateFilter(default_state))
async def process_cancel_command(message: Message):
    print(await state.get_data())         # получить данные пользователя внутри машины состояний
    print(await state.get_state())        # текущее состояние, в котором находится пользователь
    await state.update_data(name=message.text)   # сохранение данных
    await state.finish()                         # полностью очищает storage
```

###### Стратегии FSM
- USER_IN_CHAT — стратегия по умолчанию. Стейт и данные разные у каждого юзера в каждом чате. То есть, у юзера будут разные состояния и данные в разных группах, а также в ЛС с ботом.
- CHAT — стейт и данные общие для всего чата целиком. В ЛС разница незаметна, но в группе у всех участников будет один стейт и общие данные.
- GLOBAL_USER — во всех чатах у одного и того же юзера будет один и тот же стейт и данные.

Применение стратегии:
```
dp = Dispatcher(storage=MemoryStorage(), fsm_strategy=FSMStrategy.CHAT)
```

#### Функция при запуске бота
```
async def on_startup():
    print('Bot started...')

dp.startup.register(on_startup)
```

#### Действия по расписанию
```
pip install aioschedule

import aioschedule

dp.startup.register(on_startup) 

async def noon_print():
    print("It's noon!")

async def scheduler():
    aioschedule.every().day.at("00:00").do(noon_print)
    while True:
        await aioschedule.run_pending()
        await asyncio.sleep(1)

async def on_startup():
    print('Bot started...')
    asyncio.create_task(scheduler())  
```

#### Middleware
Функции-прокладки между Приходящими апдейтами и хэндлерами.
С их помощью можно:
1) Логировать события
2) передавать в хэндлеры какие-то объекты (например, сессию базы данных из пула сессий);
3) подменять обработку апдейтов, не доводя до хэндлеров;
4) по-тихому пропускать апдейты, как будто их и не было;
5) и т.д.
```
REQUEST->Outer middleware->Filters->Middleware->RESPONSE
                          (хэндлер)
```

```
from typing import Callable, Dict, Any, Awaitable
from aiogram import BaseMiddleware
from aiogram.types import TelegramObject

class SomeMiddleware(BaseMiddleware):
    async def __call__(
        self,
        handler: Callable[[TelegramObject, Dict[str, Any]], Awaitable[Any]],  # имеет смысл только для inner middleware
        event: TelegramObject,     # это может быть Update, Message, CallbackQuery или InlineQuery
        data: Dict[str, Any]       # например данные FSM, поля из фильтров, флаги и др.
       ) -> Any:
           print("Before handler")
           result = await handler(event, data)
           print("After handler")
           return result
 
# Если Вам не нужно получать данные, return await handler(event, data), если этого не делать(неявный return None),
#  апдейт будет считаться дропнутым
```

БАН пользователя, чтобы он не мог писать боту
Скорее всего, лучшим местом для этого будет outer-мидлварь на Update, как самый ранний этап обработки запроса юзера.
Более того, одна из встроенных в aiogram мидлварей кладёт в data словарик с информацией о пользователе по ключу event_from_user. Далее вы можете достать оттуда ID юзера, сравнить с каким-нибудь своим «списком заблокированных» и просто сделать return, чтобы предотвратить дальнейшую обработку по цепочке.
    
Пример outer middleware, реагирующей на колбеки.

```
# Это будет outer-мидлварь на любые колбэки
class WeekendCallbackMiddleware(BaseMiddleware):
    async def __call__(
        self,
        handler: Callable[[CallbackQuery, Dict[str, Any]], Awaitable[Any]],
        event: CallbackQuery,
        data: Dict[str, Any]
        ) -> Any:
        # Если сегодня не суббота и не воскресенье, то продолжаем обработку.
        if not _is_weekend():                  # функция, определяющая, выходные или нет
            return await handler(event, data)
        # В противном случае отвечаем на колбэк самостоятельно и прекращаем дальнейшую обработку
        await event.answer(
            "Бот по выходным не работает!",
            show_alert=True
            )
        return
```

Пример подключения middleware:
1) inner-мидлварь на сообщения:
```
router = Router()
router.message.filter(F.chat.type == "private")
router.message.middleware(WeekendMessageMiddleware())  
``` 

2) outer-мидлварь на любые колбэки
```
dp.callback_query.outer_middleware(WeekendCallbackMiddleware())
```

#### Флаги
>[!info] С помощью флагов можно пометить хэндлеры, не влезая в их внутреннюю структуру, чтобы затем что-то сделать в мидлварях, например, троттлинг.

Пример мидлвари с флагом:
```
from aiogram.dispatcher.flags import get_flag
from aiogram.utils.chat_action import ChatActionSender

class ChatActionMiddleware(BaseMiddleware):
    async def __call__(
        self,
        handler: Callable[[Message, Dict[str, Any]], Awaitable[Any]],
        event: Message,
        data: Dict[str, Any]
        ) -> Any:
              long_operation_type = get_flag(data, "long_operation")

    # Если такого флага на хэндлере нет
    if not long_operation_type:
        return await handler(event, data)

    # Если флаг есть
    async with ChatActionSender(
        action=long_operation_type, 
        chat_id=event.chat.id
        ):
        return await handler(event, data)
```

И обозначение хэндлеров флагами:
```
@dp.message(<тут ваши фильтры>, flags={"long_operation": "upload_video_note"})
```
