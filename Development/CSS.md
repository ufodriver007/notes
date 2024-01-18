#### Подключение css стилей
###### Внешние
Подключение внешнего файла со стилями - внутрь head добавляем `<link rel="stylesheet" href="static/style.css">`
###### Встроенные
`<style></style>` встраивание css стилей

#### Fonts
Чтобы подключить шрифты на сайт, гуглим "google fonts css api"
В css файле
```
@import url('https://googleapis.com/css?family=Michroma&display=swap')

body {
    font-family: Michroma, serif
}
Если шрифт Michroma не удастся подгрузить, будет serif
```

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
.bold-text {
    font-weight: bold;
}
```

обращение по нескольким селекторам
```
.main,
.baby {

}
```

обращение ко всем элементам
```
* {
    marign: 0px;
}
```

обращение к классу
```
.cont {
    
}
```

обращение ко всем img с классом container
```
.conteiner>img {

}
```

обращение по id
```
#main {

}
```

обращение по классу и тэгу(вес 11)
```
div.main {

}
```

комментарий
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

#### Bootstrap
Docs->Download->CDN копируем пути css и js

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