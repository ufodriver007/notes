![[Pasted image 20231219081151.png]]
![[2023-12-19_08-37.png]]
[[Django, DRF#Django REST Framework(DRF)]]
```
pip install django
```
```
django-admin startproject my_first_site                      # создать шаблон проекта
```
```
python3 manage.py runserver                                  # запустить проект 127.0.0.1:8000
```
```
python3 manage.py startapp <app_name>                        # создаём приложение
 # затем регистрируем в settings.py в списке INSALLED_APPS как '<app_name>.apps.app_class'

python3 manage.py createsuperuser                            # создаём админа
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
|`urls.py`|Файл привязок(роутер). при обрашении к определённому url запускается `view()`, ссылаясь на `views.py`

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
Устанавливаем PostgreSQL https://www.postgresql.org/download/
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
И затем
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

###### Подключение части разметки
В `templates` создаём файл с частью разметки, например `notifications.html`
В шаблоне, подключаем его
```
{% include "notifications.html" %}
```

###### Свои теги в шаблонизаторе
Создаём в приложении директорию `templatetags`, в ней файл `__init__.py` и питоновский файл с любым именем, например `mint_app_tags.py`, в нём пишем
```
from django import template  
from mint_app.models import Category           # импорт для примера
  
register = template.Library()   
  
@register.simple_tag()  
def tag_categories():                          # любое название
    return Category.objects.all()
```

В шаблоне
```
{% load mint_app_tags %}                       # название файла

{% tag_categories as categories %}             # функция tag_categories вернёт результат
                                               #   в переменную categories
{% for category in categories %}  
<h4>{{ category }}</h4>  
{% endfor %}
```

Ещё пример функции тэга с доступом к контексту
```
from django import template  
from django.utils.http import urlencode
  
register = template.Library()   

@register.simple_tag(takes_context=True)  
def change_params(context, **kwargs):   
    query = context['request'].GET.dict()
    query.update(kwargs)                                # добавляем свои параметры в словарь                   
    return urlencode(query)             # из словаря возвращает строку, которую можно использовать
                                        #  как параметры URL-адреса
```
В шаблоне
```
{% load goods_tags %}

<a href=?{% change_params page=page %}>{{ page }}</a>    # передаём в функцию тега change_params
                                                         # аргумент page=page в виде словаря
                                                         # след. аргументы через пробел
```

>[!tip] Можно использовать для вывода списка категорий, [[Django, DRF##### Фильтры для поиска на сайте|проброса GET параметров]] и т.д.

###### Обратное разрешение URL-адреса
```
{% url "<namespace>:<name>" <arguments> %}
{% url "<name>" <arguments> %}
{% url "<name>" %}
```
```
{% url "catalog:product" product.id %}
# Ищется в пространстве имён 'catalog', в нём путь с имёнем `product` и подставляется туда аргумент
```
Для понимания, основной  файл `urls.py`
```
urlpatterns = [  
    path('catalog/', include(`goods.urls`, namespace=`catalog`)),
]
```
И `urls.py` приложения `goods`
```
urlpatterns = [  
    path('product/<int:product_id>/`, views.product, name='product')
]
```
Получается
```
/catalog/product/2
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
###### Forms
Создаём forms.py
```
from django import forms # Импортируем формы Django

class CommentArticleForm(forms.Form):               # Просто форма БЕЗ связи с моделью
    content = forms.CharField(label='Комментарий')  # Текст комментария
    req = forms.ChoiceField(                        # виджет радиокнопки
        widget=forms.RadioSelect(),
        choices=[
            ("0", False),
            ("1", True),
        ],
        initial=0,
    )
    address = forms.CharField(                      # виджет textarea
        widget=forms.Textarea(
            attrs={
                "class": "form-control",            # НЕ РЕКОМЕНДУЕТСЯ фронтэнд хранить в бекэнде
                "id": "address",
                "rows": 2,
                "placeholder": "Введите адрес",
            }
        ),
        required=False,
    )
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

###### Генерация форм
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

###### ModelForm
>[!info] Формы, построенные на основе моделей, отличаются тем, что УЖЕ имеют встроенные валидаторы полей, которые создаются на основе оганичений описанных в моделях, типа `max_length=100`  или `blank=True` и т.д.

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

Для логина есть встроенная форма
```
from django.contrib.auth.forms import AuthenticationForm
# UserChangeForm форма для обновления пользовательских данных
# UserCreationForm для формы регистрации

class UserLoginForm(AuthenticationForm):
    username = forms.CharField()        # переопределённое поле из родит. класса
    password = forms.CharField()        # переопределённое поле из родит. класса
    class Meta:
        model = User
```

>[!tip] В реальной разработке имеет смысл подправить вёрстку логин-формы под те значения формы, которые ожидает `view`(Например изменение атрибутов у `input`  - `type`, `name`, `id`, `value` и т.д.). Чтобы `view` приняла данные и закинула их в вашу форму, типа `UserLoginForm`. И дальше их валидировать, проверять пользоателя, логинить его и т.д.

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
    list_editable = ["is_open",]                    #  можно измененять из списка
    search_fields = ["theme"]
    list_filter = ("is_open", "reg")
    fields = ["is_open", ("theme", "user"), "reg"]  # расположение полей в админке
    readonly_fields = ("user",)
```
Теперь эта таблица доступна в админ-панели.

###### Автогенерация slug
```
# admin.py
@admin.register(News)  
class NewsAdmin(admin.ModelAdmin):  
    prepopulated_fields = {"title": ("slug",)}    # Поле title транслитерируется
                                                  #   и заполняется поле slug
```

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

###### Инлайн отображение таблиц
Отображение в экземпляре модели(в админке) встроенного блока со связанной таблицей.
Делаем специальный класс
```
# cart.admin.py

class CartTabAdmin(admin.TabularInline):
    model = Cart
    fields = "product", "quantity", "created_timestamp"
    search_fields = "product", "quantity", "created_timestamp"
    readonly_fields = ("created_timestamp",)
    extra = 1                       # свободное поле для добавления заказов
```

И указываем в другом, уже зарегистрированном классе список `inlines`, где перечисляем инлайновые классы
```
# users.admin.py
from cart.admin import CartTabAdmin

@admin.register(User)
class UserAdmin(admin.ModelAdmin):
    list_display = ["username", "first_name", "last_name", "email"]
    search_fields = ["username", "first_name", "last_name", "email"]

    inlines = [CartTabAdmin,]
```

###### Кнопка смотреть на сайте
На страницу экземпляра модели в админке можно добавить кнопку, ведущую прямо на этот объект на сайте. Для этого надо реализовать метод `get_absolute_url()` в модели. Этот метод также можно использовать и в шаблонах для получения URL ссылки на объект.
```
# models.py
from django.urls import reverse

class MModel(models.Model):
    ...
    def get_absolute_url(self):  
        return reverse("model_detail", kwargs={"model_id": self.id})
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
        return mark_safe(f"<img src='{object.photo00.url}' width=70>")  # возвращаем html-код(mark
                                                    # safe не позволяет экранировать тэги)

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

Есть два типа методов в ORM:
1. Методы, которые возвращают QuerySet. Их можно компоновать
```
Order.objects.filter(order_name = 'Anna').exclude(status='finished').filter(order_price=100)
```
2. Методы, которые НЕ возвращают QuerySet
```
Order.objects.get(id=32)
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

    def get_dir(self, filename):
        dir_name = sha256(f'{self.name}{self.user.username}'.encode('utf-8')).hexdigest()  
        return f"models/{dir_name}/{filename}"

    class Meta:
        abstract = True

class Order(models.Model): 
    # Если унаследовать от TimestampedModel, то будет 2 дополнительных поля:
    # дата создания и дата обновления
    order_dt = models.DateTimeField(auto_now=True)
    order_name = models.CharField(max_length=200)
    order_phone = models.CharField(max_length=200)
    file = models.FileField(upload_to=get_dir, default=None, blank=True, verbose_name='Файл модели')
    priсe = models.DecimalField(max_digits=7, decimal_places=2) # Число с запятой(всего цифр - 7,
                                                                #  из них после запятой 2)
    email = models.EmailField(unique=True)
    description = models.TextField('Описание')
    timestamp = models.DateTimeField(auto_now_add=True)
    body = models.TextField(null=True)                      # необязательное поле
    slug = models.SlugField(max_length=50)                  # поля для слагов(часть URL)
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

>[!info] Также можно создавать методы для моделей. Их можно использовать, например в шаблонах, обращаясь как к обычному полю объекта(через точку).

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

###### Создание класса для управления QuerySet
Допустим уже есть модель `Cart` с функцией `products_price()`, которая возвращает стоимость корзины.
У пользователя может быть несколько таких корзин.

Дописывем в модели `Cart`
```
objects = CartQuerySet().as_manager()
```

И создаём класс для `QuerySet`
```
# models.py
from django.db import models

class CartQuerySet(models.QuerySet):  
    def total_price(self):  
        return sum([cart.products_price() for cart in self])  
```

Далее создаём [[Django, DRF#Свои теги в шаблонизаторе|свой шаблонный тег]]. Создаём в приложении папку `templatetags` и в ней `__init__.py` и например `cart_tag.py`
```
from django import template 
from cart.models import Cart
  
register = template.Library()   
  
@register.simple_tag()  
def user_carts(request):                        
    return Cart.objects.filter(user=request.user)
```

В шаблоне
```
{% load cart_tag %}                        # имя модуля со своим тегом

{% user_carts request as carts %}          # имя функции для тега, аргумент, возврат
                                           #   результата в переменную carts

{% for cart in carts %}                    # обходим в цикле QuerySet
    <p>{{ cart.product.name }}</p>         # получаем поле экземпляра модели
    <p>{{ cart.quantity }}</p>
    <p>{{ cart.product.sell_price }}</p>
{% endfor %}
    <p>{{ carts.total_price }}</p>         # получаем результат функции QuerySet
```


###### Миграции
>[!info] Миграции — это описания изменений в наших моделях, и в дальнейшем в схеме базы данных. Мы можем просмотреть эти файлы миграций. Они располагаются в директориях migrations внутри приложений. 

- Посмотреть сам SQL-запрос можно так: `python manage.py sqlmigrate article 0001`
- Откатиться к предыдущей миграции можно указав её: `python manage.py migrate article 0005`
- Если нам нужно отменить все миграции этого приложения, нужно указать zero в качестве версии миграции: `python manage.py migrate article zero`

###### Интерактивная консоль Django и  методы ORM
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

# Создание пользователя
user = User.objects.create_user(username='username', email='email@example.com', password='password')
# Сохранение пользователя
user.save()

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
# Велосипеды дешевле 50 или содержат в поле "description" слово "горный"
Bicycle.objects.filter(price__lt=50) | Bicycle.objects.filter(description__contains='горный'
```

###### Транзакции
>[!info] Для того, чтобы пометить фрагмент кода, как относящийся к одной транзакции, обычно используют _менеджер контекста_ atomic()

```
from django.db import transaction

with transaction.atomic():
   # весь код в этом блоке выполнится в рамках транзакции
   do_more_stuff()
```

В результате при первой же ошибке транзакция отменяется.
Разработчики на Django часто хотят выполнить в транзакции весь код какой-либо `view`. При этом `atomic()` используют как декоратор

```
from django.db import transaction

@transaction.atomic
def viewfunc(request):
    # Вся view работает в рамках транзакции
    do_stuff()
```

###### Select For Update
>[!info] Используется для выполнения SELECT запроса с блокировкой строк базы данных. Это гарантирует, что строки, выбранные в результате запроса, будут заблокированы для других транзакций до завершения текущей транзакции.

Пример
```
from django.db import transaction
from account.models import Profile

with transaction.atomic():
    Profile.objects.select_for_update().get(uid='dUKzzQvx7nuGHQszA8xdkC')
    Profile.objects.select_for_update().filter(uid=uid)
```

###### Q объекты
https://django.fun/docs/django/5.0/topics/db/queries/#complex-lookups-with-q-objects
==Q объект== - это объект, используемый для инкапсуляции набора ключевых аргументов.
- Объекты `Q` можно объединять с помощью операторов `&`, `|` и `^`. Когда оператор используется для двух объектов `Q`, получается новый объект `Q`. Например, этот оператор выдает один объект `Q`, который представляет «OR» двух запросов `"question__startswith"`
```
Q(question__startswith="Who") | Q(question__startswith="What")

# эквивалент
# WHERE question LIKE 'Who%' OR question LIKE 'What%'
```

Пример  использования Q объектов
```
from django.db.models import Q

Products.objects.filter(Q(name__contains='диван') | Q(description__contains='диван')
```

Ещё пример
```
def q_search(query):
    keywords = [word for word in query.split() if len(word) > 2]  # обрезаем предлоги

    q_objects = Q()

    for token in keywords:
        q_objects |= Q(description__icontains=token)  # объединяем
        q_objects |= Q(name__icontains=token)

    return Products.objects.filter(q_objects)
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
    # related_name='books' означает, что будет доступно Author.books
    author = models.ForeignKey(Author, related_name='books', on_delete=models.CASCADE)

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

###### Aggregate
Агрегатные операции позволяют получить одно значение, подсчитанное на наборе данных по определенному критерию. 
- Avg
- Min
- Max
- Sum
```
from .models import Person
from django.db.models import Avg, Min, Max, Sum

# средний возраст
avg_age = Person.objects.aggregate(Avg("age"))

# минимальный возраст
min_age = Person.objects.aggregate(Min("age"))

# максимальный возраст
max_age = Person.objects.aggregate(Max("age"))

# сумма всех возрастов
sum = Person.objects.aggregate(Sum("age"))
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
Также имеется директива `through`, т.е. `models.ManyToManyField(Course, through='CourseStudentRealtion')`. Тоесть мы напрямую указываем существующую таблицу, в которой связваются отношнения и соответственно другая промеж. таблица НЕ будет создана.
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

#### Django debug toolbar
```
pip install django-debug-toolbar
```
Инструкция по установке:
https://django-debug-toolbar.readthedocs.io/en/latest/installation.html
Только в `urls.py` добавляем
```
from .settings import DEBUG
...
if DEBUG:  
    urlpatterns += [path("__debug__/", include("debug_toolbar.urls")),]
```

###### Инспектирование запросов через shell
```
python3 manage.py debugsqlshell

from mint_app.models import MModel
MModel.objects.all()
```


#### Fixtures
>[!info] **Fixtures** это наборы данных, которые Django может прочитать и загрузить в свою базу данных.

Fixtures также можно использовать или создавать для хранения существующих данных. Итак, по сути, fixtures это способ для Django экспортировать и импортировать данные в базу данных. Есть пакеты, которые могут в этом помочь, например **django-seed**

###### Чтобы выгрузить Fixtures из базы данных:
1. Создадим папку `fixtures` в корне проекта и в ней папку с названием приложения, например `mint_app`
2. Пишем в терминале
```
python3 manage.py dumpdata mint_app.MModel > fixtures/mint_app/mmodel.json
```
И на выходе получаем JSON с данными MModel модели

###### Чтобы загрузить Fixtures в базу данных:
1. Создадим папку `fixtures` в корне проекта и в ней папку с названием приложения, например `mint_app`
2. В settings.py добавим
```
FIXTURE_DIRS = [
    'fixtures',
]
```
И пишем в терминале
```
python3 manage.py loaddata location/to/your/data.json
# или
python3 manage.py loaddata data.json
```

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

Ещё пример
```
if DEBUG:  
    STATICFILES_DIRS = [  
        os.path.join(BASE_DIR, 'static')  
    ]  
else:  
    STATIC_ROOT = os.path.join(BASE_DIR, 'static')
```

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

>[!tip] Также одна `view` может обрабатывать несколько URL-адресов сразу

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
# также вместо конвертера int можно использовать str или например slug
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

#### СЕССИИ
###### ПОЛУЧЕНИЕ
```
s_key = request.session.session_key

```

###### СОЗДАНИЕ СЕССИИ
```
request.session.create()
```

###### УСТАНОВКА
```
request.session['member_id'] = 12345
```

###### УДАЛЕНИЕ
```
try:
    del request.session['member_id']
except KeyError:
    pass
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
Django будет искать `404.html` в директории `templates`

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
from django.contrib import auth
user = auth.authenticate(username=username, password=password)
if user:
    auth.login(request, user)
```

###### Logout
```
auth.logout(request)
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

###### Проверка на уровне функции, что пользователь залогинен
```
from django.contrib.auth.decorators import login_required

@login_required
def profile(request):
   ...
```

Надо добавить в `settings.py`
```
LOGIN_URL =  "/путь/к/странице/с/логином"
```

И во `view` с логином добавим
```
if request.GET.get('next', None):
    return HttpResponseRedirect(request.GET.get('next'))
```

Теперь `view` с `@login_required` будет перенаправлять на страницу логина, а затем обратно

###### Миксин `LoginRequiredMixin`
>[!info] При использовании представлений на основе классов вы можете добиться того же поведения, что и `login_required` при использовании `LoginRequiredMixin`

```
from django.contrib.auth.mixins import LoginRequiredMixin
from django.shortcuts import render
from django.views.generic import View

class ProfileView(LoginRequiredMixin, View):
	def get(self, request):
		return render(request, 'profile.html')
```

#### Регистрация
>[!info] Можно воспользоваться такой регистрацией https://pypi.org/project/django-registration/

Можно наследоваться от встроенного `User`. Удаляем из БД таблицы `auth`
```
# models.py
from django.db import models
from django.contrib.auth.models import AbstractUser

class User(AbstractUser):
    image = models.ImageField(upload_to='img', blank=True, null=True)
```

В `settings.py`
```
AUTH_USER_MODEL = 'users.User'
```

Производим и применяем миграцию
```
python manage.py makemigrations
python manage.py migrate
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
from django.core.cache import cache

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

#### Логгирование
###### Django
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

###### nginx
```
sudo less /var/log/nginx/access.log
sudo less /var/log/nginx/error.log
```

Получение списка уникальных адресов из `/var/log/nginx/access.log` кроме `127.0.0.1`
```
grep -oE "\b([0-9]{1,3}\.){3}[0-9]{1,3}\b" /var/log/nginx/access.log | awk '$1 != "127.0.0.1" { print }' | sort | uniq > ips.txt
```

###### gunicorn
Путь логов gunicorn обычно указывается в `--access-logfile` или `--error-logfile`. По умолчанию gunicorn логов не ведёт. Смотреть в конфиграции юнита
```
nano /etc/systemd/system/my_service.gunicorn.service
```

###### System access log
```
sudo less /var/log/auth.log
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

###### Авторизация через VK ID
[Документация](https://id.vk.com/about/business/go/docs/ru/vkid/latest/methods)
[Мои приложения VK](https://vk.com/apps?act=manage)


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

#### Поиск на сайте
https://docs.djangoproject.com/en/5.0/ref/contrib/postgres/search/
Встроенный в Django полнотекстовый поиск
В `settings.py`
```
INSTALLED_APPS = [  
    ... 
    'django.contrib.postgres',
]
```

Делаем свою функцию
```
from django.contrib.postgres.search import SearchVector, SearchQuery, SearchRank

def q_search(query):
    vector = SearchVector("name", "description")   # создаём вектор по двум полям
    query = SearchQuery(query)                     # преобразуем в векторное представление
    
    return Products.objects.annotate(rank=SearchRank(vector, query)).filter(rank__gt=0).order_by("-rank")
    # возвращаем аннотированный QuerySet с отсортированными результатами по степени похожести,
    #  с рангом выше 0
```


#### Фильтры для поиска на сайте
Пишем форму с чекбоксами/радио-кнопками.  Во view принимаем GET-параметры и определяем QuerySet
```
# views.py

def catalog(request):
    page = request.GET.get('page', 1)
    on_sale = request.GET.get('on_sale', None)         # скидка
    order_by = request.GET.get('order_by', None)       # порядок сортироки
    
    goods = Products.objects.all()

    if on_sale:
        goods = goods.filter(discount__gt=0)

    if order_by:
        goods = goods.order_by(order_by)

    return render(request, 'catalog.html', context={'goods': goods})
```

>[!tip] Чтобы пагинация работала вместе с фильтрами, можно сделать свой [[Django, DRF#Свои теги в шаблонизаторе|тэг]] для шаблонизатора, в функции которого пробрасывать GET-параметры на следующую страницу.

#### Bootstrap
###### Подключение
1. Через CDN(может тормозить загрузку стрниц)
  [[https://getbootstrap.com/docs/5.3/getting-started/introduction/]] 
3. Скачать файлы в свой проект в  директорию `static`
[[https://getbootstrap.com/docs/5.3/getting-started/download/]]
5. Через python-модуль
Например `pip install django-bootstrap5`

#### SEO
**Search Engine Optimization**
###### Читаемые слаги(slug)
Добавляем в модель поле `slug`
```
# models.py
class Example(models.Model):
    title = models.CharField(max_length=255)
    description = models.TextField()
    slug = models.SlugField(max_length=255, unique=True, db_index=True)

    def get_absolute_url(self):                 # Прямая ссылка на модель на сайте
        return reverse("example", kwargs={"slug": self.slug})

    def save(self, *args, **kwargs):
        if not self.slug:
            self.slug = slugify(self.title)
        return super().save(*args, **kwargs)
```
```
# admin.py
from django.contrib import admin
from .models import Example

class ExampleAdmin(admin.ModelAdmin):  
    list_display = ('title', 'description', 'slug')
    prepopulated_fields = {"slug": ("title",)}    # Поле title транслитерируется
                                                  #   и заполняется поле slug
```

###### Robots.txt
>[!info] Должен быть доступен по адресу <домен>/robots.txt (например mint-coast.ru/robots.txt)
Запрещает сканировать определённые части сайта. Создаём `templates/mint_app/robots.txt`
```
# templates/mint_app/robots.txt
User-agent: *
Disallow: /admin/
Disallow: /accounts/
```
Добавляем маршрут
```
# urls.py
from django.urls import path, include, re_path  
from django.views.generic import TemplateView

urlpatterns = [
    ...
    re_path(r'^robots\.txt$', TemplateView.as_view(template_name="mint_coast/robots.txt", content_type="text/plain")),
    ]
```

###### Карта сайта
В `settings.py` добавляем
```
SITE_ID = 1

INSTALLED_APPS = [ 
    # ... 
    'django.contrib.sites', 
    'django.contrib.sitemaps', 
]
```
```
python manage.py migrate
```

Создаём в приложении `sitemaps.py`
```
from django.contrib.sitemaps import Sitemap
from .models import Post

class PostSitemap(Sitemap):
    changefreq = 'weekly'                # частота изменения страниц с постами
    priority = 0.9                       # релевантность на сайте (максимальное значение — 1)
    
	def items(self):                     # возвращает QuerySet объектов для включения в базу данных
	    return Post.published.all()      # Django вызывает метод `get_absolute_url()` для
	                                     #    каждого объекта для получения URL
	    
	def lastmod(self, obj):              # получает каждый объект, который вернул метод items()
	    return obj.updated               #    и в свою очередь возвращает дату, когда тот
	                                     #    последний раз изменился
```

Добавляем в `urls.py`
```
from django.contrib.sitemaps.views import sitemap
from blog.sitemap import PostSitemap

sitemaps = { 'posts': PostSitemap, }

urlpatterns = [
    path('sitemap.xml', sitemap, {'sitemaps': sitemaps},
        name='django.contrib.sitemaps.views.sitemap')
]
```

###### Кастомные страницы 404 и 500
>[!info] Работает только если DEBUG = False
>Для этого в `settings.py` изменяем `DEBUG = False` и `ALLOWED_HOSTS = ['*']`

- Создаём в `templates` шаблоны и называем их `404.html` и `500.html`
- В `urls.py`
```
from django.conf.urls import handler404

handler404 = 'mint_app.views.page_not_found_view'
```
- Создаём view
```
def page_not_found_view(request, exception):
    return render(request, '404.html', status=404)
```

###### Мета данные
>[!info] Размещаются в шапке

```
<Title>Заголовок</Title>
<meta name="description" content="Описание страницы" />
<meta name="keywords" content="Ключевые слова" />
```

###### Кэширование шаблонов
>[!info] Работает только если DEBUG = False
>Для этого в `settings.py` изменяем `DEBUG = False` и `ALLOWED_HOSTS = ['*']`

В `settings.py`
```
TEMPLATES = [  
    {  
        'BACKEND': 'django.template.backends.django.DjangoTemplates',  
        'DIRS': [BASE_DIR / 'templates'],  
        'APP_DIRS': True,  
        'OPTIONS': {  
            'context_processors': [  
                'django.template.context_processors.debug',  
                'django.template.context_processors.request',  
                'django.contrib.auth.context_processors.auth',  
                'django.contrib.messages.context_processors.messages',
            ],  
            'loaders': [
                ('django.template.loaders.cached.Loader', [
                    'django.template.loaders.filesystem.Loader',
                    'django.template.loaders.app_directories.Loader',
                    ])
            ]
        },  
    },  
]
```

###### Компрессоры CSS и JS
>[!info] Смысл  в том, чтобы подгружался единый файл со стилями и javascript кодом
>Для этого можно использовать `Django Compressor` или упаковщики типа WebPack.


## Django REST Framework(DRF)
![[drf.png]]
>[!info] Позволяет посредством API выдавать данные, например в JSON. Таким образом рендеринг страницы происходит не на стороне сервера, а на стороне клиента.

```
pip install djangorestframework
pip install markdown                  # Markdown support for the browsable API.
pip install django-filter             # Filtering support
```

###### Пример Hello, world
```
# нужно зарегистрировать rest_framework в settings.py и создать маршрут в urls.py
# views.py
from rest_framework.views import APIView           # базовый класс для представлений DRF
from rest_framework.response import Response

class MyAPIView(APIView):
    def get(self, request):
        return Response({'title': 'Bill Gates'})   # просто json-строка
```

###### Реальный пример
В `settings.py` регистрируем:
```
INSTALLED_APPS = [
    ...
    'rest_framework',
]
```

Допустим у нас уже есть модель `Book`.
Создаём сериализатор(Создаём файл `serializers.py `в приложении):
```
from rest_framework.serializers import ModelSerializer
from store.models import Book

# классы ModelSerializer просто являются синтаксическим сахаром для создания
#  классов сериализаторов
class BooksSerializer(ModelSerializer):
    class Meta:
        model = Book
        fields = '__all__'
```

>[!info] Класс ViewSet создаётся вместо набора представлений(CRUD)

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

>[!tip] Чтобы определить дефолтный формат и не писать в url `format=json`, нужно добавить в `settings.py`:
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

#### Views
###### Generic views
[Generic views(Общие представления)](https://ilyachch.gitbook.io/django-rest-framework-russian-documentation/overview/navigaciya-po-api/generic-views)
```
from rest_framework import generics

class MyView(generics.CreateAPIView):
    queryset = User.objects.all()
    serializer_class = UserSerializer
    permission_classes = [IsAdminUser]
```

|класс|описание|
|-------|-------------|
|CreateAPIView|Создание данных по POST запросу|
|ListAPIView|Чтение списка данных по GET запросу
|RetrieveAPIView|Чтение записи по GET запросу
|DestroyAPIView|Удаление записи по DELETE запросу
|UpdateAPIView|Изменение записи по PUT или PATCH запросу
|ListCreateAPIView|Чтение по GET запросу и создание списка по POST запросу
|RetrieveUpdateAPIView|Чтение и изменение записи по GET и POST запросу
|RetriveDestroyAPIView|Чтение по GET и удаление по DELETE отдельной записи
|RetrieveUpdateDestroyAPIView|Чтение, изменение и удаление отдельной записи(GET, PUT, PATCH, DELETE)

Пример базового представления
```
# views.py
from rest_framework.views import APIView           # базовый класс для представлений DRF
from rest_framework.response import Response
from django.forms import model_to_dict

class MyAPIView(APIView):
    def get(self, request):
        lst = Women.objects.all().values()
        return Response({'posts': list(lst)})      # просто json-строка

    def post(self, request):
        post_new = Women.objects.create(           # создаём новый объект
            title=request.data['title'],           # коллекция data в DRF - это замена POST
            content=request.data['content'],
            cat_id=request.data['cat_id']
        )

        return Response({'post': model_to_dict(post_new)})  # возвращаем json-строку с новым 
                                        # объектом, конвертированным в словарь(а потом в json)
```

###### Viewsets
[Viewssets(Наборы)](https://ilyachch.gitbook.io/django-rest-framework-russian-documentation/overview/navigaciya-po-api/viewsets)
Действия ViewSet
```
def list(self, request):
    pass

def create(self, request):
    pass

def retrieve(self, request, pk=None):
    pass

def update(self, request, pk=None):
    pass

def partial_update(self, request, pk=None):
    pass

def destroy(self, request, pk=None):
    pass
```

**ModelViewSet**
```
from rest_framework.viewsets import ModelViewSet

class MyViewSet(ModelViewSet):
    queryset = User.objects.all()
    serializer_class = UserSerializer
```

**ReadOnlyModelViewSet**

#### Роутеры
>[!info] Связывают методы(GET, POST и тд) с методами ViewSet(list, create, retrieve и тд).
> Без них бы пришлось писать `path('api/article/', ArticleViewSet.as_view({'get': 'list'}))`

```
from rest_framework import routers
from .models import Article

router = routers.SimpleRouter
router.register(r'article', ArticleViewSet)  # имя в маршруте возьмётся не отсюда, а из модели
                                             # изменить можно добавив аргумент basename=article22
urlpatterns = [
    path('admin/', admin.site.urls),
]
urlpatterns += router.urls
```

###### DefaultRouter
Обрабатывет дефолтный путь, типа `http://127.0.0.1:8000/api/`

 ###### Добавление нового маршрута в роутере
```
# views.py
from rest_framework.decorators import action
from rest_framework.viewsets import ModelViewSet

class ArticleViewSet(ModelViewSet):
    queryset = Article.objects.all()
    serializer_class = ArticleSerializer

    @action(methods=['get'], detail=False)  # detail=False возвращает список, а не одну запись
    def category(self, request):           # если detail=True, то нужен ещё аргумент pk
        cats = Category.objects.all()
        return Response({'cats': [c.name for c in cats]})

# теперь появится ещё один маршрут /article/category/ куда будет отдаваться список категорий
```

#### Сериализаторы
>[!info] Превращают объекты Python и Django в JSON

###### Процесс работы сериализатора
Кодирование сериализатором объекта:
- Помещаем объект в сериализатор, он получает атрибут `data`(словарь из например модели)
- Объект сериализации превращаем в байтовую json строку

Декодирование сериализатором объекта:
- Получаем байтовую json строку и формируем словарь
- Помещаем в сериализатор этот словарь
- У объекта сериализации вызывем метод `is_valid()`
- У объекта сериализации  появляется коллекция `validated_data`(упорядоченный словарь)

Пример базового сериализатора
```# serializers.py
from rest_framework import serializers

class ArticleSerializer(serializers.Serializer):
    title = serializers.CharField(max_length=255)
    content = serializers.CharField()
    time_create = serializers.DateTimeField()
    cat_id = serializers.IntegerField()
```
```
# views.py
from rest_framework.views import APIView           # базовый класс для представлений DRF
from rest_framework.response import Response    # преобразует данные в байтовую json строку
from .serializers import ArticleSerializer
from .models import Article

class MyAPIView(APIView):
    def get(self, request):
        articles = Article.objects.all()
        return Response({'posts': ArticleSerializer(articles, many=True).data})

    def post(self, request):
        serializer = ArticleSerializer
        serializer.is_valid(raise_exception=True)
        
        post_new = Article.objects.create(          # создаём новый объект
            title=request.data['title'],            # коллекция data в DRF - это замена POST
            content=request.data['content'],
            cat_id=request.data['cat_id']
        )

        return Response({'post':  ArticleSerializer(post_new).data})
```

###### Методы сериализаторов
**create(self, validated_data)** создание записи
```
from rest_framework import serializers

class ArticleSerializer(serializers.Serializer):
    title = serializers.CharField(max_length=255)
    content = serializers.CharField()
    time_create = serializers.DateTimeField()
    cat_id = serializers.IntegerField()

    def create(self, validated_data):   # чтобы вызвался этот метод, нужно в представлении
                                        #   вызвать метод объекта сериализации .save()
        return Article.objects.create(**validated_data)
    
```
**update(self, instance, validated_data)** изменение записи
```
# Во view должен быть метод put или patch
def update(self, instance, validated_data):
    instance.title = validated_data.get("title", instance.title)
    instance.save()
    return instance
```

###### ModelSerializer
Не нужно вручную указывать все поля
```
from rest_framework.serializers import ModelSerializer

class UserSerializer(ModelSerializer):
    class Meta:
        model = User
        fields = ('id', 'username', 'first_name', 'last_name', 'email', 'models_count')
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
###### Встроенные разрешения
```
# settimgs.py
REST_FRAMEWORK = {
    'DEFAULT_PERMISSION_CLASSES': [
    'rest_framework.permissions.DjangoModelPermissionsOrAnonReadOnly'
    ]
}
```

###### Собственные разрешения
Создание собственных разрешений. Может понадобится, если удалять, обновлять может только юзер, ассоциированный с записью. Например владелец книги.

Создадим в приложении `permissions.py`
```
from rest_framework.permissions import BasePermission, SAFE_METHODS

class IsOwnerOrReadOnly(BasePermission):
   def has_object_permission(self, request, view, obj):  # т.е. работет на те view, которые 
                                                         #  привязаны к какому-то объекту
        return bool(
            request.method in SAFE_METHODS or
            request.user and
            request.user.is_authenticated and obj.owner == request.user
        )
# есть ещё метод has_permission(self, request, view) который влияет на view не привязанный к объекту(список)
```
*Не забудьте импорировать класс во ViewSet*

#### Авторизация
###### Session-based authentication
В `settings.py`
```
REST_FRAMEWORK = {  
    'DEFAULT_AUTHENTICATION_CLASSES': [  
        'rest_framework.authentication.SessionAuthentication',  
        'rest_framework.authentication.BasicAuthentication',  
    ],  
}
```

Добавляем в `urls.py`
```
urlpatterns = [  
    ...
    path('api/auth/', include('rest_framework.urls')),  # путь пишем любой
    ...
]
# теперь доступны два адреса api/auth/login и api/auth/logout
```

###### Простая авторизация по токену(Djoser)
```
pip install djoser
```
```
# settings.py
INSTALLED_APPS = [  
    ...
    'rest_framework.authtoken',
    'djoser',
]
REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': (
        'rest_framework.authentication.TokenAuthentication',
        (...)
    ),
}
```
Добавляем в `urlpatterns`
```
path(r'api/auth/', include('djoser.urls')),  
re_path(r'^auth/', include('djoser.urls.authtoken')),  # http://127.0.0.1:8000/auth/token/login
```

>[!info] [Базовые эндпоинты для управления пользователями](https://djoser.readthedocs.io/en/latest/base_endpoints.html)

Отправляем POST запрос с полями `username` и `password` на `/auth/token/login` и в ответ получаем токен. Например `"auth_token": "911bff0261de434eededec94d587652e25237f00"`
Чтобы получить доступ к ограниченному контенту, нужно в заголовке слать
`Authorization=Token 911bff0261de434eededec94d587652e25237f00`
Для logout тоже нужно слать токен в заголовках.

###### Использование JWT-токенов
>[!info] `JSON Web Token (JWT)` — это _JSON_ объект, который определен в открытом стандарте [RFC 7519](https://tools.ietf.org/html/rfc7519). Он считается одним из безопасных способов передачи информации между двумя участниками. Для его создания необходимо определить заголовок (header) с общей информацией по токену, полезные данные (payload), такие как id пользователя, его роль и т.д. и подписи (signature).

Простыми словами, _JWT_ — это лишь строка в следующем формате `header.payload.signature`.

![[jwt-structure.png]]

>[!warning] Важно! Расшифровать токен может кто угодно (например, на сайте [jwt.io](http://jwt.io/)). Поэтому ни в коем случае нельзя передавать в нем компрометирующую информацию: чувствительные данные пользователей, пароли и прочее.

Сигнатура создаётся так
```
signature = HMAC_SHA256(secret, base64urlEncoding(header) + '.' + base64urlEncoding(payload))
```

Почему такой формат токена гарантирует нам сохранность данных и невозможность их подмены? Фокус в том, что для проверки подлинности токена серверу достаточно взять из него `header` и `payload`,  добавить к ним секретныую строку и получить по ним `signature` по алгоритму выше и сравнить сигнатуру с той, что реально присутствует в токене.

Недобросовестный пользователь решил докинуть лишнего в свой токен или поменять юзера, которому он был выдан? Токен будет признан недействительным из-за несовпадения фактической и посчитанной signature, запрос будет отклонен сервером.

Сценарий использования
- пользователь регистрируется и получает пару токенов: `access` и `refresh`;
- все свои запросы он сопровождает access-токеном и получает ответ "(как раньше, с обычным jwt-токеном)";
- когда срок жизни access-токена уже истек или начинает подходить к концу, пользователь (или клиентское приложение) отправляет свой refresh-токен серверу, который его отзывает и возвращает новую пару.

Что будет, если истечет refresh-токен? Пользователю будет нужно пройти авторизацию, чтобы подтвердить свою личность и получить новую пару токенов.

######  Использование `simplejwt`

[Документация](https://django-rest-framework-simplejwt.readthedocs.io/en/latest/getting_started.html)
```
pip install djangorestframework-simplejwt
```

В `settings.py`
```
INSTALLED_APPS = [
    ...
    'rest_framework_simplejwt',
    ...
]

REST_FRAMEWORK = {
    ...
    'DEFAULT_AUTHENTICATION_CLASSES': (
        ...
        'rest_framework_simplejwt.authentication.JWTAuthentication',
    )
    ...
}

SIMPLE_JWT_SIGNING_KEY = "b=72^ado*%1(v3r7rga9ch)03xr=d*f)lroz94kosf!61((9=i"  
  
SIMPLE_JWT = {  
    'ACCESS_TOKEN_LIFETIME': timedelta(minutes=30),  
    'REFRESH_TOKEN_LIFETIME': timedelta(days=3),  
    'ROTATE_REFRESH_TOKENS': False,  
    'BLACKLIST_AFTER_ROTATION': True,  
  
    'ALGORITHM': 'HS256',  
    'SIGNING_KEY': SIMPLE_JWT_SIGNING_KEY,  
    'VERIFYING_KEY': None,  
    'AUDIENCE': None,  
    'ISSUER': None,  
  
    'AUTH_HEADER_TYPES': ('Bearer',),  
    'USER_ID_FIELD': 'id',  
    'USER_ID_CLAIM': 'user_id',  
  
    'AUTH_TOKEN_CLASSES': ('rest_framework_simplejwt.tokens.AccessToken',),  
    'TOKEN_TYPE_CLAIM': 'token_type',  
  
    'JTI_CLAIM': 'jti',  
  
    'SLIDING_TOKEN_REFRESH_EXP_CLAIM': 'refresh_exp',  
    'SLIDING_TOKEN_LIFETIME': timedelta(minutes=50),  
    'SLIDING_TOKEN_REFRESH_LIFETIME': timedelta(days=5),  
}
```

В `urls.py`
```
from rest_framework_simplejwt.views import TokenObtainPairView, TokenRefreshView, TokenVerifyView

urlpatterns = [  
    ...
    path('api/token/', TokenObtainPairView.as_view(), name='token_obtain_pair'),  
    path('api/token/refresh/', TokenRefreshView.as_view(), name='token_refresh'),  
    path('api/token/verify/', TokenVerifyView.as_view(), name='token_verify'),
]  
urlpatterns +=
```

- Получение пары токенов: POST запрос с полями `username`, `password` и заголовками `Content-Type: application/json` на `http://localhost:8000/api/token/`
- Доступ ко view защищённому аутентификацией: с заголовком `Authorization: Bearer <access token>`
- Когда короткоживущий `access` токен уже не действителен, используем `refresh` токен для 
получения нового `access` токена:  POST запрос с полем `refresh` и заголовками `Content-Type: application/json` на `http://localhost:8000/api/token/refresh/`

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

#### Пагинация
Добавляем в `settings.py`
```
REST_FRAMEWORK = {
    'DEFAULT_PAGINATION_CLASS': 'rest_framework.pagination.LimitOffsetPagination',
    'DEFAULT_RENDERER_CLASSES': `rest_framework.pagination.PageNumberPagination`,
    'PAGE_SIZE': 100
}
```
В ответ будем получать
```
HTTP 200 OK
{
    "count": 1023,
    "next": "https://api.example.org/accounts/?page=5",
    "previous": "https://api.example.org/accounts/?page=3",
    "results": [
        ...
    ]
}
```

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

#### CORS(Cross-Origin Resource Sharing)
>[!info] Cross-Origin Resource Sharing (CORS) — механизм, использующий дополнительные HTTP-заголовки, чтобы дать возможность агенту пользователя получать разрешения на доступ к выбранным ресурсам с сервера на источнике (домене), отличном от того, что сайт использует в данный момент.

В целях безопасности браузеры ограничивают cross-origin запросы, инициируемые скриптами. Например, XMLHttpRequest и Fetch API следуют _политике одного источника_. Это значит, что web-приложения, использующие такие API, могут запрашивать HTTP-ресурсы только с того домена, с которого были загружены, пока не будут использованы CORS-заголовки.

Включение заголовков
```
pip install django-cors-headers
```

```
INSTALLED_APPS = (
    ...
    'corsheaders',
    ...
)
```

```
MIDDLEWARE = [
    ...,
    'corsheaders.middleware.CorsMiddleware',
    'django.middleware.common.CommonMiddleware',
    ...,
]
```

```
CORS_ALLOWED_ORIGINS = [
    'http://localhost:3030',
]
```

Для всех источников
```
CORS_ALLOW_ALL_ORIGINS = True
```

#### Документирование API(Django REST Swagger)
```
pip install PyYAML
pip install uritemplate
pip install django-rest-swagger
```

[Документация Django REST Swagger](https://django-rest-swagger.readthedocs.io/en/latest/)

В `settings.py`
```
INSTALLED_APPS = [
... 
'rest_framework_swagger',
...
]
```

Добавляем в `urls.py`
```
from rest_framework.schemas import get_schema_view

urlpatterns = [
    # ...
    path('api_schema/', get_schema_view(
        title='API Schema',
        description='Guide for the REST API'
    ), name='api_schema'),
    path('docs/', TemplateView.as_view(
        template_name='docs.html',
        extra_context={'schema_url':'api_schema'}
        ), name='swagger-ui'),
]
```
Схему можно скачать по адресу [http://http//127.0.0.1:8000/api_schema
](http://http//127.0.0.1:8000/api_schema)

Создаём файл `docs.html`
```
<!DOCTYPE html>
<html>
  <head>
    <title>My Site Documentation</title>
    <meta charset="utf-8"/>
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <link rel="stylesheet" type="text/css" href="//unpkg.com/swagger-ui-dist@3/swagger-ui.css" />
  </head>
  <body>
    <div id="swagger-ui"></div>
    <script src="//unpkg.com/swagger-ui-dist@3/swagger-ui-bundle.js"></script>
    <script>
    const ui = SwaggerUIBundle({
        url: "{% url schema_url %}",
        dom_id: '#swagger-ui',
        presets: [
          SwaggerUIBundle.presets.apis,
          SwaggerUIBundle.SwaggerUIStandalonePreset
        ],
        layout: "BaseLayout"
      })
    </script>
  </body>
</html>
```

Теперь документация доступна по адресу http://127.0.0.1:8000/docs/


## PWA
>[!info] Progressive Web Application - промежуточная технология между сайтами и нативными приложениями. (косметически похожее на нативное приложение)
>
###### Основные технологии PWA
1. **Service Worker**
Это проксирующий слой между фронтэндом и бэкэндом, находящийся в браузере. Все запросы браузера идут через него. Данное разделение на два независимых слоя позволило сделать переход обычного веб сайта в PWA максимально простым.
Service Worker представляет собой javascript файл, подключаемый в html коде страницы. В нем разработчик определяет логику работы с приходящими из фронтэнда запросами и другую функциональность.

2.  **HTTPS**
PWA требует, чтобы все ресурсы сайта передавались по HTTPS протоколу.

3. **Application Shell**
App shell — это просто скелет графического интерфейса, шаблон. Данную архитектуру сайта — загрузку контента и иной динамичной информации через ajax вызовы — можно продумать и реализовать на сайте заранее, тогда переход в PWA будет совсем несложным.

4. **Web App manifest**
JSON файл, декларативно определяющий для браузера название приложения, иконку, как будет выглядеть PWA (fullscreen, standalone и др.) и некоторые другие параметры. Позволяет «установить» PWA как отдельное приложение на домашний экран смартфона.

5. **Push Notifications**
Push уведомления.

###### Шаги для создания простого PWA
>[!info] В Google Chrome можно отслеживать стадии готовности и тестировать PWA с помощью Lighthouse в инструментах разработчика.

1. Иконка
[Сайт-генератор иконок](https://www.favicon-generator.org/)
Скачиваем иконку сайта и подключаем её в html шаблоне.
```
# шапка сайта
<link  
  rel="shortcut icon"  
  href="{% static 'img/headphones.png' %}"  
  type="image/x-icon"  
/>
```

2. Манифест
Файл, содержащий данные о сайте: имя, цветовая тема, информация об иконках
[Сайт-генератор манифестов](https://tomitm.github.io/appmanifest/)
Пример `manifest.json`
```
{  
  "name": "MyMusic",  
  "description": "Music is free for all",  
  "start_url": "/",  
  "lang": "ru-RU",  
  "display": "standalone",  
  "icons": [  
    {  
      "src": "static/img/headphones.png",  
      "sizes": "192x192"  
    }  
  ]  
}
```

3. Service worker
Файл, который мы добавляем в наш проект, он позволит сайту работать в автономном режиме. Наличие service worker — это также требование PWA, поэтому он необходим.
- Добавляем скрипт в наш шаблон в конец `body`
```
<script>  
    if ("serviceWorker" in navigator) {  
         window.addEventListener("load", function() {
             navigator.serviceWorker.register("/sw.js").then(
                function(registration) {
                    // Registration was successful
                    console.log("ServiceWorker registration successful with scope: ", registration.scope); },
                function(err) {
                    // registration failed :(
                    console.log("ServiceWorker registration failed: ", err);
                });
        });
    }
</script>
```
Если хочется чтобы ворекер делал что-то ещё кроме простого сохранения страниц, стоит попробовать [PWA Builder](https://www.pwabuilder.com/)

- Добавляем `sw-toolbox` в свой проект [Сам файл](https://github.com/GoogleChromeLabs/sw-toolbox/blob/master/sw-toolbox.js)
- Создаём новый файл `sw.js`
```
"use strict";  
importScripts("sw-toolbox.js");  
toolbox.precache(["/","static/css/styles.css", "static/js/main.js"]);  
toolbox.router.get("/images/*", toolbox.cacheFirst);  
toolbox.router.get("/*", toolbox.networkFirst, { networkTimeoutSeconds: 5});
```

## Celery
>[!info] Celery - это  распределенная очередь задач. Она позволяет вам выгрузить работу из вашего приложения на Python. Как только вы интегрируете Celery в свое приложение, вы можете отправлять трудоемкие задачи в очередь задач Celery. Таким образом, ваше веб-приложение может продолжать быстро реагировать на запросы пользователей, в то время как Celery асинхронно выполняет операции, требующие больших затрат в фоновом режиме.

Так как с Celery удобно работать только из докера, будет показана сборка для докера.

Для Celery нужен брокер сообщений (отдельная служба для отправки и получения сообщений). Это может быть Redis (key-value хранилище) или RabbitMQ (полноценный менеджер задач). Здесь будет показана связка с Redis.

```
pip install celery[redis]
```

Также установим `flower`(сервер, который наглядно с интерфейсом показывает таски), он будет удобен для дебага.

```
pip install flower
```

Обычный `Dockerfile` для `Django` приложения
```
FROM python:3.11  
  
COPY . /code  
WORKDIR /code  
EXPOSE 8000  
  
RUN pip install -r requirements.txt  
  
CMD python manage.py migrate \  
        && python manage.py shell -c "from django.contrib.auth import get_user_model; User = get_user_model(); User.objects.filter(username='root').exists() or User.objects.create_superuser('root', 'root@example.com', 'root')" \  
        && python manage.py collectstatic --no-input \  
        && gunicorn music_site.wsgi:application --bind 0.0.0.0:8000 --log-level in
```

В `docker-compose.yml` будут описаны все сервисы, включая `redis`, `celery` и `flower`
```
version: "3.9"  
services:  
  music_site:  
    build:  
      context: .  
    container_name: music_site  
    restart: always  
    volumes:  
      - .:/code  
      - static_volume:/code/static  
    env_file:  
      - .env  
    ports:  
      - "8000:8000"  
  
  nginx:  
    image: nginx:latest  
    container_name: nginx  
    restart: always  
    volumes:  
      - ./nginx/nginx.conf:/etc/nginx/nginx.conf  
      - static_volume:/code/static  
    ports:  
      - "80:80"  
    depends_on:  
      - music_site  
  
  redis:  
    image: redis:7.0.5-alpine  
    hostname: redis  
  
  worker:  
    build:  
      context: .  
    hostname: worker                # имя под которым будет существовать сервис в докере
    entrypoint: celery  
    command: -A celery_app.app worker --loglevel=info  
    volumes:  
      - .:/code  
    links:  
      - redis  
    depends_on:  
      - redis  
  
  flower:  
    build:  
      context: .  
    hostname: flower  
    entrypoint: celery  
    command: -A celery_app.app flower  
    volumes:  
      - .:/code  
    links:  
      - redis  
    depends_on:  
      - redis  
    ports:  
      - "5555:5555"  
  
volumes:  
  static_volume:
```

Далее создаём файл `celery_app.py` на одном уровне с `settings.py`
```
import os  
import time  
  
from celery import Celery  
from django.conf import settings  
  
  
os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'music_site.settings')  
  
app = Celery('music_site')  
app.config_from_object('django.conf:settings')  
app.conf.broker_url = settings.CELERY_BROKER_URL  
app.autodiscover_tasks()  
  

# таск для примера
@app.task()  
def debug_task():  
    time.sleep(50)  
    print('Hello from debug task!')
```

 Создаём `__init__.py` на одном уровне с `settings.py`
```
from .celery_app import app as celery_app  
  
__all__ = ('celery_app',)
```

В `settings.py` указываем
```
CELERY_BROKER_URL = 'redis://redis:6379/0'  # protocol://hostname
```

Всё. Делаем из директории с `docker-compose.yml`
```
docker-compose build
```

И запускаем
```
docker-compose up
```

---
Чтобы увидеть тестовую задачу `debug_task()` можно зайти в джанговый шелл
Заходим в контейнер
```
docker exec -it music_site /bin/bash
```

И затем в шелл
```
python manage.py shell
```

И уже в шелле пишем
```
from celery_app import debug_task
debug_task.delay()
```

Вывод можно будет увидеть в логах `worker`, а также посмотреть в интерфесе `flower` http://127.0.0.1:5555

###### Получение результатов
В `settings.py` добавляем
```
CELERY_RESULT_BACKEND = 'redis://127.0.0.1:6379/0'
```

При запуске таски, получаем `ID` и сохраняем его куда-нибудь
```
task = process_file.delay()  
task_id = task.id
```

Теперь с помощью этого `ID` можно получать статус таски и её результат
```
from celery.result import AsyncResult

task_result = AsyncResult(task_id)  
if task_result.status == 'SUCCESS' or task_result.status == 'FAILURE':  
    print(task_result.result)  
else:  
    print(task_result.status)
```

###### Таски в других приложениях в Django
В других приложениях мы описываем таски так
```
from celery import shared_task

@shared_task()
def test_task():
    pass
```

###### Celery Singleton
>[!info] Предотвращает дублирование тасок. Т.е. когда приходит таск с такими же аргументами, он её не принимает.
```
pip install celery-singleton
```

```
from celery import shared_task
from celery_singleton import Singleton

@shared_task(base=Singleton)
def test_task():
    pass
```

###### Запуск без Докера
```
pip install celery[redis]
pip install flower
```

Запуск воркера Celery
```
celery -A my_project worker
```

Запуск Flower
```
celery -A my_project flower
```

## Elasticsearch
>[!info] Движок для полнотекстового поиска с JSON REST API. По сути это масштабируемое хранилище для быстрого поиска и анализа Big Data в онлайн-режиме. На выходе мы получаем, например, нужные `id `записей БД и затем идём в БД за конкретными записями.

###### Индекс
(Можно сравнить с таблицей в реляционной БД)
Это логическое хранилище документов, которые объединены одним смыслом. Такое хранилище является по сути коллекцией, которая оптимизирована под поисковые запросы. Содержимое полей документов сканируется и сохраняется в соответствующие структуры данных

У индекса есть и физическое представление, заключающееся в том, что Elasticsearch имеет распределенные свойства и способен к горизонтальному масштабированию. Индекс может биться по шардам, а у каждого шарда могут быть реплики и т.д.

###### Документ
(Можно сравнить с записью в реляционной БД)

###### Запрос
(Можно сравнить с SQL запросом)

###### Анализатор

#### Установка и запуск через Docker
```
docker run -p 9200:9200 -e "discovery.type=single.node" elasticsearch:7.17.22
```
Теперь можно перейти в браузер `localhost:9200`

#### API Elasticsearch
###### Создание индекса
PUT-запрос `localhost:9200/first_index`
```
{
    "mappings": {
        "properties": {
            "title": {
                "type": "text",
                "analyzer": "russian"
            },
            "price": {
                "type": "float"
            },
            "available": {
                "type": "boolean"
            }
        }
    }
}
```

###### Добавление и обновление документов
PUT-запрос `localhost:9200/first_index/_doc/1`
```
{
    "title": "Беспроводные наушники",
    "price": 49.99,
    "available": true
}
```

###### Создание документов
>[!warning] Если документ уже существует, вернётся ошибка

PUT-запрос `localhost:9200/first_index/_doc/2/_create`
```
{
    "title": "Кабель USB",
    "price": 3.99,
    "available": true
}
```

###### Добавление нескольких документов ОДНИМ запросом
POST-запрос `localhost:9200/first_index/_bulk`
```
{"index": {"_index": "first_index", "_id": 100}}
{"title": "Зарядная станция", "price": 273.99, "available": true}
{"index": {"_index": "first_index", "_id": 101}}
{"title": "Кулер для процессора", "price": 70.99, "available": true}

```
>[!warning] Последняя строка должна быть пустой!


###### Получение документов
GET-запрос `localhost:9200/first_index/_doc/1`

###### Удаление документа
DELETE-запрос `localhost:9200/first_index/_doc/1`

###### Поиск
**Не конкретизированный поиск**
GET-запрос `localhost:9200/first_index/_search`

**Поиск без конкретного запроса, но со сдвигом 20 и размером 3**
GET-запрос `localhost:9200/first_index/_search`
```
{
    "from": 20,
    "size": 3
}
```

**Поисковой запрос по слову 'беспроводной' в полях 'title'**
GET-запрос `localhost:9200/first_index/_search`
```
{
    "query": {
        "match": {
            "title": "беспроводной"
        }
    }
}
```

**Поисковой запрос в диапазоне цен**
GET-запрос `localhost:9200/first_index/_search`
```
{
    "query": {
        "bool": {
            "must": [
                {
                    "range": {
                        "price": {
                            "gte": 15,
                            "lte": 50
                        }
                    }
                },
                {
                    "term": {
                        "available": true
                    }
                }
            ]
        }
    }
}
```
