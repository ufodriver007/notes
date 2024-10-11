![[CSS3_logo.png|300]]
#### Подключение css стилей
###### Внешние
Подключение внешнего файла со стилями - внутрь head добавляем `<link rel="stylesheet" href="static/style.css">`
###### Встроенные
`<style></style>` встраивание css стилей

#### Выпадающее меню
```
.drop:hover .nav-list {
    display: block;
    position: absolute;
    background-color: black;
}
```

При наведении курсора на кнопку, список ссылок становится блочным(чтобы вся площадь элемента была кликабельна), абсолютное позиционирование(выдёргиваем из потока) и даём чёрный цвет фона.

#### Селекторы
В html указываем class(например `<span class="bold-text">`), а в файле css обращаемся к этому классу
```
.bold-text { font-weight: bold; }
```

обращение по id
```
#main { ... }
```

класс `main` ИЛИ класс `baby`
```
.main,
.baby { ... }
```

обращение по классу И тэгу(вес 11)
```
div.main { ... }
```

обращение ко всем элементам
```
* { ... }
```

все абзацы
```
p { ... }
```

обращение ко всем img с классом container (`>` дочерний комбинатор(прямые потомки))
```
.container > img { ... }
```

обращение ко всем span внутри p (дочерний комбинатор(все потомки))
```
p span { ... }
```

**Псевдоклассы**
элемент в фокусе
```
input:focus { ... }
```

первый потомок
```
li:first-child { ... }
```

последний потомок
```
li:last-child { ... }
```

n-потомок
```
li:nth-child(2) { ... }
```

Первый из одноуровневых p
```
p:first-of-type { font-weight: 700; }
```

Второй  из одноуровневых p
```
p:nth-of-type(2) { color: green; }
```

Последний  из одноуровневых p
```
p:last-of-type { text-decoration: underline; }
```

**Комментарий**
```
/* comment */ 
```

#### Медиа запрос
медиа запрос. стили, которые будут применяться с шириной экрана от 1 до 900px
```
@media (max-width: 900px){  

}
```

#### Вес(приоритетность правил)
|Вес|Описание|
|-----|------------|
|1|по тегу
|10|по классу
|100|по id
|1000|инлайновый стиль(в самом теге)
|бесконечно большой вес|!important (например `border-style: solid !important;`)

#### Свойства
|Свойство|Описание|
|-------|------------|
|`width: 500px;`|ширина(блочные элементы)
|`height: 50px;`|высота(блочные элементы)
|`font-size: 24px;`|размер шрифта
|`font-style: italic;`|стиль шрифта
|`font-weight: bold;`|жирный шрифт
|`background-color:green;`|задний фон
|`margin: 30px;`|внешний отступ
|`padding: 5px;`|внутренний отступ
|`padding-inline: 10px;`|внутр. отступ справа и слева
|`display: block;`|блочный элемент
|`display: inline;`|строчный элемент
|`display: inline-block;`|строчный элемент, но реагирующий на настройки высоты и ширины
|`display: grid;`|сетка
|`grid-template-columns: 1fr 1fr 1fr;`|настройка сетки на 3 колонки
|`grid-template-rows: repeat(4, 300px)`|высота 4х строк сетки - 300px
|`grid-column: 3/11;`|занять колонки с 3ей по 10(не включая 11)
|`grid-row: 3/11;`|занять строки с 3ей по 10(не включая 11)
|`text-align: center;`|выравнивание текста по центру элемента
|`display: flex;`|делает контейнер flex
|`flex-direction: row;`|выравнивание содержимого flex-контейнера в сточку
|`flex-direction: column;`|выравнивание содержимого flex-контейнера колонкой
|`align-items: center;`|выравнивание элементов flex-контейнера, если он колонкой
|`justify-content: space-between;`|свободное пространство блока разделяется между элементами
|`border-style: solid;`|рамка
|`border-width: 0.5px;`|толщина рамки
|`float: right;`|расположение в блочном контейнере справа с обтеканием
|`list-style: none;`|убрать точки элементов списка

#### Еденицы измерения
Шрифт по умолчанию = 16px, соответственно, величина 1em и 1rem = 16px
- ==em== еденица, наследуемая от родителя
- ==rem== еденица, наследуемая от главного родителя

#### Псевдоклассы
Когда наведён курсор
```
.nav-bar a:hover {
    background-color: white;
}
```

#### Кнопка с картинкой
```
<button class="image-button"></button>
```
```
.image-button {
  width: 100px;
  height: 50px;
  border: none;
  background-image: url('path/to/image.jpg');
  background-size: cover;
}
```

#### Шрифты
Идём в [Google Fonts](https://fonts.google.com/?query=roboto). Выбираем шрифт, тыкаем `get embeded code` и получаем html-код подключения файлов из CDN гугла.

#### Иконки
С помощью сайта [cdnjs.com](https://cdnjs.com/) Можно подключить практически любую библиотеку через CDN. Ищем там `fontawesome`, получаем ссылку, подключаем у себя(в шапке до css).
Далее идём на [fontawesome.com](https://fontawesome.com/search) и ищем нужную бесплатную иконку. Получаем её код и подключаем её себе.

#### Переменные
Инициализация
```
:root {
  --bg-color: #f5f5f5;
  --text-color: #6c757d;
  --accent-color: #ffd333;
  --contrast-color: #3d464d;
}
```
Использование
```
body {
  background-color: var(--bg-color);
  color: var(--text-color);
}
```

#### Bootstrap
[Сайт Bootstrap](https://getbootstrap.com/docs/5.3/getting-started/introduction/)
[Документация на русском](https://bootstrap-4.ru/docs/5.3/getting-started/introduction/)
Docs->Download->CDN копируем пути css и js

|Имя файла|Описание|
|------|------|
|`bootstrap.js`|Сама библиотека Bootstrap|
|`bootstrap.min.js`|Сжатая версия Bootstrap(без пробелов, некоторых точек с запятой и т.д.)|
|`bootstrap.bundle.js`|Bootstrap + Popper(плагин всплывающих подсказок)|
|`bootstrap.bundle.min.js`|Сжатая версия Bootstrap + Popper(плагин всплывающих подсказок)|
|`bootstrap.esm.js`| Версия Bootstrap, построенная как ESM (позволяет использовать Bootstrap в качестве модуля в браузере)|

1. В шапке указываем тег
```
<meta name="viewport" content="width=device-width, initial-scale=1">
```
Этот тег определяет, как нужно настроить параметры окна браузера для конкретного устройства, особенно это актуально для мобильного телефона.

2. Подключаем CSS и JS. Можно указать путь к CDN, а можно скачать в свой проект.
CSS
```
<link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-QWTKZyjpPEjISv5WaRU9OFeRpok6YctnYmDr5pNlyT2bRjXh0JMhjY6hW+ALEwIH" crossorigin="anonymous">
```
>[!warning] Свои стили подлючаем ПОСЛЕ подключения стилей bootstrap

JS(В конец тега body)
```
<script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/js/bootstrap.bundle.min.js" integrity="sha384-YvpcrYf0tY3lHB60NNkmXc5s9fDVZLESaAA55NDzOxhy9GkcIdslK1eN7N6jIeHz" crossorigin="anonymous"></script>
```

###### Прижатие footer вниз
**В Bootstrap**
```
<div class="container-fluid">
    <div class="row">
        <footer class="footer d-flex fixed-bottom bg-black text-white">
            FOOTER
        </footer>
    </div>
</div>
```

**Обычный вариант**
[Компоновка flex на странице](https://tproger.ru/translations/how-css-flexbox-works)
HTML
```
<div class="wrapper">
    <header class="header">Header</header>
    <main class="main">Content</main>
    <footer class="footer">Footer</footer>
</div>
```

CSS
```
html, body {
  height: 100%;
}

.wrapper {
  min-height: 100%;
  display: flex;
  flex-direction: column;
}

main.main {
  flex: 1 1 auto;
}
```
Пояснение для `flex: 1 1 auto;`.  Это включает в себя несколько параметров:
1. `flex-grow` определяет как много свободного пространства во flex-контейнере должно быть назначено текущему элементу. Т.е. 1 означает всё.
2. `flex-shrink` фактор сжатия flex-элемента. Т.е. 1 означает макс.
3. `flex-basis` задаёт базовый размер флекс элемента по основной оси(т.е. по `column`)

###### Сетка
В Bootstrap 12 колонок. Для начала добавьте в шапку 
```
<meta name="viewport" content="width=device-width, initial-scale=1" />
```
>[!info] Breakpoint - это ключевое значение ширины `viewport`

[Доступные `breakpoints`](https://getbootstrap.com/docs/5.3/layout/breakpoints/#available-breakpoints). 

|Breakpoint|Class infix|Dimensions|
|---|---|---|
|Extra small|_None_|<576px|
|Small|`sm`|≥576px|
|Medium|`md`|≥768px|
|Large|`lg`|≥992px|
|Extra large|`xl`|≥1200px|
|Extra extra large|`xxl`|≥1400px|

Сетка состоит из:
- оберточных контейнеров (элементов с классом container, container-fluid, или
container-{breakpoint));
- рядов или строк (элементов с классом row);
- адаптивных колонок или блоков, имеющих один или несколько классов col.

Типичный пример сетки
```
<div class="container">
    <div class="row>
        <div class="col">1</div>
        <div class="col">2</div>
        <div class="col">3</div>
    </div>
</div>
```

###### Контейнеры
>[!info] Контейнер - это элемент сетки Bootstrap, с которого обычно начинается создание макета страницы или ее части. Другими словами, это ба­зовый элемент, в котором необходимо размещать все другие элементы сетки (ряды и адаптивные блоки). Его основная цель - это установить шаблону ширину и выровнять его по центру страницы

Bootstrap поставляется с двумя базовыми контейнерами:
1. адаптивно фиксированный контейнер (класс container)- устанавливает максималь­ную ширину (max-width) для каждой контрольной точки;
*Для адаптивно фиксированного контейнера можно указать контрольную точку (container-{breakpoint)), при этом будет установлена максимальная ширина (width: 100%) до указанной контрольной точки.*
2. адаптивно подвижный («резиновый») контейнер (класс container-fluid), который устанавливает максимальную ширину (width: 100%) во всех контрольных точках.
```
<div class="container-fluid"> ...</div>
```
3. контейнер с указанием брейкпоинта

###### Ряды(row)
Элемент сетки страницы, который выступает в роли контейнера для адаптивных блоков (колонок).
```
<div class="row"> ... </div>
```

###### Адаптивные блоки (соl)
Адаптивные блоки располагаются только внутри ряда.
Это блоки, ширина которых на разных контрольных точках (breakpoint) может быть различной
Примеры

|Колонка|Описание|
|---|---|
|`class="col-lg-5"`|Начиная с брейкпоита `lg`(≥992px) ширина блока будет равна 5 из 12 колонок (т.е. 41.6%)|
|`class="col-xl-3"`|Начиная с брейкпоита `xl`(≥1200px) ширина блока будет равна 3 из 12 колонок (т.е. 25%)
|`class="col-xxl-6"`|Начиная с брейкпоита `xxl`(≥1400px) ширина блока будет равна 6 из 12 колонок (т.е. 50%)

**Вертикальное выравнивание блоков**

|Класс|Описание|
|---|---|
|`align-items-start`|выравнивание относительно начала ряда (по верху ряда);
|`align-items-center`|по центру ряда;
|`align-items-end`|относительно конца ряда (по низу ряда).

**Горизонтальное выравнивание блоков**

|Класс|Описание|
|---|---|
|`justify-content-start`|по левому краю
|`justify-content-center`|по центру
|`justify-content-end`|по правому краю
|`justify-content-around`|равномерно, с учетом пространства перед первым и послед­ним адаптивными блоками
|`justify-content-between`|равномерно, с одинаковым пространством между адаптив­ными блоками

###### Использование стилей, путём добавления классов
[Категория **Utilities**](https://getbootstrap.com/docs/5.3/utilities/background/)

|Класс|Описание|
|---|---|
|`py-1`|Padding по оси Y 1px|
|`ms-2`|Margin Start 2px|
|`me-5`|Margin End 5 px|
|`d-none`|Display: none|
|`d-flex`|Display: flex|
|`justify-content-end`|Выравнивание по правому краю|
|`sticky-top`|Всегда отображать блок вверху
|`sticky-bottom`|Всегда отображать блок внизу

и т.д.

Пример элемента, который занимает 6 колонок (из 12), а если ширина viewport ≥768px (md), то 4 колонки(из 12).
```
<div class="col-6 col-md-4>

</div>
```