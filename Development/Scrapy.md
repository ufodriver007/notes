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


