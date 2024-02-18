![[js.jpg|300]]

Однострочный комментарий
```
// однострочный комментарий
```

Многострочный комментарий
```
/* многострочный
      комментарий */
```

Подключение JavaScript к странице
```
<script>...Скрипт перед закрывающим тегом body...</script> 
       либо
<script src="js/main.js"></script>
```

Асинхронная подгрузка внешнего скрипта
```
<script async src="js/main.js"></script>
// Т.е. будет загружаться HTML, одновременно с ним JavaScript.
// Как только файл js будет загружен, загрузка HTML прекратиться до полного выполнения js скрипта.
```


Отложенный запуск асинхронной подгрузки
```
<script defer src="js/main.js"></script>
// Т.е. будет загружаться HTML, одновременно с ним JavaScript.
// Код скрипта будет выполнен только после полной загрузки HTML
```

```
alert("Привет мир");                              //Модальное окно
console.log("Привет мир");                        //Вывод в консоль          

var username;                                     //Глобальная/функционльная область видимости
let username;                                     //Блочная область видимости; нельзя обращаться
                                                  //  к ней до момента её объявления.
var username, age, height;                        // Объявление нескольких переменных
const sum = 5 + 8;                                // Объявление константы. Должна всегда быть
                                                  //   СРАЗУ инициализирована
```

#### Типы данных
- **String**. представляет строку. Каычки любые. Встраивание выражения в строку(только косые кавычки): 
```
text = `Name: ${username}`
```
- **Number**. представляет числовое значение
- **BigInt**. предназначен для представления очень больших целых чисел
- **Boolean**. представляет логическое значение `true` или `false`
- **Undefined**. представляет одно специальное значение - `undefined` и указывает, что значение не установлено
- **Null**. представляет одно специальное значение - `null` и указывает на отсутствие значения
- **Symbol**. представляет уникальное значение, которое часто применяется для обращения к свойствам сложных объектов
- **Object**. представляет комплексный объект. Например: 	
```
const user = {};
const user = {name: "Tom", age:24};
```

>[!info] Объекты и массивы в JS - ссылочные типы

#### Значения null и undefined
- undefined указывает, что значение не определено или не установлено(смысл в том, что переменная неинициализирована).
- Присвоение значение null означает, что у переменной отсутствует значение.

JavaScript является языком со слабой и динамической типизацией. Это значит, что переменные могут динамически менять тип.
С помощью оператора `typeof` можно получить тип переменной(для значения `null` оператор `typeof` возвращает значение `"object"`):
```
id = 45;
console.log(typeof id);   // number
```

Инкремент в JS(увеличивает значение переменной на единицу): 
`x++`    Сначала возвращает значение, потом инкрементирует
`++x`    Сначала инкрементирует, потом возвращает значение
Декремент по аналогии.

Операции с присваиванием по аналогии с Python:
```
   let a = 3;
   a += 5;            // аналогично a = a + 5
   console.log(a);    // 8
```

#### Операторы сравнения
`==` сравнивает два значения
`===` сравнивает два значения и их тип
Остальное по аналогии с Python

#### Логические операции
`&&` Логическое И
`||` Логическое ИЛИ
`!` Логическое отрицание

#### Тернарная операция
```
[первый операнд - условие] ? [второй операнд] : [третий операнд]
```
```
const a = 1;
const b = 2;
const result = a < b ? a: b;      //Если a < b, возвращается второй операнд(a)
console.log(result);              // 1
```

#### Оператор ??
Оператор возвращает значение левого операнда, если оно НЕ равно `null` и `undefined`. Иначе возвращается значение правого операнда.
```
console.log(null ?? "not null");    // not null
```

#### Преобразование типов
`parseInt()`     Преобразование строки в целое число или  NaN
```
const num1 = "123hello";
const num2 = parseInt(num1);
console.log(num2); // 123
```

С помощью специальной функции `isNaN()` можно проверить, представляет ли строка число
`parseFloat()`    Для преобразования строк в дробные числа

#### Условные конструкции
###### IF
```
if(условие){    
    некоторые действия
}

if (myVar !== undefined && myVar !== null) {
    console.log(`Переменная myVar имеет значение: ${myVar}`);
}
```
###### IF-ELSE
```
if(условие){
    действия, если условие истинно
}
else{
    действия, если условие ложно
}
```
###### IF-ELSE IF-ELSE
```
const income = 50;
if(income > 50) {
    console.log("Доход больше 50");
}
else if(income === 50){
    console.log("Доход равен 50");
}
else{
    console.log("Доход меньше 50");
}
```
###### SWITCH-CASE
```
const income = 200;
switch(income){
 
    case 100 : 
        console.log("Доход равен 100");
        break;
    case 200 : 
        console.log("Доход равен 200");
        break;
    case 300 : 
        console.log("Доход равен 300");
        break;
     default: 
        console.log("Доход неизвестной величины");
        break;
}
```

#### ЦИКЛЫ
###### FOR
```
for(let i = 0; i<5; i++){
    console.log(i);
}
```

###### WHILE
```
let i = 1;
while(i <=5){
    console.log(i);
    i++;
}
```

###### DO-WHILE
```
let i = 1;
do{
    console.log(i);
    i++;
}while(i <= 5)
```

###### FOR-IN(Для перебора объектов)
```
for (свойство in объект) {
    // действия
}
```

###### FOR-OF(для перебора наборов данных(строк, массивов))
```
const text = "Hello";
for(char of text){
     console.log(char);
}
```

>[!info] Также присутствуют `continue` и `break`

#### ФУНКЦИИ
```
function hello(message, x = 0){
    console.log(message);
    console.log(x);                   // параметр по умолчанию
}
hello();                              // вызов функции
```

###### Переменное количество аргументов
```
function my_print(){
    for(const n of arguments)   // объект arguments автоматически передаваемый в функцию
        console.log(n);
}
my_print(6, 4, 5, 8);
```
Или
```
function my_print(...numbers){
    for(const n of numbers)  
        console.log(n);
}
my_print(6, 4, 5, 8);
```

###### Анонимные функции
```
const message = function(){
    console.log("Hello JavaScript");
}
message();
```

###### Стрелочные функции
```
const sum = (x, y)=> console.log("Sum =", x + y);
sum(2, 5);
```

###### Самовызывающиеся функции
```
(function(){
    console.log("Привет мир");
}());
```

#### Область видимости
- Локальная
- Глобальная
>[!info] Все переменные и константы, которые объявлены вне функций, являются глобальными.

#### Паттерн Модуль
>[!info] Паттерн "Модуль" базируется на замыканиях и состоит из двух компонентов: внешняя функция, которая определяет лексическое окружение, и возвращаемый набор внутренних функций, которые имеют доступ к этому окружению.

```
let calculator = (function(){
    let data = { number: 0};
     
    return {
        sum: function(n){
            data.number += n;
        },
        subtract: function(n){
            data.number -= n;
        },
        display: function(){
            console.log("Result: ", data.number);
        }
    }
})();
calculator.sum(10);
calculator.sum(3);
calculator.display();   // Result: 13
calculator.subtract(4);
calculator.display();   // Result: 9
```

#### ООП
###### 2 способа создания объекта:
```
const user = new Object();
```
```
const user = {};               # более распростанённый способ
    const user = {            # почти как словарь в Python
        name: "Tom",
        age: 26
    };
```

###### Свойства объекта
```
user.name = "Tom";
user.age = 26;
```

###### Удаление свойства
```
delete user.name
```
>[!info] После удаления свойство будет не определено, поэтому при попытке обращения к нему, программа вернет значение `undefined`

###### Метод объекта
```
user.display = function(){
    console.log(user.name);      # или же console.log(this.name);
                         # this - тоже что и self в Python(ссылка на текущ. экземпляр объекта)
    console.log(user.age);       # или же console.log(this.age); 
};
```
и его вызов:
```
user.display();
```

#### Массивы
```
const myArray = [];
```
Или
```
const myArray = new Array();
```
```
console.log(myArray.length);
```
>[!info] Если мы попробуем обратиться к элементу по индексу больше размера массива, то мы получим `undefined`. Можно установить элемент, который изначально не установлен.
```
const people = ["Tom", "Alice", "Sam"];
console.log(people[7]);                       // undefined - в массиве только три элемента
people[7] = "Bob";
console.log(people[7]);                       // Bob
```
Обращение к элементу двумерного массива
```
people[0][1]
```

###### Array.from()
Создание массива из объекта "похожего на массив"
```
const array = Array.from("Hello");
console.log(array);                           // ["H", "e", "l", "l", "o"]
```

В качестве второго параметра передается функция преобразования, которая через первый параметр получает текущий  элемент набора и возвращает некоторый результат его трансформации.
```
const numbers = [1, 2, 3, 4];
const array = Array.from(numbers, n => n * n);
console.log(array);                           // [1, 4, 9, 16]
```

###### Свойства массивов
|Свойство|Описание|
|--------|--------|
|length()|длина массива|
|concat()|объединяет элементы двух массивов в один массив
|includes()|проверяет, есть ли в массиве значение
|indexOf()|возвращают индекс первого включения элемента в массиве
|join()|объединяет элементы массива в строку
|map()|преобразует каждый элемент массива в другое значение на основе функции преобразования
|pop()|удаление элемента с конца массива
|push()|добавление элемента в конец массива
|reverse()|располагает элементы массива в обратном порядке
|slice(start, end)|вырезает отдельные элементы из массива
|sort()|сортирует массив

###### Spread оператор
`...массив`
```
const users = ["Tom", "Sam", "Bob"];
console.log(...users);  // Tom Sam Bob
```
Так можно выполнить объединение массивов
```
const men = ["Tom", "Sam", "Bob"];
const people = [...men, "Alex"];
```
Или передача в функцию
```
print(...people);                            // Передадутся 4 аргумента - значения массива
```
Копирование массива
```
const people = ["Sam", "Tom", "Bob"];
const employees = [...people];          // Если бы people был массивом объектов, то копировались
                                        //   бы только ссылки на объекты
```

#### JSON
###### JSON.stringify()
Для сериализации объекта javascript в json
```
// объект javascript
const user = {
    name: "Tom",
    married: false,
    age: 39
};

// объект json
const serializedUser = JSON.stringify(user);
console.log(serializedUser); // {"name":"Tom","married":false,"age":39}
```

###### JSON.parse()
Для десериализации или парсинга json-объекта в javascript
```
const user = {
    name: "Tom",
    married: false,
    age: 39
};

// сериализация
const serializedUser = JSON.stringify(user);

// десериализация
const tomUser = JSON.parse(serializedUser);
console.log(tomUser.name); // Tom
```

#### DOM (Document Object Model)
Что доступно для JavaScript в браузерном окружении
![[DOM.png|700]]
>[!info] DOM - объектная модель документа, которая представляет все содержимое страницы в виде объектов, которые можно менять. Объект document – основная «входная точка». С его помощью мы можем что-то создавать или менять на странице.

Например
```
// заменим цвет фона на красный,
document.body.style.background = "red";
```

В соответствии с объектной моделью документа («Document Object Model», коротко DOM), каждый HTML-тег является объектом.
 Вложенные теги являются «детьми» родительского элемента. Текст, который находится внутри тега, также является объектом.
Все, что есть в HTML, даже комментарии, является частью DOM

###### Получение элементов рядом
- Коллекция `childNodes` содержит список всех детей, включая текстовые узлы.
- Свойства `firstChild` и `lastChild` обеспечивают быстрый доступ к первому и последнему дочернему элементу.
- Для проверки наличия дочерних узлов существует также специальная функция `elem.hasChildNodes()`

DOM-коллекции доступны только для чтения
Коллекции перебираются циклом `for..of`
Следующий узел того же родителя (следующий сосед) – в свойстве `nextSibling`, а предыдущий – в `previousSibling`
Родитель доступен через `parentNode`
```
// после <head> идёт <body>
alert( document.head.nextSibling ); // HTMLBodyElement
```

###### Получение произвольных элементов
```
document.getElementById(id)
document.querySelector()                 # возвращает первый элемент, соответствующий данному
                                         #   CSS-селектору
document.querySelectorAll(css)           # возвращает все элементы внутри elem, 
                                         #   удовлетворяющие данному CSS-селектору.
                                         #   например document.querySelectorAll('.chapter')
elem.getElementsByClassName(className)
document.getElementsByTagName('input')
```

###### Установка/получение HTML атрибутов
|Функция|Описание|
|--------|--------|
|elem.hasAttribute(name)|проверяет наличие атрибута|
|elem.getAttribute(name)|получает значение атрибута|
|elem.setAttribute(name, value)|устанавливает значение атрибута|
|elem.removeAttribute(name)|удаляет атрибут|

```
let punkt = document.querySelector('input');
punkt.setAttribute('id', 'fiva');           # теперь у инпута будет id="fiva"
```

###### Создание элемента
```
let div = document.createElement('div');
div.className = "alert";
div.innerHTML = "<strong>Всем привет!</strong> Вы прочитали важное сообщение.";
```

###### Методы вставки
1. Вставляется как текст
node.append(...nodes or strings) – добавляет узлы или строки в конец node,
node.prepend(...nodes or strings) – вставляет узлы или строки в начало node,
node.before(...nodes or strings) – вставляет узлы или строки до node,
node.after(...nodes or strings) – вставляет узлы или строки после node,
node.replaceWith(...nodes or strings) – заменяет node заданными узлами или строками.
Например
```
<div id="first">Первый</div>
<div id="second">Второй</div>
<script>
// нет необходимости вызывать метод remove
second.after(first); // берёт #second и после него вставляет #first
</script>
```

2. Вставляется как HTML
elem.insertAdjacentHTML(where, html)
"beforebegin" – вставить html непосредственно перед elem,
"afterbegin" – вставить html в начало elem,
"beforeend" – вставить html в конец elem,
"afterend" – вставить html непосредственно после elem.

Например
```
document.body.insertAdjacentHTML("afterbegin", `<div class="alert"><strong>Всем привет!</strong> Вы прочитали важное сообщение.</div>`);
```

###### Удаление узлов
```
node.remove()
```
Например
```
setTimeout(() => div.remove(), 1000);
```

###### Клонирование узлов
`elem.cloneNode(true)`     клон элемента – со всеми атрибутами и дочерними элементами
`elem.cloneNode(false)`    клон будет без дочерних элементов
```
let div2 = div.cloneNode(true); // клонировать сообщение
```

#### BOM (Browser Object Model)
>[!info] Это дополнительные объекты, предоставляемые браузером (окружением), чтобы работать со всем, кроме документа.

Например
```
navigator.userAgent            // информация о текущем браузере
navigator.platform             // информация о платформе
alert(location.href);          // показывает текущий URL
```

#### CSS
Установка одного значения
```
elem.style.left = left;
```
Для свойств из нескольких слов используется camelCase
```
elem.style.backgroundColor
elem.style.zIndex
```
Полная перезапись стилей elem, используем `=`
```
elem.style.cssText = `
  top: ${top};
  left: ${left};
`;
```
Перезапись всей строки с классами css
```
document.body.className
```

Добавить/удалить один класс:
`elem.classList.add/remove("class")` – добавить/удалить класс
`elem.classList.contains("class")` – проверка наличия класса, возвращает `true`/`false`

Также можно перечислить все классы при помощи `for..of`
```
document.body.classList.add('article');
```

#### Размеры и прокрутка
```
alert( window.innerWidth );      # полная ширина окна
alert( window.innerHeight );     # полная высота окна

window.scrollBy(0, 10)           # прокручивает страницу относительно её текущего положения(на 10px вниз)
window.scrollTo(0, 0)            # прокручивает страницу на абсолютные координаты

window.scrollTo({
  top: 100,
  left: 0,
  behavior: "smooth"             # "smooth"/"instant"
});

elem.scrollIntoView(top)         # прокрутить так, чтобы elem был на первой строчке сверху
```

#### События
###### События мыши
   `click` – происходит, когда кликнули на элемент левой кнопкой мыши (на устройствах с сенсорными экранами оно происходит при касании).
   `contextmenu` – происходит, когда кликнули на элемент правой кнопкой мыши.
   `mouseover` / `mouseout` – когда мышь наводится на / покидает элемент.
   `mousedown` / `mouseup` – когда нажали / отжали кнопку мыши на элементе.
   `mousemove` – при движении мыши.
   
###### События на элементах управления
`submit` – пользователь отправил форму `<form>`.
`focus` – пользователь фокусируется на элементе, например нажимает на `<input>`.

###### Клавиатурные события
`keydown` и `keyup` – когда пользователь нажимает / отпускает клавишу.

###### События документа
`DOMContentLoaded` – когда HTML загружен и обработан, DOM документа полностью построен и доступен.

###### CSS events
`transitionend` – когда CSS-анимация завершена.

#### Обработчики событий
**Обработчик может быть назначен прямо** в разметке, в атрибуте, который называется on<событие>(Используется редко)
Например
```
<input value="Нажми меня" onclick="alert('Клик!')" type="button">
```
или же назначить функцию:
```
<input type="button" onclick="countRabbits()" value="Считать кроликов!">
```

**Можно использовать id элемента**(через DOM-свойство)
```
<input id="elem" type="button" value="Нажми меня!">
<script>
elem.onclick = function() {
alert('Спасибо');
};
</script>
```

**Убрать обработчик можно назначением `elem.onclick = null`**

>[!info] Несколько обработчиков на одно событие - слушатель

```
element.addEventListener(event, handler, [options]);
```
`event` - Имя события, например `click`
`handler` - Ссылка на функцию-обработчик
`options` {
        once:    если true, тогда обработчик будет автоматически удалён после выполнения
        passive: если true, то указывает, что обработчик никогда не вызовет `preventDefault()`
        }
Например
```
document.addEventListener("DOMContentLoaded", function() {
    alert("DOM построен");        // а вот так сработает
});
```
**Удаление слушателя**
```
element.removeEventListener(event, handler, [options]);
```

Также метод `addEventListener` позволяет добавлять несколько обработчиков на одно событие одного элемента
```
elem.addEventListener("click", handler1);
elem.addEventListener("click", handler2);
```

#### Действия браузера по умолчанию
Для отмены действия браузера существует стандартный метод `event.preventDefault()`
Или же необязательная опция `passive: true` для `addEventListener`

#### Загрузка документа и ресурсов
- `DOMContentLoaded` – браузер полностью загрузил HTML, было построено DOM-дерево, но внешние ресурсы, такие как картинки `<img>` и стили, могут быть ещё не загружены.
- `load` - браузер загрузил HTML и внешние ресурсы (картинки, стили и т.д.)
- `beforeunload`/`unload` - пользователь покидает страницу.
- `window.onload`    - наступает, когда загрузилась вся страница, включая стили, картинки и другие ресурсы
```
<script>
  window.onload = function() { 
      alert('Страница загружена');
  };
</script>
```
>[!info] Браузер позволяет отслеживать загрузку сторонних ресурсов

```
let script = document.createElement('script');

// мы можем загрузить любой скрипт с любого домена
script.src = "https://cdnjs.cloudflare.com/ajax/libs/lodash.js/4.3.0/lodash.js"
document.head.append(script);

script.onload = function() {
  // в скрипте создаётся вспомогательная переменная с именем "_"
  alert(_.VERSION); // отображает версию библиотеки
};
script.onerror = function() {
  alert("Ошибка загрузки " + this.src); // Ошибка загрузки https://example.com/404.js
};
```
>[!warning] Обработчики onload/onerror отслеживают только сам процесс загрузки.
Ошибки обработки и выполнения загруженного скрипта ими не отслеживаются. Чтобы «поймать» ошибки в скрипте, нужно воспользоваться глобальным обработчиком window.onerror.

#### Cookie
###### Чтение
```
alert( document.cookie ); // cookie1=value1; cookie2=value2;
```
###### Запись
```
document.cookie = "user=John";

   для правильного форматирования следует использовать встроенную функцию encodeURIComponent:
   // кодирует в my%20name=John%20Smith
   document.cookie = encodeURIComponent("my name") + '=' + encodeURIComponent("John Smith");

   path=/admin            куки будут доступны для страниц под этим путём
   domain=site.com        домен определяет, где доступен файл куки
   max-age=3600           срок действия куки в секундах с текущего момента
   
   document.cookie = "user=John; max-age=3600";
```
По умолчанию куки, установленные сайтом http://site.com, также будут доступны на сайте https://site.com и наоборот.
   `document.cookie = "user=John; secure";` Эти куки будут доступны только по `https`

#### Callback
Асинхронное программирование с использованием колбэков
```
function loadScript(src, callback) {
  let script = document.createElement('script');
  script.src = src;

  script.onload = () => callback(null, script);
  script.onerror = () => callback(new Error(`Не удалось загрузить скрипт ${src}`));

  document.head.append(script);
}
```

#### Промисы
Функция-исполнитель запускается автоматически, она должена выполнить работу, а затем вызвать `resolve` или `reject`.
```
let promise = new Promise(function(resolve, reject) {      # resolve(value) — если работа завершилась успешно, с результатом value
   // функция-исполнитель (executor)                       # reject(error) — если произошла ошибка, error – объект ошибки
   setTimeout(() => resolve("done"), 1000);
});
```
У объекта promise, возвращаемого конструктором new Promise, есть внутренние свойства

||если resolve(value)|если reject(error)|
|------|-------|-------|
|state      pending(ожидание) -> |fulfilled(выполнено успешно)|rejected(выполнено с ошибкой)|
|result     undefined                  ->|value|error|

Функция-исполнитель запускается сразу же при вызове new Promise
Исполнитель получает два аргумента: resolve и reject — это функции, встроенные в JavaScript, поэтому нам не нужно их писать.
 Нам нужно лишь позаботиться, чтобы исполнитель вызвал одну из них по готовности.
Состояние промиса может быть изменено только один раз.

Пример промиса с функцией-исполнителем, сообщающей об ошибке
```
let promise = new Promise(function(resolve, reject) {
  // спустя одну секунду будет сообщено, что задача выполнена с ошибкой
  setTimeout(() => reject(new Error("Whoops!")), 1000);
});
```
**.then**
```
   let promise = new Promise(function(resolve, reject) {
     setTimeout(() => resolve("done!"), 1000);
   });

   // resolve запустит первую функцию, переданную в .then
   promise.then(
     result => alert(result), // выведет "done!" через одну секунду
     error => alert(error)    // не будет запущена
   );
```
**.catch**
   Если мы хотели бы только обработать ошибку
```
promise.catch(alert);      // выведет "done!" спустя одну секунду
```

**.finally**
выполнится в любом случае, когда промис завершится: успешно или с ошибкой

Пример промисов
```
let promise = loadScript("https://cdnjs.cloudflare.com/ajax/libs/lodash.js/4.17.11/lodash.js");

promise.then(
  script => alert(`${script.src} загружен!`),
  error => alert(`Ошибка: ${error.message}`)
);

promise.then(script => alert('Ещё один обработчик...'));
```

Цепочка промисов(последовательно загружает 3 скрипта)
```
loadScript("/article/promise-chaining/one.js").then(script1 => {
  loadScript("/article/promise-chaining/two.js").then(script2 => {
    loadScript("/article/promise-chaining/three.js").then(script3 => {
      // эта функция имеет доступ к переменным script1, script2 и script3
      one();
      two();
      three();
    });
  });
});
```

Пример промисов с fetch
```
// response.json() читает данные в формате JSON
fetch('/article/promise-chaining/user.json')
  .then(response => response.json())
  .then(user => alert(user.name)); // получили имя пользователя
```

#### Асинхронные функции
###### ASYNC
>[!info] У слова async один простой смысл: эта функция всегда возвращает промис

```
async function f() {
    return 1;
}
```
эта функция возвратит выполненный промис с результатом 1

###### AWAIT
Работает только в теле функций
await заставит интерпретатор JavaScript ждать до тех пор, пока промис справа от await не выполнится. После чего оно вернёт его результат, и выполнение кода продолжится.
```
// работает только внутри async–функций
let value = await promise;
```

Пример
```
   async function getInfo() {

     try {
         // запрашиваем JSON с данными пользователя
         let response = await fetch('/article/promise-chaining/user.json');
         let user = await response.json();

         return user;
     } catch(err) {
         alert(err)
     }
   };
```

###### FETCH
>[!info] Без options это простой GET-запрос, скачивающий содержимое по адресу `url`

Типичный запрос fetch
```
let response = await fetch(url, options);     // завершается с заголовками ответа
let result = await response.json();           // читать тело ответа в формате JSON
```

```
let response = await fetch(url);

if (response.ok) {                        // если HTTP-статус в диапазоне 200-299; есть ещё .status
  let json = await response.json();
} else {
  alert("Ошибка HTTP: " + response.status);
}
```

Для получения тела ответа здесь используется `.json()` .Мы можем выбрать только один метод чтения ответа.Вот список методов:
`response.text()` – читает ответ и возвращает как обычный текст
`response.json()` – декодирует ответ в формате JSON
`response.formData()` – возвращает ответ как объект FormData
`response.blob()` – возвращает объект как Blob (бинарные данные с типом)
`response.arrayBuffer()` – возвращает ответ как ArrayBuffer (низкоуровневое представление бинарных данных)

Для установки заголовка запроса в fetch мы можем использовать опцию headers(Не можем устанавливать Cookie)
```
   let response = fetch(protectedUrl, {
     headers: {
       Authentication: 'secret'
     }
   });
```

###### POST метод в Fetch
Пераметры:
- method – HTTP метод, например POST
- body – тело запроса, одно из списка:
      строка (например, в формате JSON),
      объект FormData для отправки данных как form/multipart,
      Blob/BufferSource для отправки бинарных данных,

```
let user = {
  name: 'John',
  surname: 'Smith'
};

let response = await fetch('/article/fetch/post/user', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json;charset=utf-8'
  },
  body: JSON.stringify(user)
});

let result = await response.json();
alert(result.message);
```

#### Примеры AJAX
###### AJAX
```
document.addEventListener("DOMContentLoaded", function(){             //Создаётся слушатель события полной загрузки страницы и передаём ему функцию
        let btn = document.querySelector('input[type=submit]');       //Создаём переменную btn. Находим в документе кнопку и привязываем её к переменной
        btn.addEventListener('click', async function(e){              //Создаём слушатель кнопки, по событию клик запускаем асинхронную функцию, где событие-это аргумент
            e.preventDefault();                                       //предотвратить поведение кнопки по умолчанию
            let request = await fetch("/login", {                     //отправляем форму на сервер
                method: "POST",
                body: new FormData(document.querySelector('form'))
            });
            let response_json = await request.json();                 //Создаём переменную, которая будет принимать ответ сервера
            if (response_json.success){                               //Если "success" == True, то...
                let body = document.querySelector('body');
                body.style.backgroundColor = "green";
                body.innerHTML = response_json.message;
            }
        })
})
```

Со стороны Python-сервера в ответ(кончено же import json):
```
Response(
         json.dumps({
             "success": False,
             "message": "Unknown user!"
         }),
         media_type="application/json")
```

###### AJAX запрос на API
```
document.addEventListener('DOMContentLoaded', function() {
    var xhr = new XMLHttpRequest();
    xhr.open('GET', '/banned_users/?user={{ object_id }}', true);
    xhr.onload = function() {
        if (xhr.status === 200) {
            var response = JSON.parse(xhr.responseText);
            if (response.length > 0) {
                var firstObject = response[0];
                var endDate = firstObject.end_date;
                endDate = new Date(endDate).toLocaleDateString();
                var reason = firstObject.reason;
                var user = firstObject.user;
                if (user !== undefined && user !== null && user !== "") {
                    document.getElementById('user_status').textContent = 'STATUS: BANNED';
                    document.getElementById('banned_end_date').textContent = 'Забанен до: ' + endDate;
                    document.getElementById('banned_reason').textContent = 'Причина: ' + reason;

                    document.getElementById('unban_button').disabled = false;
                    document.getElementById('ban_button').disabled = true;
                }
            } else {
                document.getElementById('user_status').textContent = 'STATUS: ACTIVE';

                document.getElementById('unban_button').disabled = true;
                document.getElementById('ban_button').disabled = false;
            }
        } else {
            document.getElementById('user_status').textContent = 'STATUS: WRONG USER';
        }
    };
    xhr.send();
});
```

#### FormData
Отправка формы
```
<form id="formElem">
  <input type="text" name="name" value="John">
  <input type="text" name="surname" value="Smith">
  <input type="submit">
</form>
```

```
  formElem.onsubmit = async (e) => {
    e.preventDefault();

    let response = await fetch('/article/formdata/post/user', {
      method: 'POST',
      body: new FormData(formElem)
    });

    let result = await response.json();

    alert(result.message);
  };
```

- formData.append(name, value) – добавляет к объекту поле с именем name и значением value,
- formData.append(name, blob, fileName) – добавляет поле, как будто в форме имеется элемент `<input type="file">`, третий аргумент fileName устанавливает имя файла (не имя поля формы), как будто это имя из файловой системы пользователя,
- formData.delete(name) – удаляет поле с заданным именем name,
- formData.get(name) – получает значение поля с именем name,
- formData.has(name) – если существует поле с именем name, то возвращает `true`, иначе `false`

>[!info] Поля объекта formData можно перебирать, используя цикл `for..of`