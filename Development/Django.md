![[Pasted image 20231219081151.png]]
![[2023-12-19_08-37.png]]
```
pip install django
```
```
django-admin startproject my_first_site                      # создать шаблон проекта
```
```
python3 maanage.py runserver                                 # запустить проект 127.0.0.1:8000
```
```
django-admin startapp <app_name>                             # создаём приложение
 # затем регистрируем в settings.py в списке INSALLED_APPS как '<app_name>.apps.app_class'
```
>[!tip] Чтобы обновить  страницу в браузере с перезагрузкой кэша(например если Вы правите css) существует сочетание `CTRL-F5`

>[!info] WSGI — это стандарт взаимодействия между Python-скриптом и веб-сервером. Например Gunicorn — это веб-сервер с поддержкой стандарта WSGI. ASGI - это просто асинхронный тип стандарта.
#### Файлы проекта
|Файл|Описание|
|-----|----------|
|`manage.py`|Скрипт, который используется в процессе разработки для выполнения различных действий над проектом
|`wsgi.py`|Точка входа в WSGI-приложение
|`asgi.py`|Точка входа в ASGI-приложение
|`settings.py`|Модуль, который содержит все настройки проекта
|`views.py`|Файл контроллера в django
|`urls.py`|Файл привязок(роутер). при обрашении к определённому url запускается views(), ссылаясь на views.py

###### include
В `urls.py` в `urlpatterns` можно вписать `include` и в приложении (например `articles`) в `urls.py` будет своё пространство маршрутов.
Т.е. запрашивая `/articles` используется маршрут из `articles/urls.py`(который `path('', views.index)`), а запрашивая `/articles/new` используется маршрут из `articles/urls.py`(который `path('new/', views.new)`)

```
   urlpatterns = [
    path('articles/', include('article.urls'))
    ]
```
Также вместо `include('project.article.urls')` можно импортировать модуль и указывать сразу его: `path('articles/', project.article.urls)`, избавляет от потенциальных циклических импортов

###### name
Также пути можно дополнить песвдонимом:
```
path('articles/', include('article.urls'), name='articles')  

# потом можно вызывать например return redirect('articles')
```

###### namespace
   Можно указать namespace, например для основного urls.py, чтобы было понятно к какому именно приложению относятся данные адреса в шаблонах):
```
 path('articles/', include('article.urls'), namespace='mint_app')
```
И потом в шаблоне указывать `{% url "mint_app:index" %}` .Так Django понимает, что псевдоним `index` нужно искать в приложении `mint_app`

#### Установка PostgeSQL в Django
```
pip install psycopg2-binary
```
Или
```
pip install psycopg2
```
В settings.py Django проекта:
```
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': 'myproject',
        'USER': 'myprojectuser',
        'PASSWORD': 'password',
        'HOST': 'localhost',
        'PORT': '',
    }
}
```
И затем:
```
python manage.py makemigrations
python manage.py migrate
```

#### Шаблонизатор
>[!info] Чтобы шаблон превратить в результат, Django использует бэкенды. Встроенных бэкенда два: DjangoTemplates и Jinja2.

>[!tip] Настривается в settings.py в списке TEMPLATES:
   `DIRS` — параметр, который перечисляет директории, где будет производиться поиск шаблонов
   `APP_DIRS` — параметр, который разрешает Django искать шаблоны в директориях приложений
   По умолчанию в качестве директории с шаблонами принято указывать `"templates"`, вот так: `'DIRS': [BASE_DIR / 'templates']`

>[!info] Все шаблоны проекта, которые используются только в нем, стоит хранить в отдельной директории в корне проекта и держать эту директорию в порядке — использовать поддиректории и хорошие имена. Директорию нужно добавить в `settings.TEMPLATES.DIRS`.

###### Context Processors
Часто возникает задача иметь в контексте шаблона доступ к параметрам запроса или настройкам проекта.
У Django есть механизм Context Processors — посредники между нами и шаблоном, которые расширяют контекст единым образом для всех шаблонов. Подключаются context processors в `settings.TEMPLATES.OPTIONS.context_processors` — это список строк с полными именами процессоров.
- `django.template.context_processors.request` добавляет в контекст переменную request с очевидным значением
- `django.template.context_processors.debug` добавляет переменную `debug`, которая истинна, если сервер запущен в режиме разработчика. С помощью этой переменной можно выводить какую-то отладочную информацию, которая не будет видна в боевом режиме

В `settings.py`, в `TEMPLATES`->`OPTIONS`->`context_processors` добавляем строку `'my_app.context_processors.my_func'`
Далее создаём в приложении файл `context_processors.py`:
```
def my_func(request):
    return {'categories': Category.objects.all()}
```

>[!info] Данные в шаблон поступают из вью в виде контекста

```  
def about(request):
    tags = ['обучение', 'программирование', 'python', 'oop']
    return render(
        request,
        'about.html',
        context={'tags': tags},
    )
```

###### Цикл for
```
{% for obj in object_list %}
    {{ obj.atr }}
{% endfor %}
```

###### if
```
{% if object_list|len == 1 %}
    *Какое-то действие*
    {% else %}
    *Какое-то действие*
{% endif %}
```

###### Наследование шаблонов
Создаём в папке `templates base.html`
Указываем там весь код html + нужные блоки:
```
...html код начала страницы...
{% block title %}{% endblock %}
{% block content %}{% endblock %}
...html код конца страницы...
```

В html коде шаблона, который состоит из кусков др. шаблонов(например index.html):
```
{% extends 'base.html' %}
{% block title %}
    Главная страница
{% endblock %}
{% block content %}
    Контент
{% endblock %}
```


#### Формы
```
<form>
    <form action="{% url 'home' %}" method="POST">
        {% csrf_token %}
        <label>Имя</label>
        <input name="name">
        <label>Телефон</label>
        <input name="phone">
        <button type="submit">Отправить</button>
</form>
```
###### Получение данных из формы
Во `views.py`, в методе страницы принимаем данные:
```
def thanks_page(request):
    name = request.POST['name']
    phone = request.POST['phone']

    # запись в БД
    element = Order(order_name=name, order_phone=phone)
    element.save()

    return render(request, './thanks.page.html', {'name': name, 'phone': phone})
```
###### Forms и ModelForm
Создаём forms.py
```
from django import forms # Импортируем формы Django

class CommentArticleForm(forms.Form):               # Просто форма БЕЗ связи с моделью
    content = forms.CharField(label='Комментарий')  # Текст комментария
```

Чтобы форма отображалась нужно передать её в шаблон через view:
```
from django.shortcuts import render
from .forms import CommentArticleForm

class CommentArticleView(View):

    def get(self, request, *args, **kwargs):
        form = CommentArticleForm() # Создаем экземпляр нашей формы
        return render(request, 'comment.html', {'form': form}) # Передаем нашу форму в контексте
```

Подставляем в шаблоне:
```
<form action="{% url 'comment_create' %}" method="post">
    {% csrf_token %}
    <table border="1">
        {{ form }}
    </table>
    <input type="submit" value="Сохранить">
    </form>
```

Принимаем данные формы:
```
class CommentArticleView(View):
    def post(self, request, *args, **kwargs):
        form = CommentArticleForm(request.POST)      # Получаем данные формы из запроса
        if form.is_valid():                          # Проверяем данные формы на корректность
            comment = Comment(
                name = form.cleaned_data['content'], # Получаем очищенные данные из поля content
                        # Заполняем оставшиеся поля
                )
            comment.save()
```

Чтобы создать форму с полями модели(связать форму и модель):
```
from django.forms import ModelForm

class ArticleCommentForm(ModelForm):
    class Meta:
        model = ArticleComment
        fields = ['content']
```

Если нам нужно дополнительно заполнить или обработать поля формы, то мы можем указать Django, что данные не нужно сразу сохранять:
```
class ArticleCommentFormView(View):

    def post(self, request, *args, **kwargs):
        form = ArticleCommentForm(request.POST)      # Получаем данные формы из запроса
        if form.is_valid():                          # Проверяем данные формы на корректность
            comment = form.save(commit=False)        # Получаем заполненную модель
            # Дополнительно обрабатываем модель
            comment.content = check_for_spam(form.data['content'])
            comment.save()
```

#### Валидация данных формы
>[!tip]  При ошибках валидации:
```
    form = YourForm(request.POST)
    for field in form:
        print("Field Error:", field.name,  field.errors)
```

>[!info] В классе формы для каждого отдельного поля можно создать метод `clean_<имя поля>`, он вызовется автоматически при создании объекта этого класса..Этот метод не получает никаких дополнительных параметров: он самостоятельно запросит значение из словаря `cleaned_data`.

```
class ArticleForm(ModelForm):
    class Meta:
        model = Article
        fields = ['name', 'body']

    def clean_name(self):
        name = self.cleaned_data['name']
        if len(name) < 4:
            raise forms.ValidationError('Name of the article must contains more than 3 chars!')
        # Метод-валидатор обязательно должен вернуть очищенные данные, даже если не изменил их
        return name
```

После валидации все данные будут переданы в словарь form.cleaned_data, и для дальнейшей работы данные формы берут именно из этого словаря.
```
# views.py
if form.is_valid():
    # Берём валидированные данные формы из словаря form.cleaned_data
    name = form.cleaned_data['name']
    email = form.cleaned_data['email']
    subject = form.cleaned_data['subject']
    message = form.cleaned_data['body']
    # При необходимости обрабатываем данные
    # ...
    # сохраняем объект в базу
    form.save()

```

#### Flash сообщения
>[!info] Сообщения реализуются через класс middleware и соответствующий контекстный процессор.
>
```
from django.contrib import messages
    
# Добавляем в нужный метод
messages.debug(request, '%s SQL операторов было выполнено.' % count)
messages.info(request, 'На вашем счету осталось три кредита')
messages.success(request, 'Информация о профиле обновлена.')
messages.warning(request, 'Срок действия вашего аккаунта истекает через три дня')
messages.error(request, 'Document deleted.')
```

Выводим в шаблоне:
```
{% if messages %}
    <ul class="messages">
        {% for message in messages %}
            <li>{{ message }}</li>
        {% endfor %}
    </ul>
    {% endif %}
```

#### Админ-панель
Создаём суперпользователя `python manage.py createsuperuser`
В `admin.py` регистрируем таблицу БД:
```
from django.contrib import admin
from .models import Order

admin.site.register(Order) 
          # либо
@admin.register(Ticket)
class TicketAdmin(admin.ModelAdmin):
    list_display = ("is_open", "theme", "user", "reg")
    search_fields = ["theme"]
    list_filter = ("is_open", "reg")
```
Теперь эта таблица доступна в админ-панели.
###### Чтобы данные отображались в понятном формате:
- добавляем в поля модели аргумент verbose_name, т.е.:
```
order_name = models.CharField(max_length=200, verbose_name = 'Имя')
order_phone = models.CharField(max_length=200, verbose_name = 'Телефон')
```
- добавляем метод строкового отображения объекта модели:
```
def __str__(self):
    return self.order_name
```
- добавляем мета класс для отображения названия таблицы:
```
class Meta:
     verbose_name = 'Заказ'
     verbose_name_plural = 'Заказы'
```

>[!tip] Чтобы поменять язык в админке, в `settings.py` меняем `LANGUAGE_CODE` на `'ru-RU'`

###### Простые фильтры
- Поиск в админке по названию и телу статьи
```
class ArticleAdmin(admin.ModelAdmin):
    search_fields = ['name', 'body']
  
admin.site.register(ArticleAdmin)
```
- Добавим отображение в списке статей даты публикации и фильтрацию по данному полю
```
from django.contrib import admin
from django.contrib.admin import DateFieldListFilter
from .models import Article


@admin.register(Article)
class ArticleAdmin(admin.ModelAdmin):
    list_display = ('name', 'timestamp') # Перечисляем поля, отображаемые в таблице списка статей
    search_fields = ['name', 'body']
    list_filter = (('timestamp', DateFieldListFilter),) # Перечисляем поля для фильтрации
```

###### Шаблоны для админки
Лежат в `venv/bin/lib/python3.11/site-packages/django/contrib/admin/templates/admin/`
Берём например `base_site.html`(Главная страница админки) и закидываем в `templates/admin/base_site.html`
Поскольку Django будет искать сначала в пользовательских шаблонах, то там уже можно редактировать по своему усмотрению

###### Функция для отображения поля модели
В списке объектов модели появится колонка с фото
В admin.py:
```
from django.utils.safestring import mark_safe

@admin.register(MModel)
class MModelAdmin(admin.ModelAdmin):
    list_display = ("name", "user", "category", "price", "get_html_photo", "is_hidden")
    search_fields = ["name"]
    list_filter = ("category", "price")

    def get_html_photo(self, object):
        return mark_safe(f"<img src='{object.photo00.url}' width=70>")   # возвращаем html-код(mark                                                              # safe не позволяет экранировать тэги)

    get_html_photo.short_description = 'Фото'
```

###### Сигналы
>[!info] Django включает в себя «диспетчер сигналов», который помогает получать уведомления о действиях, происходящих в других частях фреймворка. По возможности следует выбирать прямой вызов кода обработки, а не диспетчеризацию через сигнал.

Например, своя функция, срабатывающая при удалении экземпляра модели:
```
 # models.py
from django.db.models.signals import pre_delete
from django.dispatch import receiver

@receiver(pre_delete, sender=MModel)
def delete_model_files(sender, instance, **kwargs):
    print(f"{instance.name} был удалён")
```


#### ORM
>[!info] В Django ORM реализует паттерн проектирования Active Record. Его идея, состоит в том, что каждой таблице соответсвует один класс(модель). Этот класс отвечает за реализацию бизнес логики и за взаимодействие с БД.

К фактической базе данных Django подключается с помощью Database Engines. Обычно одна база приходится на одно веб-приложение.

Описываются базы данных в словаре `settings.DATABASES`:
```
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': os.path.join(BASE_DIR, 'db.sqlite3'),
    }
}
```

- **null=True**
Это свойство позволяет базе данных оставлять поле пустым.
Крайне не рекомендуется применять это свойство к строковым полям (`CharField` и `TextField`), так как вместо `None` в них лучше класть пустую строку.

- **blank=True**
Настраивает поведение админки и Django-форм. Если для поля не указан `blank=True`, то админка запретит нам сохранить форму, и потребует заполнить поле

###### Создание модели
Создаём модель в models.py(ID прописывать не обязательно, уже идёт автоматом)
```
from django.db import models
class TimestampedModel(models.Model):
    """An abstract model with a pair of timestamps."""

    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)

    class Meta:
        abstract = True

class Order(models.Model): 
    # Если унаследовать от TimestampedModel, то будет 2 дополнительных поля:
    # дата создания и дата обновления
    order_dt = models.DateTimeField(auto_now=True)
    order_name = models.CharField(max_length=200)
    order_phone = models.CharField(max_length=200)
    priсe = models.DecimalField(max_digits=7, decimal_places=2) # Число с запятой(всего цифр - 7,
                                                                #  из них после запятой 2)
    email = models.EmailField(unique=True)
    description = models.TextField('Описание')
    timestamp = models.DateTimeField(auto_now_add=True)
    body = models.TextField(null=True)                      # необязательное поле
    published = models.BooleanField(default = False)        # булево значение, по-умолчанию False
    type = CharField(max_length=200, choices=TYPE_CHOICES)  # поле с выбором

    # 1 элемент - значение в базе, 2й - инфо для отображения в админке и т.д.
    TYPE_CHOICES = (
        (1, 'Тип 1'),          
        (2, 'Тип 2'),
        (3, 'Тип 3'),
    )
```
>[!info] Поле id уже описано и является первичным ключом. Хотя его можно переопределить.

Производим и применяем миграцию:
```
python manage.py makemigrations
python manage.py migrate
```

Можно в классе модели переопределить метод сохранения, чтобы например, выполнять какую-нибудь функцию при сохранении модели:
```
def save(self, *args, **kwargs):
    super().save(*args, **kwargs)
    set_rating(self.book)          # кастомная функция, условно подсчитывающее среднее значение
                                   #  среди связанных полей
```
###### Миграции
>[!info] Миграции — это описания изменений в наших моделях, и в дальнейшем в схеме базы данных. Мы можем просмотреть эти файлы миграций. Они располагаются в директориях migrations внутри приложений. 

- Посмотреть сам SQL-запрос можно так: `python manage.py sqlmigrate article 0001`
- Откатиться к предыдущей миграции можно указав её: `python manage.py migrate article 0005`
- Если нам нужно отменить все миграции этого приложения, нужно указать zero в качестве версии миграции: `python manage.py migrate article zero`

###### Интерактивная консоль Django и ORM
```
python manage.py shell
```

```
from crm.models import Order               # Добавление записи через ORM
n = Order(order.name = 'Иван', order_phone = '+7123456789')
n.save()                                   # Модель ленивая, т.е. запрос выполняется только здесь

from django.db import connection           # Вывод SQL запроса, который был выполнен
connection.queries

n2.Order.objects.create(order_name = 'Anna', order_phone = '+78888888888') # Добавление записи 
# через менеджер записей ORM
#  Django(objects). Отвечает за операции, относящиеся не к конкретной сущности, а к самой таблице

Order.objects.all()  # Вывод всех экземпляров QuerySet, состоящих из экземпляров Order. Вся таблица.
Order.objects.filter(order_name = 'Anna')  # Выборка с фильтром. Только записи с order_name = 'Anna'
Order.objects.filter(order_name = 'Anna').exclude(status='finished')  # Выборка с фильтром. Только
                          # записи с order_name = 'Anna', но не записи со статусом 'finished'
Order.objects.order_by('id')               # Выборка с выводом по id
Order.objects.order_by('-id')              # Выборка с выводом по id по убыванию
Order.objects.get(pk=1)                    # Выборка одной записи с id=1(здесь pk(private key).
                                           #  id и pk будут совпадать, если вы не задавали id
                                           # отдельным полем). get может получать только 1 запись.
User.objects.count()                       # Общее число записей
first_user = User.objects.raw('SELECT * FROM blog_user')[0]

order1 = Order.objects.get(pk=1)           # изменение записи
order1.order_phone = '111111111111111'
order1.save()
order1.delete()
```

>[!info] Lookup - двойное подчёркивание однозначно отделяет имя поля от оператора.
```
User.objects.filter(
    votes__gt=2,                # WHERE votes > 2
    votes__gte=100,             # WHERE votes >= 100("greater than or equal")
    id__in=[45, 101, 512],      # равно одному из значений списка (входит В список)
    first_name__iexact='John',  # равно без учёта регистра
    age__range=(18, 21),        # входит в диапазон 18..21
    middle_name__isnull=True,   # необязательное поле не заполнено
    last_name__icontains='ibn'  # содержит подстроку
)
```

###### Annotate
Добавление записи к каждому объекту внутри queryset
```
from django.db.models import Count

users = User.objects.annotate(mmodel_count=Count('mmodel')).filter(mmodel_count__gt=1)

       # Здесь происходит аннотация(добавление записи к каждому пользователю(имя
       #    поля аннотации mmodel_count)), полю присваивается функция Count
       # Функция Count выполняет агрегацию и подсчитывает количество связанных
       #   объектов модели mmodel для каждого пользователя.
       # После к результату применяется фильтр mmodel_count__gt=1, т.е. результат
       #    в поле аннотации mmodel_count больше чем 1

from django.db.models.functions import Lower
MModel.objects.annotate(lower_name=Lower('name')).filter(lower_name__icontains=query)  
# содержит подстроку БЕЗ учёта регистра
```

###### select_related() и prefetch_related()
Это два метода в Django ORM, которые позволяют оптимизировать запросы к базе данных и улучшить производительность.

- `select_related() `для загрузки связанных объектов вместе с основными объектами в одном запросе работает с полями, устанавливающими связи один-к-одному (OneToOneField) или многие-к-одному (ForeignKey). Django выполняет JOIN-запрос, чтобы объединить таблицы и получить связанные объекты. Это избавляет от необходимости выполнять дополнительные запросы, чтобы получить связанные объекты
Пример:
```
class Author(models.Model):
    name = models.CharField(max_length=100)

class Book(models.Model):
    title = models.CharField(max_length=100)
    author = models.ForeignKey(Author, on_delete=models.CASCADE)

books = Book.objects.select_related('author').all()
for book in books:
    print(book.title, book.author.name)
```
    
- `prefetch_related()` для загрузки связанных объектов в одном запросе, но для полей, устанавливающих связи многие-ко-многим (ManyToManyField). В отличие от select_related(), prefetch_related() выполняет дополнительный запрос для получения связанных объектов, но делает это за один раз, загружая все связанные объекты одним запросом
Пример:
```
class Author(models.Model):
    name = models.CharField(max_length=100)

class Book(models.Model):
    title = models.CharField(max_length=100)
    authors = models.ManyToManyField(Author)

books = Book.objects.prefetch_related('authors').all()
for book in books:
    print(book.title, [author.name for author in book.authors.all()])
```

#### Связи в ORM
###### Один-к-одному
```
class Profile(models.Model):
    user = models.OneToOneField(User, default=None, on_delete=models.CASCADE)
```

###### Один-ко-многим
Внешний ключ для поля класса модели Child указывается так:
```
class Child(models.Model):
    creator = models.ForeignKey(User, on_delete=models.CASCADE)   # это поле ссылается на др. 
                                            # таблицу(Parent), у которого много таких объектов

```

###### Многие-ко-многим
 В конструктор models.ManyToManyField передается сущность, с которой устанавливается отношение многие ко многим. В результате будет создаваться промежуточная таблица, через которую собственно и будет осуществляться связь.
```
class Course(models.Model):
    name = models.CharField(max_length=30)
 
class Student(models.Model):
    name = models.CharField(max_length=30)
    courses = models.ManyToManyField(Course)
```
Также имеется директива `through`, т.е. `models.ManyToManyField(Course, through='CourseStudentRealtion')`. Тоесть мы напрямую указываем существующую таблицу, в которой связваются отношнения и соответственно друная промеж. таблица НЕ будет создана.
Пример промежуточной модели `CourseStudentRealtion`:
```
class CourseStudentRealtion(models.Model):
    course = models.ForeignKey(Course, on_delete=models.CASCADE)
    student = models.ForeignKey(Student, on_delete=models.CASCADE)
    like = models.BooleanField(default=False)
    rate = models.PositiveSmallIntegerField(choices=RATE_CHOICES)
```

Варианты `on_delete=` (Что будет со свзязанными полями/записями при удалении)
```
on_delete=models.SET_NULL           Установка Null
on_delete=models.CASCADE            Каскадное удаление
on_delete=PROTECT                   Запрет удаления записи, пока есть текущая запись
```

#### Генерация форм
Создаём `forms.py`
```
from django import forms
class OrderForm(forms.Form):
    name = forms.CharField(max_length=200, required=False)  # не обязательное поле
    phone = forms.CharField(max_length=200)        # доп. аргумент widget=forms.TextInput(attrs={'class': 'css_input'})
                                                #  так подключается класс .css_input из вёрстки
```

Во `views.py` импортируем `from .forms import OrderForm`
Далее рендерим её:
```
 def first_page(request):
    initial_data = {
                'first_name': user.first_name,
                'last_name': user.last_name,
                'email': user.email,
    }
    form = OrderForm(initial=initial_data)         # initial - это словарь для заполнения формы
    return render(request, './index.html', {'form': form})
```

В html подставляем шаблон:
```
<form action="{% url 'home' %}" method="POST">
    {% csrf_token %}
    {{ form }}                                         # {{ form.as_p }}
    <button type="submit">Отправить</button>
</form>
```
Таким образом получает форма со встренной валидацией.

#### Статический контент
В `settings.py` в `STATIC_URL` указывем пути для статических файлов
```
STATIC_URL = '/static/'
```
и выше создём список:
```
STATICFILES_DIRS = [
    BASE_DIR / "static"
]
```

Создаём папку `static`. В ней будет папка `img` и файл `1.jpg`
В html указываем путь с помощью шаблонизатора
`<img src="{% static 'img/1.jpg' %}">`

#### Медиа файлы
Указание пути сохранения
```
MEDIA_URL = '/media/'
MEDIA_ROOT = os.path.join(BASE_DIR, 'media')
```

Чтение. В `urls.py`, в `urlpatterns` добавляем:
```
from django.conf import settings
from django.conf.urls.static import static

urlpatterns = [
    path('', views.first_page)
] + static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
```

#### VIEWS
###### views функции
>[!info] Это обычные функции, которые принимают на входе запрос — объект класса HttpRequest, и возвращают ответ — объект класса HttpResponse.

Также к ним можно применять декораторы.
```
@require_http_methods(['GET', 'POST'])
def login(request):
    pass
```
Если такая view получит запрос с отличным от указанных методом, то автоматически будет сформирован
 ответ MethodNotAllowed (код 405).

###### views классы
Все представления этого вида наследуются от класса `django.views.View`
```
from django.shortcuts import render, get_object_or_404
from django.views import View

class IndexView(View):

    def get(self, request, *args, **kwargs):
        article = get_object_or_404(Article, id=kwargs['id'])
        return render(request, 'articles/show.html', context={
            'article': article,
        })
```
Метод `get` здесь работает как view function. При этом на каждый запрос будет создан новый экземпляр этого класса.
```
# urls.py
urlpatterns = [
    ...
    path('', IndexView.as_view()),
    ...
]
```

Пример с `TemplateView`:
```
from django.views.generic.base import TemplateView

class HomePageView(TemplateView):

    template_name = "home.html"

    def get_context_data(self, **kwargs):
        context = super().get_context_data(**kwargs)
        context['who'] = 'World'
        return context
```
В `urls.py`:
```
urlpatterns = [
    ...
    path('', TemplateView.as_view(template_name='index.html')),
    ...
]
```

#### Получение POST и GET параметров
###### POST
```
@require_http_methods(['POST'])
def articles_post(request):
    title = request.POST.get('title')
    author = request.POST.get('author')
    articles.append({'title': title, 'author': author})
    return redirect("articles_get")
```

###### GET
```
@require_http_methods(['GET'])
def articles_get(request):
    q = request.GET.get('q', '')
    return render(request, 'articles.html', context={'q': q})
```

###### Определение метода
```
if request.method == "POST":
    pass
```

#### Маршрутизация
###### Статическая
```
# urls.py
urlpatterns = [
    path('', views.index),
]
```

###### Динамическая
```
# urls.py
urlpatterns = [
    path('users/<int:user_id>/pets/<int:pet_id>/med_info/', med_info_view),
    …
]
```
```
# views.py:
def med_info_view(request, user_id, pet_id):
    pass
```

#### COOKIE
###### УСТАНОВКА
Во view:
```
cookie_value = '122'
response =  render(request, 'user_profile.html')
response.set_cookie('cookie_huyooki', cookie_value)
```

###### ПОЛУЧЕНИЕ
В шаблоне:
```
<p>{{ request.COOKIES.cookie_huyooki }}</p>
```
Либо во view:
```
cookie_value = request.COOKIES.get('cookie_huyooki')
```
###### УДАЛЕНИЕ
Во view:
```
response =  render(request, 'user_profile.html')
response.delete_cookie('cookie_huyooki')
```

С подписанными куками тоже тамое, только методы:
```
set_signed_cookie(key, value, salt='', max_age=None, expires=None, path='/', domain=None,       secure=False, httponly=False, samesite=None)
get_signed_cookie(key, default=RAISE_ERROR, salt='', max_age=None)
```

#### Переменные окружения
- Используем python-dotenv для загрузки переменных окружения из файла
```
pip install python-dotenv

from dotenv import load_dotenv
load_dotenv()                      # Загрузка переменных окружения из файла .env
```

- Читаем переменные окружения
```
import os

SECRET_KEY = os.getenv('SECRET_KEY')
DEBUG = os.getenv('DEBUG', False)
```


#### Шаблон 404
В `settings.py`:
```
DEBUG = False
ALLOWED_HOSTS = ['127.0.0.1']
```
Django будет искать `404.html` в `templates` директории

#### Отправка почты
В `settings.py`:
```
EMAIL_BACKEND = 'django.core.mail.backends.smtp.EmailBackend'

# Настройки для Mail.ru
EMAIL_HOST = 'smtp.mail.ru'
EMAIL_PORT = 2525
EMAIL_USE_TLS = True
EMAIL_USE_SSL = False
EMAIL_HOST_USER = 'your@mail.ru'
EMAIL_HOST_PASSWORD = 'app_password'

# Для mail.ru нужно настроить пароль для внешних приложений https://account.mail.ru/user/2-step-auth/passwords/
```
Во `views.py`:
```
from django.core.mail import send_mail

subject = 'Код подтверждения для MINT-COAST.RU'
message = 'Это сообщение было отправлено из Django приложения.'
from_email = 'noreply.mintcoast@mail.ru'
to_email = 'nemp_122@mail.ru'

send_mail(subject, message, from_email, [to_email])
```

#### Login/Logout пользователя
###### Login
```
user = authenticate(username=username, password=password)
login(request, user)
```

###### Logout
```
logout(request)
```

Проверка залогинен ли пользователь:
- В шаблоне:
```
{% if user.is_authenticated %}
```
- Во views:
```
request.user.is_authenticated
```

Проверка существует ли пользователь:
```
User.objects.filter(username=username).exists()
```

Проверка подтверждён ли email:
```
request.user.emailaddress_set.filter(primary=True, verified=True).exists()
```

#### Пагинация
```
from django.core.paginator import Paginator

models = MModel.objects.all().order_by('id')   
p = Paginator(models, 20)                           # 20 объектов на странице
models = p.get_page(page)                           # все объекты страницы page
pages = p.page_range                                # range из страниц
last_page = p.num_pages                             # количество страниц
```

#### MIDDLEWARE
Создаём `middleware.py`
```
from .models import Ban

class BanMiddleware:
    def __init__(self, get_response):
        self.get_response = get_response

    def __call__(self, request):
        # Код, выполняющийся перед передачей запроса представлению
        # Делайте здесь что угодно, что вам нужно с запросом
        if request.user.is_authenticated and Ban.objects.filter(user=request.user.id).exists():
            return render(request, 'banned.html', status=403, context={'reason': ban.reason, 'end_date': ban.end_date})

        response = self.get_response(request)   # здесь будут вызваны последующие слои(мидлвари или вью) и только после этого след. код
  
        # Код, выполняющийся после возврата ответа представлением
        # Делайте здесь что угодно, что вам нужно с ответом

        return response

    # необязательный метод для обработки исключений
    def process_exception(self, request, exception):
        print(f'Exception is {exception}')
        return HttpResponse('Exception!')      # если мы вернём None, то Django продолжит обработку ошибок по дефолту(сгенерирует
                                               #  html сообщение об ошибке)
```

Регистрируем свою мидлварь в `settings.py`:
```
MIDDLEWARE = [
    ...
    'mint_app.middleware.BanMiddleware',
    ...
]
```

#### Throttling middleware
```
class ThrottlingMiddleware:
    def __init__(self, get_response):
        self.get_response = get_response

    def __call__(self, request):
        # Получаем IP-адрес пользователя или другую уникальную строку для идентификации
        # Для простоты примера здесь используется IP-адрес, но это не самое надежное решение
        client_ip = request.META['REMOTE_ADDR']

        # Устанавливаем ключ для кэша, используя IP-адрес и префикс
        cache_key = f'throttle_{client_ip}'

        # Получаем текущее количество запросов из кэша
        request_count = cache.get(cache_key, 0)

        # Проверка на превышение лимита запросов
        if request_count >= 100:                               # Примерно 100 запросов в час
            return HttpResponseForbidden('Превышен лимит запросов')

        # Увеличиваем счетчик запросов и сохраняем его в кэше
        cache.set(cache_key, request_count + 1, 3600)          # Сохраняем на час

        # Пропускаем запрос дальше по стеку middleware
        response = self.get_response(request)

        return response
```

#### Логгирование в Django
Устанавливаем словарь в `settings.py`(Например настравиваем логгирование для событий `views.py`):
```
LOGGING = {
    "version": 1,
    "disable_existing_loggers": False,
    "handlers": {
        "file": {
            "level": "DEBUG",
            "class": "logging.FileHandler",
            "filename": os.path.join(BASE_DIR, "site_log.log"),
            'formatter': 'simple',
        },
    },
    "loggers": {
        "my_views": {
            "handlers": ["file"],
            "level": os.getenv("DJANGO_LOG_LEVEL", "DEBUG"),
            "propagate": False,
        },
    },
    'formatters': {
        'simple': {
            'format': '{asctime} - {levelname} - {name} - {message}',
            'style': '{'
        }
    },
}
```
Во views.py:
```
import logging
logger = logging.getLogger("my_views")

logger.debug(f'Failed to edit. Model: {model.name}. Owner: {model.user} Errors: {errors}')
```

#### Тесты в Django
Для тестов Django использует копию базы данных, которую затем очищает. Тесты в Django наследуются от unittest. Можно в приложении создать директорию `tests` и в ней test_*(напр. `test_logic.py`)
```
from django.test import TestCase

class LogicTestCase(TestCase):
    def setUp(self):                         # метод, запускаемый ПЕРЕД тестами класса  
        pass

    def test_plus(self):
        self.assertEqual(25, 15 + 10)        # сравнение(что ожидаем, функция приложения)
```
Запуск тестов(из терминала, из вирт. окружения): `python manage.py test mint_app.tests.test_logic`

#### OAuth
Проще всего сделать через allauth
```
pip install django-allauth
```
В `settings.py`:
`TEMPLATES-OPTIONS-context_processors` добавить `'django.template.context_processors.request'`
```
AUTHENTICATION_BACKENDS = [
    ...
    # Бэкенд аутентификации по умолчанию
    'django.contrib.auth.backends.ModelBackend',

    # `allauth` Бэкенд аутентификации
    'allauth.account.auth_backends.AuthenticationBackend',
    ...
]

INSTALLED_APPS = [
    ...
    # The following apps are required:
    'django.contrib.auth',
    'django.contrib.messages',

    'allauth',
    'allauth.account',
    'allauth.socialaccount',

    'allauth.socialaccount.providers.github',
    'allauth.socialaccount.providers.vk',
```
В `MIDDLEWARE` добавить `"allauth.account.middleware.AccountMiddleware"`
```
SOCIALACCOUNT_PROVIDERS = {
    'github': {
        "VERIFIED_EMAIL": True,
        'SCOPE': [
            'user',
            'repo',
            'read:org',
        ],
        'client_id': 'acd277a293893187dcaa',
        'secret': '07d272411078a411e5361470cf01ee16fa28a5d1',
        'key': '',
    },
    'vk': {
        'SCOPE': [
            'email',
            'first_name',
            'last_name',
        ],
        'client_id': '51778698',
        'secret': 'a3LHmz6CDaH6ey4FHUCM',
        'key': '',
    },
}
```
В `urls.py`:
```
urlpatterns = [
    ...
    path('accounts/', include('allauth.urls')),
    ...
]
```
```
python manage.py makemigrations
python manage.py migrate
```
На github и vk нужно зарегистрировать наше приложение:
GitHub: https://github.com/settings/applications/new (`Homepage URL`: `http://127.0.0.1:8000/`;
            `Authorization callback URL`: `http://127.0.0.1:8000/accounts/github/login/callback`)
VK: https://vk.com/editapp?act=create (`Адрес сайта`: `http://127.0.0.1:8000`; `Базовый домен`: `127.0.0.1`;
                                                        `Доверенный redirect URI` оставить пустым)
От этих сайтов получаем `client ID` и `secret key`
Заходим в админку Django и создаем соответственно 2 социальных приложения(github и vk):
Нужно выбрать `провайдер` из списка, вписать `имя`, `ID клиента `и `секретный ключ`. 
Вставляем в шаблон для перехода на встроенный view allauth:
```
{% load socialaccount %}
<a href="{% provider_login_url 'github' %}"> Войти через GitHub </a><br>
<a href="{% provider_login_url 'vk' %}"> Войти через VK </a><br>
```
Или можно сразу форму вставлять, чтобы сразу напрямую соединяться с сервером авторизации:
```
<form method="post" action="{% provider_login_url 'vk' %}">
    {% csrf_token %}
     <button type="submit">VK</button>
</form>
```

#### CAPTCHA
reCaptcha3

Регистрируем сайт:
https://www.google.com/recaptcha/admin/create
указываем `reCaptcha v3`
домены: `localhost` и `127.0.0.1`

получаем `Site_Key` и `Secret_Key`

Страница проекта: https://github.com/kbytesys/django-recaptcha3
```
pip install django-recaptcha3
```
В `settings.py`:
```
INSTALLED_APPS = (
    ...
    'snowpenguin.django.recaptcha3',
    ...
)

RECAPTCHA_PRIVATE_KEY = 'your private key'
RECAPTCHA_PUBLIC_KEY = 'your public key'
RECAPTCHA_DEFAULT_ACTION = 'generic'
RECAPTCHA_SCORE_THRESHOLD = 0.5              # при 0 все проходит всё
```
В `forms.py`:
```
from snowpenguin.django.recaptcha3.fields import ReCaptchaField

class ExampleForm(forms.Form):
    [...]
    captcha = ReCaptchaField()
    [...]
```
В шаблоне:
```
{% load recaptcha3 %}
{% recaptcha_init %}
{% recaptcha_ready action_name='homepage' %}              # homepage|login|social|e-commerce
```
>[!warning] Заменить строчку `from django.utils.translation import ugettext_lazy as _` на `from django.utils.translation import gettext_lazy as _`

#### Скачивание файла
```
from django.http import HttpResponse
from django.conf import settings
import os

def download_file(request):
    # Путь к файлу, который нужно скачать
    file_path = os.path.join(settings.MEDIA_ROOT, 'path_to_file/filename.txt')
    
    # Открываем файл для чтения в бинарном режиме
    with open(file_path, 'rb') as file:
        response = HttpResponse(file.read(), content_type='application/octet-stream')
        
        # Устанавливаем заголовок для указания имени файла
        response['Content-Disposition'] = 'attachment; filename="filename.txt"'
        
        return response
```

## Django REST Framework(DRF)
![[drf.png]]
>[!info] Позволяет посредством API выдавать данные, например в JSON. Таким образом рендеринг страницы происходит не на стороне сервера, а на стороне клиента.

```
pip install djangorestframework
pip install markdown                  # Markdown support for the browsable API.
pip install django-filter             # Filtering support
```

В `settings.py` регистрируем:
```
INSTALLED_APPS = [
    ...
    'rest_framework',
]
```

Допустим у нас уже есть модель Book.
Создаём сериализатор(Создаём файл serializers.py в приложении):
```
from rest_framework.serializers import ModelSerializer
from store.models import Book

class BooksSerializer(ModelSerializer):
    class Meta:
        model = Book
        fields = '__all__'
```

Создаём ViewSet во `views.py`:
```
from rest_framework.permissions import IsAuthenticated
from rest_framework.viewsets import ModelViewSet
from store.models import Book

class BookViewSet(ModelViewSet):
    queryset = Book.objects.all()
    serializer_class = BooksSerializer
    permission_classes = [IsAuthenticated]    # указывает, что запрос доступен только для
                                              # аутентифицированных пользователей
```

В `urls.py`:
```
from rest_framework.routers import SimpleRouter
router = SimpleRouter()
router.register(r'book', BookViewSet)    # book - это url

urlpatterns = [
    path('admin/', admin.site.urls),
]
urlpatterns += router.urls
```

Теперь можно получить инфо: `127.0.0.1:8000/book/?format=json`

Чтобы определить дефолтный формат и не писать в url `format=json`, нужно добавить в `settings.py`:
```
REST_FRAMEWORK = {
    'DEFAULT_RENDERER_CLASSES': (
        'rest_framework.renderers.JSONRenderer',
    ),
    'DEFAULT_PARSER_CLASSES': (
        'rest_framework.parsers.JSONParser',
    )
}
```

#### Своё поле в сериализаторе
```
from rest_framework.serializers import ModelSerializer, SerializerMethodField

class UserSerializer(ModelSerializer):
    models_count = SerializerMethodField()

    class Meta:
        model = User
        fields = ('id', 'username', 'first_name', 'last_name', 'email', 'models_count')

    def get_models_count(self, instance): # instance - это сериализуемый в данный момент экземпляр
        return MModel.objects.filter(user=instance).count()
```

Естественно нужно сделать ещё `UserViewSet` и зарегистрировать его в роутере
>[!warning] Нужно учитывать, что будет +1 запрос в базу на каждый экземпляр `User`

#### Вложенные поля в сериализаторе
В JSON'е в результате будут вложенные словари на месте значения readers
```
class BookReaderSerializerModelSerializer):
    class Meta:
        model = User
        fields = ('first_name', 'last_name')


class BookSerializer(ModelSerializer):
    readers = BookReaderSerializer(many=True) # название поля - поле в модели Book(many-to-many)

    class Meta:
        model = Book
        fields = ('id', 'name', 'price', 'author_name', 'readers')

```

#### Permissions
Создание собственных разрешений. Может понадобится, если удалять, обновлять может только юзер, ассоциированный с записью. Например владелец книги.

Создадим в приложении `permissions.py`
```
from rest_framework.permissions import BasePermission, SAFR_METHODS

class IsOwnerOrReadOnly(BasePermission):
   def has_object_permission(self, request, view, obj):  # т.е. работет на те view, которые 
                                                         #  привязан к какому-то объекту
        return bool(
            request.method in SAFE_METHODS or
            request.user and
            request.user.is_authenticated and obj.owner == request.user
        )
```
*Не забудьте импорировать класс во ViewSet*

#### Фильтрация в DRF
```
pip install django-filter                             # (author: Alex Gaynor)
```

Во `views.py`:
```
from django_filters.rest_framework import DjangoFilterBackend

class CategoryViewSet(ModelViewSet):
    queryset = Category.objects.all()
    serializer_class = CategorySerializer

    filter_backends = [DjangoFilterBackend]  # указываем бэкенд фильтрации
    filterset_fields = ['name']              # поля для фильтрации
```

И теперь по запросу `http://127.0.0.1:8000/categories/?name=Музыка` получаем в JSON запись из БД

#### Поиск в DRF
>[!info] Если поиск только по одному полю, то достаточно фильтрации.

Во `views.py`:
```
from rest_framework.filters import SearchFilter

class CategoryViewSet(ModelViewSet):
    queryset = Category.objects.all()
    serializer_class = CategorySerializer

    filter_backends = [SearchFilter]               # указываем бэкенд поиска
    search_fields = ['name', 'description']        # поля поиска
```

И теперь по запросу `http://127.0.0.1:8000/categories/?search=М` получаем в JSON запись из БД, где в
полях `name` или `description` есть буква `М`

#### Сортировка в DRF
```
from rest_framework.filters import OrderingFilter

class CategoryViewSet(ModelViewSet):
    queryset = Category.objects.all()
    serializer_class = CategorySerializer

    filter_backends = [OrderingFilter]
    ordering_fields = ['name', 'path']
```
И теперь по запросу `http://127.0.0.1:8000/categories/?ordering=name` получаем отсортированный JSON
по полю `name`. Если `-name`, то сортировка в обратном порядке.

#### CRUD в DRF
###### Создание
Создавать записи через Django REST Framework можно передавая методом POST данные в JSON
Т.е. запрос POST на `http://127.0.0.1:8000/categories/` с прикреплённым JSON:
```
{
    "name": "new category",
    "path": "path"
}
```
Если нужно, например, при создании связать запись с текущим(аутентифицированным) пользователем:
Пример модели:
```
class Book(models.Model):
    name = models.CharField(max_length=255)
    author_name = models.CharField(max_length=255)
    owner = models.ForeignKey(User, on+delete=models.SET_NULL, null=True)
```
Во ViewSet добавляем метод(который будет перегруженным) `perform_create`(Найти его можно пройдя в класс `ModelViewSet`, а оттуда в `CreateModelMixin`):
```
class BookViewSet(ModelViewSet):
    queryset = Book.objects.all()
    serializer_class = BookSerializer
    filter_backends = [DjangoFilterBackend, SearchFilter, OrderingFilter]
    permission_classes = [IsAuthenticatedOrReadOnly]
    filterset_fields = ['name']
    search_fields = ['name', 'author_name']
    ordering_fields = ['name', 'author_name']
        
    def perform_create(self, serializer):
        serializer.validated_data['owner'] = self.request.user  # validated_data - это данные 
                                        # serializer, после того как прошли валидацию. Так как
                                        #  запрос будут делать только авторизованные пользователи,
                                        #  то request.user будет всегда
            serializer.save()           # также, но короче serializer.save(owner=request.user)
```

###### Обновление
Обновлять записи можно с помощью метода PUT. Только поля надо передавать ВСЕ.
Т.е. запрос PUT на `http://127.0.0.1:8000/categories/16/` с прикреплённым JSON:
```
{
    "id": "16",
    "name": "new category",
    "path": "path"
}
```

###### Удаление
Удаление записи с помощью метода DELETE:
Т.е. запрос DELETE на `http://127.0.0.1:8000/categories/16/`

#### Throttling middleware для DRF
Ограничение числа запросов к API на уровне проекта:
-  для анонимных пользователей — не более десяти в минуту;
- для аутентифицированных пользователей — не более ста в минуту.
Добавить в `settings.py` ключи `DEFAULT_THROTTLE_CLASSES` и `DEFAULT_THROTTLE_RATES` с необходимыми параметрами в словарь `REST_FRAMEWORK`:
```
REST_FRAMEWORK = {
    'DEFAULT_RENDERER_CLASSES': (
        'rest_framework.renderers.JSONRenderer',
    ),
    'DEFAULT_PARSER_CLASSES': (
        'rest_framework.parsers.JSONParser',
    ),
    'DEFAULT_THROTTLE_CLASSES': [
        'rest_framework.throttling.UserRateThrottle',
        'rest_framework.throttling.AnonRateThrottle',
    ],
    'DEFAULT_THROTTLE_RATES': {
        'user': '100/minute',  # Лимит для UserRateThrottle
        'anon': '10/minute',   # Лимит для AnonRateThrottle
    }
}
```