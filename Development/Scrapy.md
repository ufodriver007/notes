[[Парсинг]]
```
pip install Scrapy
```

###### Решение проблемы ==ModuleNotFoundError: No module named '_lzma'==
1. Установите пакет в систему
```
sudo apt-get install liblzma-dev
```

2. Установите пакет в ваш проект Python
```
pip install backports.lzma
```

3. Имените файл 'lzma.py'
```
sudo nano /usr/local/lib/python3.12/lzma.py
```
В нём надо заменить строки
```
from _lzma import *
from _lzma import _encode_filter_properties, _decode_filter_properties
```
На эти
```
from backports.lzma import *
from backports.lzma import _encode_filter_properties, _decode_filter_properties
```

###### Простейший пример
```
import scrapy  
  
class QuotesSpider(scrapy.Spider):  
    name = "quotes"  
    start_urls = [  
        "https://quotes.toscrape.com/tag/humor/",  
    ]  
  
    def parse(self, response):  
        for quote in response.css("div.quote"):  
            yield {  
                "author": quote.xpath("span/small/text()").get(),  
                "text": quote.css("span.text::text").get(),  
            }  
  
        next_page = response.css('li.next a::attr("href")').get()  
        if next_page is not None:  
            yield response.follow(next_page, self.parse)
```

Запустить можно так
```
scrapy runspider main.py -o quotes.json
```
В `quotes.json` паук соберёт все результаты

###### Создание проекта
```
scrapy startproject <project_name>
```
После этого фреймворк сгенерирует шаблон проекта
```
├── scrapy.cfg
└── bookscraper
    ├── __init__.py
    ├── items.py
    ├── middlewares.py
    ├── pipelines.py
    ├── settings.py
    └── spiders
        └── __init__.py
```

###### Элементы проекта
- **settings.py** — это место, где хранятся все настройки вашего проекта, такие как активация конвейеров, промежуточного программного обеспечения и т. д. Здесь вы можете изменить задержки, параллелизм и многое другое.
- **items.py** — это модель для извлеченных данных. Вы можете определить пользовательскую модель (например, ProductItem), которая унаследует класс Scrapy Item и будет содержать ваши извлеченные данные.
- **pipelines.py** — это то, куда передается элемент, выданный пауком, он в основном используется для очистки текста и подключения к выходным файлам или базам данных (CSV, JSON SQL и т. д.).
- **middlewares.py** полезен, когда вы хотите изменить способ выполнения запроса, а Scrapy обрабатывает ответ.
- **scrapy.cfg** — это файл конфигурации для изменения некоторых параметров развертывания и т. д.

###### Spiders
В вашем проекте может быть несколько «пауков», которые будут парсить одни и те же или разные веб-сайты и сохранять данные в разных местах.

Все, что вы могли бы сделать с помощью **Python Requests/BeautifulSoup scraper,** можно сделать и с помощью **Scrapy Spider** .

Пример паука
```
import scrapy

class BooksSpider(scrapy.Spider):
    name = 'books'

    def start_requests(self):
        url = 'https://books.toscrape.com/catalogue/a-light-in-the-attic_1000/index.html'
        yield scrapy.Request(url, callback=self.parse)

    def parse(self, response):
        item = {}
        product = response.css("div.product_main")
        item["title"] = product.css("h1 ::text").extract_first()
        item['category'] = response.xpath(
            "//ul[@class='breadcrumb']/li[@class='active']/preceding-sibling::li[1]/a/text()"
        ).extract_first()
        item['description'] = response.xpath(
            "//div[@id='product_description']/following-sibling::p/text()"
        ).extract_first()
        item['price'] = response.css('p.price_color ::text').extract_first()
        yield item
```

Чтобы запустить его
```
scrapy crawl books
```

Несколько моментов
1. **Асинхронность** - поскольку Scrapy создан с использованием фреймворка Twisted, когда вы отправляете запрос на веб-сайт, он не блокируется. Scrapy отправит запрос на веб-сайт, и как только получит успешный ответ, он запустит `parse`метод, используя обратный вызов, определенный в исходном запросе Scrapy `yield scrapy.Request(url, callback=self.parse)`.
2. **Имя паука** - Каждый паук в вашем проекте Scrapy должен иметь уникальное имя, чтобы Scrapy мог его идентифицировать. Вы задаете это с помощью `name = 'books'`атрибута.
3. **Запросы на запуск** - Вы определяете начальные точки для своего паука, используя `start_requests()`метод. Последующие запросы могут быть сгенерированы последовательно из этих начальных запросов.
4. **Parse** - Вы используете `parse()`метод для обработки ответа с веб-сайта и извлечения необходимых вам данных. После извлечения эти данные отправляются в Item Pipelines с помощью `yield`команды.

###### Scrapy Items
Предназначены для хранения и обработки данных. Похоже на модели в `Django`.
```
# items.py

from scrapy.item import Item, Field

class BookItem(Item):
    title = Field()
    category = Field()
    description = Field()
    price = Field()
```

Не забываем импортировать в файле паука наш `Item`
Внутри вашего паука, вместо того, чтобы выдавать словарь, вы создаете новый элемент с извлеченными данными, прежде чем выдавать их.
```
import scrapy
from bookscraper.items import BookItem   

class BooksSpider(scrapy.Spider):
    name = 'books'

    def start_requests(self):
        url = 'https://books.toscrape.com/catalogue/a-light-in-the-attic_1000/index.html'
        yield scrapy.Request(url, callback=self.parse)

    def parse(self, response):
        book_item = BookItem()
    product = response.css("div.product_main")
    book_item["title"] = product.css("h1 ::text").extract_first()
    book_item['category'] = response.xpath(
        "//ul[@class='breadcrumb']/li[@class='active']/preceding-sibling::li[1]/a/text()"
    ).extract_first()
    book_item['description'] = response.xpath(
        "//div[@id='product_description']/following-sibling::p/text()"
    ).extract_first()
    book_item['price'] = response.css('p.price_color ::text').extract_first()
    yield book_item
```

###### Scrapy Item Pipelines(Конвейеры)
Это процессоры данных, через которые проходят все собранные нами элементы, и где мы можем очищать, обрабатывать, проверять и хранить наши данные.

Используя Scrapy Pipelines мы можем:
- Очистить наши данные (например, удалить знаки валют из цен)
- Форматировать наши данные (например, преобразовать строки в целые числа)
- Обогатить наши данные (например, преобразовать относительные ссылки в абсолютные)
- Подтвердить наши данные (например, убедитесь, что полученная цена является реальной)
- Хранить наши данные в базах данных, очередях, файлах или хранилищах объектов.

Например, вот конвейер элементов, который сохраняет наши данные в базе данных Postgres
```
# pipelines.py

import psycopg2

class PostgresDemoPipeline:

    def __init__(self):
        ## Connection Details
        hostname = 'localhost'
        username = 'postgres'
        password = '******' # your password
        database = 'quotes'

        ## Create/Connect to database
        self.connection = psycopg2.connect(host=hostname, user=username, password=password, dbname=database)
        
        ## Create cursor, used to execute commands
        self.cur = self.connection.cursor()
        
        ## Create quotes table if none exists
        self.cur.execute("""
        CREATE TABLE IF NOT EXISTS quotes(
            id serial PRIMARY KEY, 
            title text,
            category text,
            description VARCHAR(255)
        )
        """)

    def process_item(self, item, spider):
        ## Define insert statement
        self.cur.execute(""" insert into books (title, category, description) values (%s,%s,%s)""", (
            item["title"],
            str(item["category"]),
            item["description"]
        ))

        ## Execute insert of data into database
        self.connection.commit()
        return item

    def close_spider(self, spider):
        ## Close cursor & connection to database 
        self.cur.close()
        self.connection.close()

```

Пример конвейера для:
- удаления пробелов(сначала и с конца) во всех полях объекта, кроме поля `description`;
-  преобразовать 2 поля в нижний регистр; убрать символ функта и привести цену к типу `float`;
- извлечь число доступных книг на складе из фразы;
- преобразовать количество отзывов в целое число
```
from itemadapter import ItemAdapter

class BookscraperPipeline:

    def process_item(self, item, spider):
        adapter = ItemAdapter(item)

        ## Strip all whitespaces from strings
        field_names = adapter.field_names()
        for field_name in field_names:
            if field_name != 'description':
                value = adapter.get(field_name)
                adapter[field_name] = value.strip()
                
        ## Category & Product Type --> switch to lowercase  
        lowercase_keys = ['category', 'product_type']  
        for lowercase_key in lowercase_keys:  
            value = adapter.get(lowercase_key)  
            adapter[lowercase_key] = value.lower()

        ## Price --> convert to float  
        price_keys = ['price', 'price_excl_tax', 'price_incl_tax', 'tax']  
        for price_key in price_keys:  
            value = adapter.get(price_key)  
            value = value.replace('£', '')  
            adapter[price_key] = float(value)

        ## Availability --> extract number of books in stock  
        availability_string = adapter.get('availability')  
        split_string_array = availability_string.split('(')  
        if len(split_string_array) < 2:  
            adapter['availability'] = 0  
        else:  
            availability_array = split_string_array[1].split(' ')  
            adapter['availability'] = int(availability_array[0])

        ## Reviews --> convert string to number  
        num_reviews_string = adapter.get('num_reviews')  
        adapter['num_reviews'] = int(num_reviews_string)

        return item
```

Чтобы активировать конвейер элементов, нам просто нужно добавить следующий код в наш `settings.py`файл:
```
## settings.py

ITEM_PIPELINES = {
   'bookscraper.pipelines.BookscraperPipeline': 300,  # 300 - это значение порядка. Здесь работает
}                                                     # 0-1000. Более низкие сначала обрабатывются
```

###### Scrapy Middlewares
Большая часть функциональности парсинга содержится в `Middlewares`. 

- ==Downloader Middlewares==
Это специальные хуки, которые располагаются между `Scrapy Engine` и `Downloader` и обрабатывают запросы по мере их передачи от Engine к Downloader, а также ответы по мере их передачи от `Downloader` к `Engine`.

По умолчанию в Scrapy включены следующие `Downloader Middlewares`
```
# settings.py

DOWNLOADER_MIDDLEWARES_BASE = {
    # Engine side
    'scrapy.downloadermiddlewares.robotstxt.RobotsTxtMiddleware': 100,
    'scrapy.downloadermiddlewares.httpauth.HttpAuthMiddleware': 300,
    'scrapy.downloadermiddlewares.downloadtimeout.DownloadTimeoutMiddleware': 350,
    'scrapy.downloadermiddlewares.defaultheaders.DefaultHeadersMiddleware': 400,
    'scrapy.downloadermiddlewares.useragent.UserAgentMiddleware': 500,
    'scrapy.downloadermiddlewares.retry.RetryMiddleware': 550,
    'scrapy.downloadermiddlewares.ajaxcrawl.AjaxCrawlMiddleware': 560,
    'scrapy.downloadermiddlewares.redirect.MetaRefreshMiddleware': 580,
    'scrapy.downloadermiddlewares.httpcompression.HttpCompressionMiddleware': 590,
    'scrapy.downloadermiddlewares.redirect.RedirectMiddleware': 600,
    'scrapy.downloadermiddlewares.cookies.CookiesMiddleware': 700,
    'scrapy.downloadermiddlewares.httpproxy.HttpProxyMiddleware': 750,
    'scrapy.downloadermiddlewares.stats.DownloaderStats': 850,
    'scrapy.downloadermiddlewares.httpcache.HttpCacheMiddleware': 900,
    # Downloader side
}
```

Эти `Middlewares` контролируют все:

- Запросы на тайм-аут
- Какие заголовки отправлять с вашими запросами
- Какие пользовательские агенты использовать в ваших запросах
- Повтор неудачных запросов
- Управление файлами cookie, кэшами и сжатием ответов

Вы можете отключить любой из этих промежуточных программ по умолчанию, установив его `none`в своем `settings.py`файле. Вот пример отключения **RobotsTxtMiddleware** .
```
# settings.py

DOWNLOADER_MIDDLEWARES = {
    'scrapy.downloadermiddlewares.robotstxt.RobotsTxtMiddleware': None,
}
```

Вы также можете переопределить существующие промежуточные программы или вставить свои собственные совершенно новые промежуточные программы, если хотите:
- изменить запрос непосредственно перед его отправкой на сайт (изменить прокси, user-agent и т. д.)
- изменить полученный ответ перед передачей его пауку
- повторить запрос, если ответ не содержит правильных данных, вместо того, чтобы передать полученный ответ пауку
- передать ответ пауку без загрузки веб-страницы
- молча отбрасывать некоторые запросы

Вот пример вставки нашего собственного промежуточного ПО для использования прокси со всеми вашими запросами. Мы создадим это в нашем `middlewares.py`файле
```
## middlewares.py

import base64

class MyProxyMiddleware(object):

    @classmethod
    def from_crawler(cls, crawler):
        return cls(crawler.settings)

    def __init__(self, settings):
        self.user = settings.get('PROXY_USER')
        self.password = settings.get('PROXY_PASSWORD')
        self.endpoint = settings.get('PROXY_ENDPOINT')
        self.port = settings.get('PROXY_PORT')

    def process_request(self, request, spider):
        user_credentials = '{user}:{passw}'.format(user=self.user, passw=self.password)
        basic_authentication = 'Basic ' + base64.b64encode(user_credentials.encode()).decode()
        host = 'http://{endpoint}:{port}'.format(endpoint=self.endpoint, port=self.port)
        request.meta['proxy'] = host
        request.headers['Proxy-Authorization'] = basic_authentication
```

Мы включим его в вашем `settings.py`файле и укажем данные вашего прокси-подключения
```
## settings.py

PROXY_USER = 'username'
PROXY_PASSWORD = 'password'
PROXY_ENDPOINT = 'proxy.proxyprovider.com'
PROXY_PORT = '8000'

DOWNLOADER_MIDDLEWARES = { 
    'myproject.middlewares.MyProxyMiddleware': 350, 
}
```

- ==Spider Middlewares==
Представляет собой специальные хуки, которые располагаются между `Scrapy Engine` и `Spiders` и обрабатывают входные данные (ответы) и выходные данные (элементы и запросы) `Spider.`
По умолчанию в Scrapy включены следующие `Middlewares`
```
# settings.py

SPIDER_MIDDLEWARES_BASE = {
    # Engine side
    'scrapy.spidermiddlewares.httperror.HttpErrorMiddleware': 50,
    'scrapy.spidermiddlewares.offsite.OffsiteMiddleware': 500,
    'scrapy.spidermiddlewares.referer.RefererMiddleware': 700,
    'scrapy.spidermiddlewares.urllength.UrlLengthMiddleware': 800,
    'scrapy.spidermiddlewares.depth.DepthMiddleware': 900,
    # Spider side
}
```

Spider Middlewares используется для:
- постобработка вывода обратных вызовов паука - изменение/добавление/удаление запросов или элементов
- пост-обработка `start_requests`
- обрабатывать исключения паука
- вызов `errback` вместо `callback` для некоторых запросов на основе содержимого ответа

Пример отключения **RobotsTxtMiddleware** .
```
# settings.py

SPIDER_MIDDLEWARES = {
    'scrapy.spidermiddlewares.referer.RefererMiddleware': None,
}
```

###### Scrapy Settings
Файл `settings.py` - панель управления для вашего проекта Scrapy. Вы можете включить/отключить функциональность по умолчанию или интегрировать собственные промежуточные программы и расширения.

Вы можете изменить настройки для всего проекта, обновив `settings.py`файл, или для каждого паука отдельно, добавив `custom_settings`к каждому файлу `spider`.

Вот пример добавления настройки в паука, чтобы он сохранял извлеченные данные в `data.csv`
```
class BooksSpider(scrapy.Spider):  
	name = 'books'  
	custom_settings = {  
		'FEEDS': { 'data.csv': { 'format': 'csv',}}  
		}
	...
```

###### Создание паука
Scrapy предоставляет несколько различных типов пауков. Вот некоторые из наиболее распространенных:
- **Spider —** берет список start_url и извлекает каждый из них с помощью `parse`метода.
- **CrawlSpider —** разработан для полного сканирования веб-сайта путем перехода по всем найденным ссылкам.
- **SitemapSpider —** разработан для извлечения URL-адресов из карты сайта.

Чтобы создать нового универсального паука, просто выполните команду **genspider** :
```
# syntax is --> scrapy genspider <name_of_spider> <website> 
$ scrapy genspider bookspider books.toscrape.com
```

Пример **genspider**
```
import scrapy

class BookspiderSpider(scrapy.Spider):
    name = 'bookspider'
    allowed_domains = ['books.toscrape.com']
    start_urls = ['https://books.toscrape.com/']

    def parse(self, response):
        pass
```

- **name** - атрибут класса, который дает имя пауку. Мы будем использовать его при запуске нашего паука позже `scrapy crawl <spider_name>`.
- **allowed_domains** — атрибут класса, который сообщает Scrapy, что он должен скрапить только страницы домена `books.toscrape.com`. Это не позволяет пауку скрапить множество веб-сайтов.
- **start_urls** — атрибут класса, который сообщает Scrapy первый URL, который он должен посетить.
- **parse** - `parse`функция вызывается после получения ответа от целевого веб-сайта.

>[!info] Чтобы начать использовать этот Spider, нам просто нужно начать вставлять наш код синтаксического анализа в `parse`функцию.

Когда будет готов паук, можно будет запустить его
```
scrapy crawl bookspider
```

Если мы хотим сохранить данные в файл JSON, мы можем использовать `-O`опцию, за которой следует именем файла.

```
scrapy crawl bookspider -O myscrapeddata.json
```
 
Пример паука собирающего информацию на нескольких страницах
```
import scrapy

class BookspiderSpider(scrapy.Spider):
    name = 'bookspider'
    allowed_domains = ['books.toscrape.com']
    start_urls = ['https://books.toscrape.com/']

    def parse(self, response):
        books = response.css('article.product_pod')
        for book in books:
            yield{
                'name' : book.css('h3 a::text').get(),
                'price' : book.css('div.product_price .price_color::text').get(),
                'url' : book.css('h3 a').attrib['href'],
            }
        
        next_page = response.css('li.next a ::attr(href)').get()
        if next_page is not None:
            if 'catalogue/' in next_page:
                next_page_url = 'https://books.toscrape.com/' + next_page
            else:
                next_page_url = 'https://books.toscrape.com/catalogue/' + next_page
            yield response.follow(next_page_url, callback=self.parse)
```

###### Scrapy shell
```
scrapy shell
```

```
fetch('https://books.toscrape.com/')
```

```
response.css('article.product_pod')
```

###### Сохранение данных в файл
- Через аргументы `shell`
- С помощью настроек в `settings.py`

==Различие JSON и JSON lines==
JSON
```
[  
	{"name": "Color TV", "price": "1200"},  
	{"name": "DVD player", "price": "200"}  
]
```
JSON lines
```
{"name": "Color TV", "price": "1200"}  
{"name": "DVD player", "price": "200"}
```

1. Если мы хотим сохранить данные в файл JSON, мы можем использовать `-O`опцию, за которой следует именем файла.

```
scrapy crawl bookspider -o bookspider_data.json
 ИЛИ
scrapy crawl bookspider -o bookspider_data.jsonl  # json lines
```

Или сохранить в csv файл
```
scrapy crawl bookspider -O myscrapeddata.csv
```

При использовании этой команды у вас есть два варианта: использовать маленькие `-o`или заглавные буквы `-O`. 
-> Маленькая `o` Добавляет новые данные к существующему файлу.
-> Большая `O` Заменяет любой существующий файл с тем же именем текущими данными.

2. Настройки `settings.py`
```
# settings.py 

FEEDS = {
    'data.json': {'format': 'json'}  # ИЛИ 'data.jsonl': {'format': 'jsonlines'}
}
```
[Полный список настроек](https://docs.scrapy.org/en/stable/topics/feed-exports.html#feeds)

Также это можно настроить в  каждом отдельном пауке, установив `custom_setting`в файле spider
```
# bookspider.py 

import scrapy
from proxy_waterfall.items import BookItem

class BookSpider(scrapy.Spider):
    name = 'bookspider'
    start_urls = ["http://books.toscrape.com"]

    custom_settings = {
        'FEEDS': { 'data.jsonl': { 'format': 'jsonlines', 'overwrite': True}}
        }
        
    def parse(self, response):

        for article in response.css('article.product_pod'):
            book_item = BookItem(
                url = article.css("h3 > a::attr(href)").get(),
                title = article.css("h3 > a::attr(title)").extract_first(),
                price = article.css(".price_color::text").extract_first(),
            )
            yield book_item
```

###### Сохранение данных в БД
1. Устанавливаем драйвер БД
```
pip install psycopg2
```

2. Настраиваем конвейер
```
# pipelines.py

import psycopg2

class SaveToPostgresPipeline:

    def __init__(self):
        ## Connection Details
        hostname = 'localhost'
        username = 'postgres'
        password = '******' # your password
        database = 'books'

        ## Create/Connect to database
        self.connection = psycopg2.connect(host=hostname, user=username, password=password, dbname=database)
        
        ## Create cursor, used to execute commands
        self.cur = self.connection.cursor()
        
        ## Create books table if none exists
        self.cur.execute("""
        CREATE TABLE IF NOT EXISTS books(
            id serial PRIMARY KEY, 
            url VARCHAR(255),
            title text,
            upc VARCHAR(255),
            product_type VARCHAR(255),
            price_excl_tax DECIMAL,
            price_incl_tax DECIMAL,
            tax DECIMAL,
            price DECIMAL,
            availability INTEGER,
            num_reviews INTEGER,
            stars INTEGER,
            category VARCHAR(255),
            description text
        )
        """)

    def process_item(self, item, spider):

        ## Define insert statement
        self.cur.execute(""" insert into books (
            url, 
            title, 
            upc, 
            product_type, 
            price_excl_tax,
            price_incl_tax,
            tax,
            price,
            availability,
            num_reviews,
            stars,
            category,
            description
            ) values (
                %s,
                %s,
                %s,
                %s,
                %s,
                %s,
                %s,
                %s,
                %s,
                %s,
                %s,
                %s,
                %s
                )""", (
            item["url"],
            item["title"],
            item["upc"],
            item["product_type"],
            item["price_excl_tax"],
            item["price_incl_tax"],
            item["tax"],
            item["price"],
            item["availability"],
            item["num_reviews"],
            item["stars"],
            item["category"],
            str(item["description"])
        ))

        ## Execute insert of data into database
        self.connection.commit()
        return item

    def close_spider(self, spider):

        ## Close cursor & connection to database 
        self.cur.close()
        self.connection.close()
```

3. Активируем конвейер. Для этого включаем его в `settings.py`
```
# settings.py

ITEM_PIPELINES = {
   'bookscraper.pipelines.SaveToPostgresPipeline': 300,
}
```

###### Блокировка во время парсинга
Веб-сайты используют ряд методов для обнаружения и запрета парсерам извлекать свои данные:
- **IP-адрес**
- **Fingerprint TLS** или **TCP/IP**
- **Заголовки HTTP** (значения, порядок и используемые случаи)
- **Браузерные Fingerprint**
- **Файлы cookie/Сеансы**

###### Установка поддельного User-Agent
1. Можно установить `User-Agent` в `settings.py`
```
## settings.py

USER_AGENT = 'Mozilla/5.0 (iPad; CPU OS 12_2 like Mac OS X) AppleWebKit/605.1.15 (KHTML, like Gecko) Mobile/15E148'
```

2.  Задать user-agent для каждого запроса, который делает ваш паук, определив user-agent в заголовках вашего запроса.
```
## myspider.py

def start_requests(self):
    for url in self.start_urls:
        return Request(url=url, callback=self.parse,
                       headers={"User-Agent": "Mozilla/5.0 (iPad; CPU OS 12_2 like Mac OS X) AppleWebKit/605.1.15 (KHTML, like Gecko) Mobile/15E148"})
```

Пример ротации данных `User-Agent`
```
## myspider.py

import random

user_agent_list = [
    'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/93.0.4577.82 Safari/537.36',
    'Mozilla/5.0 (iPhone; CPU iPhone OS 14_4_2 like Mac OS X) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/14.0.3 Mobile/15E148 Safari/604.1',
    'Mozilla/4.0 (compatible; MSIE 9.0; Windows NT 6.1)',
    'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/87.0.4280.141 Safari/537.36 Edg/87.0.664.75',
    'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/70.0.3538.102 Safari/537.36 Edge/18.18363',
]

def start_requests(self):
    for url in self.start_urls:
        return Request(url=url, callback=self.parse,
                       headers={"User-Agent": user_agent_list[random.randint(0, len(user_agent_list)-1)]})
```

###### [ScrapeOps Fake User-Agent API](https://scrapeops.io/docs/fake-user-agent-headers-api/fake-user-agents/)
1. Регистрируем учётную запись [здесь](https://scrapeops.io/app/register/headers)
2. Шлём GET-запрос
```
http://headers.scrapeops.io/v1/user-agents?api_key=YOUR_API_KEY
```
Пример ответа
```
{
  "result": [
    "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/13.0.5 Safari/605.1.15",
    "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/103.0.5060.53 Safari/537.36",
    "Mozilla/5.0 (Windows NT 10.0; Windows; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/103.0.5060.114 Safari/537.36",
    "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_10_5) AppleWebKit/603.3.8 (KHTML, like Gecko) Version/10.1.2 Safari/603.3.8",
    "Mozilla/5.0 (Windows NT 10.0; Windows; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/103.0.5060.114 Safari/537.36",
    "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/14.0 Safari/605.1.15",
    "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/103.0.5060.53 Safari/537.36",
    "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_6) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/14.0 Safari/605.1.15",
    "Mozilla/5.0 (Windows NT 10.0; Windows; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/103.0.5060.114 Safari/537.36",
    "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/103.0.5060.53 Safari/537.36"
  ]
}
```

Лучший способ интегрции - создать Middleware
```
## middlewares.py

from urllib.parse import urlencode
from random import randint
import requests

class ScrapeOpsFakeUserAgentMiddleware:

    @classmethod
    def from_crawler(cls, crawler):
        return cls(crawler.settings)

    def __init__(self, settings):
        self.scrapeops_api_key = settings.get('SCRAPEOPS_API_KEY')
        self.scrapeops_endpoint = settings.get('SCRAPEOPS_FAKE_USER_AGENT_ENDPOINT', 'http://headers.scrapeops.io/v1/user-agents?') 
        self.scrapeops_fake_user_agents_active = settings.get('SCRAPEOPS_FAKE_USER_AGENT_ENABLED', False)
        self.scrapeops_num_results = settings.get('SCRAPEOPS_NUM_RESULTS')
        self.headers_list = []
        self._get_user_agents_list()
        self._scrapeops_fake_user_agents_enabled()

    def _get_user_agents_list(self):
        payload = {'api_key': self.scrapeops_api_key}
        if self.scrapeops_num_results is not None:
            payload['num_results'] = self.scrapeops_num_results
        response = requests.get(self.scrapeops_endpoint, params=urlencode(payload))
        json_response = response.json()
        self.user_agents_list = json_response.get('result', [])

    def _get_random_user_agent(self):
        random_index = randint(0, len(self.user_agents_list) - 1)
        return self.user_agents_list[random_index]

    def _scrapeops_fake_user_agents_enabled(self):
        if self.scrapeops_api_key is None or self.scrapeops_api_key == '' or self.scrapeops_fake_user_agents_active == False:
            self.scrapeops_fake_user_agents_active = False
        else:
            self.scrapeops_fake_user_agents_active = True
    
    def process_request(self, request, spider):        
        random_user_agent = self._get_random_user_agent()
        request.headers['User-Agent'] = random_user_agent
```
А затем включим его в `settings.py`файле нашего проекта
```
## settings.py

SCRAPEOPS_API_KEY = 'YOUR_API_KEY'
SCRAPEOPS_FAKE_USER_AGENT_ENDPOINT = True

DOWNLOADER_MIDDLEWARES = {
    'bookscraper.middlewares.ScrapeOpsFakeUserAgentMiddleware': 400,
}
```

######  [Fake Browser Header API](https://scrapeops.io/docs/fake-user-agent-headers-api/fake-browser-headers/)
1. Регистрируем учётную запись [здесь](https://scrapeops.io/app/register/headers)
2. Шлём GET-запрос
```
http://headers.scrapeops.io/v1/browser-headers?api_key=YOUR_API_KEY
```

Лучший способ интегрции - опять таки создать Middleware
```
## middlewares.py

from urllib.parse import urlencode
from random import randint
import requests

class ScrapeOpsFakeBrowserHeaderAgentMiddleware:

    @classmethod
    def from_crawler(cls, crawler):
        return cls(crawler.settings)

    def __init__(self, settings):
        self.scrapeops_api_key = settings.get('SCRAPEOPS_API_KEY')
        self.scrapeops_endpoint = settings.get('SCRAPEOPS_FAKE_BROWSER_HEADER_ENDPOINT', 'http://headers.scrapeops.io/v1/browser-headers?') 
        self.scrapeops_fake_browser_headers_active = settings.get('SCRAPEOPS_FAKE_BROWSER_HEADER_ENABLED', False)
        self.scrapeops_num_results = settings.get('SCRAPEOPS_NUM_RESULTS')
        self.headers_list = []
        self._get_headers_list()
        self._scrapeops_fake_browser_headers_enabled()

    def _get_headers_list(self):
        payload = {'api_key': self.scrapeops_api_key}
        if self.scrapeops_num_results is not None:
            payload['num_results'] = self.scrapeops_num_results
        response = requests.get(self.scrapeops_endpoint, params=urlencode(payload))
        json_response = response.json()
        self.headers_list = json_response.get('result', [])

    def _get_random_browser_header(self):
        random_index = randint(0, len(self.headers_list) - 1)
        return self.headers_list[random_index]

    def _scrapeops_fake_browser_headers_enabled(self):
        if self.scrapeops_api_key is None or self.scrapeops_api_key == '' or self.scrapeops_fake_browser_headers_active == False:
            self.scrapeops_fake_browser_headers_active = False
        else:
            self.scrapeops_fake_browser_headers_active = True
    
    def process_request(self, request, spider):        
        random_browser_header = self._get_random_browser_header()
        request.headers = random_browser_header
```
А затем включим его в `settings.py`файле нашего проекта
```
## settings.py

SCRAPEOPS_API_KEY = 'YOUR_API_KEY'
SCRAPEOPS_FAKE_BROWSER_HEADER_ENABLED = True

DOWNLOADER_MIDDLEWARES = {
    'bookscraper.middlewares.ScrapeOpsFakeBrowserHeaderAgentMiddleware': 400,
}
```

###### Использование Proxy
Бесплатные прокси
- [FreeProxyLists](https://www.freeproxylists.net/)
- [Списки бесплатных прокси-серверов Geonode](https://geonode.com/free-proxy-list)
- [ProxyNova](https://www.proxynova.com/proxy-server-list/)

Существует несколько бесплатных Middleware для Scrapy
[scrapy-proxyes](https://github.com/aivarsk/scrapy-proxies)
[scrapy-rotating-proxyes,](https://github.com/TeamHG-Memex/scrapy-rotating-proxies)

Пример с [scrapy-rotating-proxyes](https://github.com/TeamHG-Memex/scrapy-rotating-proxies)
```
pip install scrapy-rotating-proxies
```

Подключаем в настройках
```
## settings.py

## Insert Your List of Proxies Here
ROTATING_PROXY_LIST = [
    'proxy1.com:8000',
    'proxy2.com:8031',
    'proxy3.com:8032',
]

## Enable The Proxy Middleware In Your Downloader Middlewares
DOWNLOADER_MIDDLEWARES = {
    # ...
    'rotating_proxies.middlewares.RotatingProxyMiddleware': 610,
    'rotating_proxies.middlewares.BanDetectionMiddleware': 620,
    # ...
}
```
Всё. В качестве альтернативы можно указать не список с прокями, а файл
```
ROTATING_PROXY_LIST_PATH = '/my/path/proxies.txt'
```

>[!info] **scrapy-rotating-proxyes** будет активно отслеживать работоспособность каждого отдельного прокси-сервера и удалять любые неработающие прокси-серверы из ротации прокси-серверов.

###### SmartProxy
Единая конечная точка прокси, на которую вы отправляете свои запросы, а они занимаются выбором и ротацией прокси на своей стороне. Это значительно упрощает для вас интеграцию решения прокси в вашего паука.
```
"http://username:password@gate.smartproxy.com:7000"
```
При работе с такими прокси НЕ следует использовать `Middleware` для rotation  proxy
Просто подключаем такие прокси в пауке
```
## your_spider.py

def start_requests(self):
    for url in self.start_urls:
        return Request(url=url, callback=self.parse,
                       meta={"proxy": "http://username:password@gate.smartproxy.com:7000"})
```