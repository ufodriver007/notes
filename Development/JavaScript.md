![[js.jpg|300]]

[AirBnb Style Guide](https://github.com/airbnb/javascript)
[AirBnb Style Guide(на русском)](https://github.com/leonidlebedev/javascript-airbnb)

[Google JS Style Guide](https://google.github.io/styleguide/jsguide.html)
[Google JS Style Guide(на русском)](https://rostislavdugin.github.io/styleguide/jsguide.html)

#### Устанавливаем node js
>[!info] Node.js — это среда выполнения JavaScript-программ, построенная на движке Chrome V8. Раньше на JS можно было делать только интерактивные сайты, так как это специализированный браузерный язык программирования. С программной платформой Node.js появилась возможность создавать «безбраузерные» приложения для компьютера. Это значит, что при необходимости на JavaScript теперь можно делать то же, что на Python и других скриптовых языках программирования общего назначения.

Проверка, установлен ли Node
```bash
node -v
```

На [сайте](https://nodejs.org/en/download) есть инструкция, как установить node.js с помощью NVM (Node Version Manager)

#### Подключение JavaScript к странице
```html
<script>...Скрипт перед закрывающим тегом body...</script> 
       <!--либо -->
<script src="js/main.js"></script>
```

Асинхронная подгрузка внешнего скрипта
```html
<script async src="js/main.js"></script>
<!-- Т.е. будет загружаться HTML, одновременно с ним JavaScript. 
<!-- Как только файл js будет загружен, загрузка HTML прекратится до полного выполнения js скрипта.
```


Отложенный запуск асинхронной подгрузки
```html
<script defer src="js/main.js"></script>
<!-- Т.е. будет загружаться HTML, одновременно с ним JavaScript.
<!-- Код скрипта будет выполнен только после полной загрузки HTML
```

#### Вывод
```javascript
alert("Привет мир");                              // Модальное окно
console.log("Привет мир");                        // Вывод в консоль 
console.dir(window);                              // Показать объект         
```

#### Переменные
>[!info] `var` не рекомендуется к применению


```js
var userName;                                     // Глобальная/функционльная область видимости
let userName;                                     // Блочная область видимости; нельзя обращаться
                                                  //   к ней до момента её объявления.
var userName, age, height;                        // Объявление нескольких переменных
const sum = 5 + 8;                                // Объявление константы. Должна всегда быть
                                                  //   СРАЗУ инициализирована
```

#### Комментарии
Однострочный комментарий
```js
// однострочный комментарий
```

Многострочный комментарий
```js
/* многострочный
      комментарий */
```

#### Типы данных
- **String**. представляет строку. Каычки любые. Встраивание выражения в строку(только косые кавычки): 
```js
text = `Name: ${username}`
```
- **Number**. представляет числовое значение
- **BigInt**. предназначен для представления очень больших целых чисел
- **Boolean**. представляет логическое значение `true` или `false`
- **Undefined**. представляет одно специальное значение - `undefined` и указывает, что значение не установлено
- **Null**. представляет одно специальное значение - `null` и указывает на отсутствие значения
- **Symbol**. представляет уникальное значение, которое часто применяется для обращения к свойствам сложных объектов
- **Object**. представляет комплексный объект. Например: 	
```js
const user = {};
const user = {name: "Tom", age:24};
```

>[!info] Объекты и массивы в JS - ссылочные типы

Узнать тип можно так:
```js
typeof 123     // 'number'
typeof 'asdf'  // 'string'
```

#### Округление
```js
let num = 12.34567;
let rounded = num.toFixed(3);
console.log(rounded); // "12.346"
```

#### Значения `null` и `undefined`
- `undefined` указывает, что значение не определено или не установлено(смысл в том, что переменная неинициализирована).
- Присвоение значение `null` означает, что у переменной отсутствует значение.

JavaScript является языком со слабой и динамической типизацией. Это значит, что переменные могут динамически менять тип.
С помощью оператора `typeof` можно получить тип переменной(для значения `null` оператор `typeof` возвращает значение `"object"`):
```javascript
id = 45;
console.log(typeof id);   // number
```

Инкремент в JS(увеличивает значение переменной на единицу): 
`x++`    Сначала возвращает значение, потом инкрементирует
`++x`    Сначала инкрементирует, потом возвращает значение
Декремент по аналогии.

Операции с присваиванием по аналогии с Python:
```javascript
let a = 3;
a += 5;            // аналогично a = a + 5
console.log(a);    // 8
```

#### Операторы сравнения
`==` сравнивает два значения
`===` сравнивает два значения и их тип (т.е не выполняет скрытого преобразования типов)
Остальное по аналогии с Python

#### Логические операции
`&&` Логическое И
`||` Логическое ИЛИ
`!` Логическое отрицание

#### Тернарная операция
```javascript
[условие] ? [если true] : [если false]
```
```javascript
const a = 1;
const b = 2;
const result = a < b ? a: b;      //Если a < b, возвращается второй операнд(a)
console.log(result);              // 1
```

#### Оператор ??
Выбирает существенное значение. Оператор возвращает значение левого операнда, если оно НЕ равно `null` и `undefined`. Иначе возвращается значение правого операнда.
```javascript
console.log(null ?? "not null");    // not null
```

#### Опциональные цепочки
Если какое-то значение или атрибут не установлены, можно использовать такой приём
```js
const film.ratings.rottenTomatoes?.reviewsCount ?? 0
```
Теперь даже если `film.ratings.rottenTomatoes` не существует (и мы получим ошибку) с помощью `film.ratings.rottenTomatoes?.reviewsCount` мы получим `undefined`. И уже после этого с помощью `??` мы возвращаем 0 (потому что первый операнд `undefined`)

#### Преобразование типов
`parseInt()`     Преобразование строки в целое число или  NaN
```javascript
const num1 = "123hello";
const num2 = parseInt(num1);
console.log(num2); // 123
```

С помощью специальной функции `isNaN()` можно проверить, представляет ли строка число
`parseFloat()`    Для преобразования строк в дробные числа

Есть ещё функция `Number()`. Это просто более строгий парсинг числа(int или float)

#### Условные конструкции
###### IF
```javascript
if(условие){    
    некоторые действия
}

if (myVar !== undefined && myVar !== null) {
    console.log(`Переменная myVar имеет значение: ${myVar}`);
}
```

###### IF-ELSE
```javascript
if(условие){
    действия, если условие истинно
}
else{
    действия, если условие ложно
}
```

###### IF-ELSE IF-ELSE
```javascript
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
```javascript
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
```javascript
for(let i = 0; i<5; i++){
    console.log(i);
}
```

###### WHILE
```javascript
let i = 1;
while(i <=5){
    console.log(i);
    i++;
}
```

###### DO-WHILE
```javascript
let i = 1;
do{
    console.log(i);
    i++;
}while(i <= 5)
```

###### FOR-IN(Для перебора объектов)
```javascript
for (свойство in объект) {
    // действия
}
```

###### FOR-OF(для перебора наборов данных(строк, массивов))
```js
const text = "Hello";
for(char of text){
     console.log(char);
}
```

###### forEach(для перебора массивов)
Метод `arr.forEach(callback[, thisArg])` используется для перебора массива.
Он для каждого элемента массива вызывает функцию `callback`.
Этой функции он передаёт три параметра `callback(item, i, arr)`:
- `item` – очередной элемент массива.
- `i` – его номер.
- `arr` – массив, который перебирается.

Например:
```javascript
 var arr = ["Яблоко", "Апельсин", "Груша"];
 
arr.forEach(function(item, i, arr) {
    alert( i + ": " + item + " (массив:" + arr + ")" ); 
}); 
```

Второй, необязательный аргумент `forEach` позволяет указать контекст `this` для `callback`. Мы обсудим его в деталях чуть позже, сейчас он нам не важен.
Метод `forEach` ничего не возвращает, его используют только для перебора, как более «элегантный» вариант, чем обычный цикл `for`.

>[!info] Также присутствуют `continue` и `break`

#### ФУНКЦИИ
```javascript
function hello(message, x = 0){
    console.log(message);
    console.log(x);                   // параметр по умолчанию
}
hello();                              // вызов функции
```

###### Переменное количество аргументов
```javascript
function my_print(){
    for(const n of arguments)   // объект arguments автоматически передаваемый в функцию
        console.log(n);
}
my_print(6, 4, 5, 8);
```
Или
```javascript
function my_print(...numbers){
    for(const n of numbers)  
        console.log(n);
}
my_print(6, 4, 5, 8);
```

###### Анонимные функции
```javascript
const message = function(){
    console.log("Hello JavaScript");
}
message();
```

###### Стрелочные функции
```javascript
const sum = (x, y) => console.log("Sum =", x + y);
sum(2, 5);
```

###### Самовызывающиеся функции
```javascript
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

```javascript
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
###### Создание класса
```js
class Person {  // всегда называем с большой буквы
  // Конструктор для инициализации объекта
  constructor(name, age) {
    this.name = name; // Свойство "name"
    this.age = age;   // Свойство "age"
  }

  // Метод класса
  greet() {
    console.log(`Привет, меня зовут ${this.name} и мне ${this.age} лет.`);
  }
}
```

###### 2 способа создания объекта:
```javascript
const user = new Object();
```
```javascript
const user = {};          // более распростанённый способ
const user = {            // почти как словарь в Python
    name: "Tom",
    age: 26
};
```

###### Свойства объекта
```javascript
user.name = "Tom";
user.age = 26;
```

Получить все свойства
```js
console.log(Object.keys(user));
```

###### Удаление свойства
```javascript
delete user.name
```
>[!info] После удаления свойство будет не определено, поэтому при попытке обращения к нему, программа вернет значение `undefined`

###### Метод объекта
```javascript
user.display = function(){
    console.log(user.name);      // или же console.log(this.name);
                         // this - тоже что и self в Python(ссылка на текущ. экземпляр объекта)
    console.log(user.age);       // или же console.log(this.age); 
};
```
и его вызов:
```javascript
user.display();
```

###### Деструктуризация объектов
```js
const user = {
    name: "Tom",
    age: 26
};

const name = user.name;
const age = user.age;

// Деструктуризация объекта. Названия переменных должны точно совпадать
const { name, age } = user;

// Смена названия переменной
const { name: title, age } = user;

console.log(title);  // Tom
```

#### Массивы
```javascript
const myArray = [];
```
Или
```javascript
const myArray = new Array();
```
```javascript
console.log(myArray.length);
```
>[!info] Если мы попробуем обратиться к элементу по индексу больше размера массива, то мы получим `undefined`. Можно установить элемент, который изначально не установлен.
```javascript
const people = ["Tom", "Alice", "Sam"];
console.log(people[7]);                       // undefined - в массиве только три элемента
people[7] = "Bob";
console.log(people[7]);                       // Bob
```
Обращение к элементу двумерного массива
```javascript
people[0][1]
```

###### Array.from()
Создание массива из объекта "похожего на массив"
```javascript
const array = Array.from("Hello");
console.log(array);                           // ["H", "e", "l", "l", "o"]
```

В качестве второго параметра передается функция преобразования, которая через первый параметр получает текущий  элемент набора и возвращает некоторый результат его трансформации.
```javascript
const numbers = [1, 2, 3, 4];
const array = Array.from(numbers, n => n * n);
console.log(array);                           // [1, 4, 9, 16]
```

Генерация массива
```js
const sequence = [...Array(15).keys()];
```

```js
const length = 10;
const sequence = Array.from({ length }, (_, i) => i);
```

```js
function* range(start, end) {
  for (let i = start; i <= end; i++) {
    yield i;
  }
}

const sequence = [...range(0, 9)];
console.log(sequence); // [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
```

###### Свойства массивов
|Свойство|Описание|
|--------|--------|
|`length()`|длина массива|
|`concat()`|объединяет элементы двух массивов в один массив
|`includes()`|проверяет, есть ли в массиве значение
|`indexOf()`|возвращают индекс первого включения элемента в массиве
|`join()`|объединяет элементы массива в строку
|`map()`|преобразует каждый элемент массива в другое значение на основе функции преобразования
|`reduce(acc, func, startValue)`|применяет указанную функцию к элементам последовательности, сводя её к единственному значению
|`filter()`|создаёт новый массив, содержащий только те элементы, которые удовлетворяют условию. Например `[1,2,3,4].filter(num => num % 2 === 0)`
|`pop()`|удаление элемента с конца массива
|`push()`|добавление элемента в конец массива
|`reverse()`|располагает элементы массива в обратном порядке
|`slice(start, end)`|вырезает отдельные элементы из массива
|`sort()`|сортирует массив на месте

###### Деструктуризация массивов
```js
const [a, b] = [1, 2];
console.log(a);  // 1
console.log(b);  // 2
```

![[js_arrays.jpg]]

###### Класс Map
Хранит отображение и позволяет задействовать любые типы ключей.
```js
let ages = new Map();
ages.set("Борису", 39);
ages.get("Борису");      // 39
ages.has("Борису");      // true
ages.size;               // 1
```

###### Rest оператор
>[!info] Rest-оператор используется для **сбора** оставшихся элементов массива, объекта или параметров функции в одну переменную.

Пример с деструктуризацией (распаковкой) массива
```js
const users = ["Tom", "Sam", "Bob"];
const [firstUser, ...others] = users;

console.log(firstUser);  // Tom
console.log(others);     // ['Sam', 'Bob']
```

###### Spread оператор
>[!info] Spread-оператор распаковывает значения из одного массива или объекта в другое место.

`...массив`
```javascript
const users = ["Tom", "Sam", "Bob"];
console.log(...users);  // Tom Sam Bob
```

Так можно выполнить объединение массивов
```javascript
const men = ["Tom", "Sam", "Bob"];
const people = [...men, "Alex"];
```

Или передача в функцию
```javascript
print(...people);                            // Передадутся 4 аргумента - значения массива
```

Копирование массива
```javascript
const people = ["Sam", "Tom", "Bob"];
const employees = [...people];          // Если бы people был массивом объектов, то копировались
                                        //   бы только ссылки на объекты
```

#### Замер времени
```js
const start = performance.now();
let result = 0;

for (let i = 0; i < 10000000; i++) {
  result = result + i;
}

const end = performance.now();
console.log(result)
console.log(`Время выполнения: ${end - start} миллисекунд`);
```

#### JSON
###### JSON.stringify()
>[!info] Для сериализации объекта javascript в json
```javascript
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
>[!info] Для десериализации или парсинга json-объекта в javascript
```javascript
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
```javascript
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
```javascript
// после <head> идёт <body>
alert( document.head.nextSibling ); // HTMLBodyElement
```

###### Получение произвольных элементов
```javascript
document.getElementById(id)
document.querySelector()                 // возвращает первый элемент, соответствующий данному
                                         //   CSS-селектору
document.querySelectorAll(css)           // возвращает все элементы внутри elem, 
                                         //   удовлетворяющие данному CSS-селектору.
                                         //   например document.querySelectorAll('.chapter')
elem.getElementsByClassName(className)
document.getElementsByTagName('input')
```

###### Установка/получение HTML атрибутов
|Функция|Описание|
|--------|--------|
|`elem.hasAttribute(name)`|проверяет наличие атрибута|
|`elem.getAttribute(name)`|получает значение атрибута|
|`elem.setAttribute(name, value)`|устанавливает значение атрибута|
|`elem.removeAttribute(name)`|удаляет атрибут|

```javascript
let punkt = document.querySelector('input');
punkt.setAttribute('id', 'fiva');           // теперь у инпута будет id="fiva"
```

###### Создание элемента
```javascript
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
```html
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
```javascript
document.body.insertAdjacentHTML("afterbegin", `<div class="alert"><strong>Всем привет!</strong> Вы прочитали важное сообщение.</div>`);
```

###### Удаление узлов
```javascript
node.remove()
```
Например
```javascript
setTimeout(() => div.remove(), 1000);
```

###### Клонирование узлов
`elem.cloneNode(true)`     клон элемента – со всеми атрибутами и дочерними элементами
`elem.cloneNode(false)`    клон будет без дочерних элементов
```javascript
let div2 = div.cloneNode(true); // клонировать сообщение
```

#### BOM (Browser Object Model)
>[!info] Это дополнительные объекты, предоставляемые браузером (окружением), чтобы работать со всем, кроме документа.

Например
```javascript
navigator.userAgent            // информация о текущем браузере
navigator.platform             // информация о платформе
alert(location.href);          // показывает текущий URL
```

#### CSS
Установка одного значения
```javascript
elem.style.left = left;
```
Для свойств из нескольких слов используется camelCase
```javascript
elem.style.backgroundColor
elem.style.zIndex
```
Полная перезапись стилей elem, используем `=`
```javascript
elem.style.cssText = `
  top: ${top};
  left: ${left};
`;
```
Перезапись всей строки с классами css
```javascript
document.body.className
```

Добавить/удалить один класс:
`elem.classList.add/remove("class")` – добавить/удалить класс
`elem.classList.contains("class")` – проверка наличия класса, возвращает `true`/`false`
`elem.className = "active"` - устанавливает(перезаписывает) класс
`elem.className = "class1 class2 class3"`- устанавливает(перезаписывает) набор классов
`elem.className += "active"` - добавляет класс


Также можно перечислить все классы при помощи `for..of`
```javascript
document.body.classList.add('article');
```

#### Размеры и прокрутка
```javascript
alert( window.innerWidth );      // полная ширина окна
alert( window.innerHeight );     // полная высота окна

window.scrollBy(0, 10)           // прокручивает страницу относительно её текущего положения(на 10px вниз)
window.scrollTo(0, 0)            // прокручивает страницу на абсолютные координаты

window.scrollTo({
  top: 100,
  left: 0,
  behavior: "smooth"             // "smooth"/"instant"
});

elem.scrollIntoView(top)         // прокрутить так, чтобы elem был на первой строчке сверху
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
**Обработчик может быть назначен прямо** в разметке, в атрибуте, который называется ==on<событие>==(Используется редко)
Например
```html
<input value="Нажми меня" onclick="alert('Клик!')" type="button">
```
или же назначить функцию:
```html
<input type="button" onclick="countRabbits()" value="Считать кроликов!">
```

**Можно использовать id элемента**(через DOM-свойство)
```html
<input id="elem" type="button" value="Нажми меня!">
<script>
elem.onclick = function() {
alert('Спасибо');
};
</script>
```

**Убрать обработчик можно назначением `elem.onclick = null`**

>[!info] Несколько обработчиков на одно событие - слушатель

```javascript
element.addEventListener(event, handler, [options]);
```
`event` - Имя события, например `click`
`handler` - Ссылка на функцию-обработчик
`options` {
        once:    если true, тогда обработчик будет автоматически удалён после выполнения
        passive: если true, то указывает, что обработчик никогда не вызовет `preventDefault()`
        }
Например
```javascript
document.addEventListener("DOMContentLoaded", function() {
    alert("DOM построен");        // а вот так сработает
});
```
**Удаление слушателя**
```javascript
element.removeEventListener(event, handler, [options]);
```

Также метод `addEventListener` позволяет добавлять несколько обработчиков на одно событие одного элемента
```javascript
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
```html
<script>
  window.onload = function() { 
      alert('Страница загружена');
  };
</script>
```
>[!info] Браузер позволяет отслеживать загрузку сторонних ресурсов

```javascript
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
```javascript
alert( document.cookie ); // cookie1=value1; cookie2=value2;
```
###### Запись
```javascript
document.cookie = "user=John";

//для правильного форматирования следует использовать встроенную функцию encodeURIComponent:
// кодирует в my%20name=John%20Smith
document.cookie = encodeURIComponent("my name") + '=' + encodeURIComponent("John Smith");

path="/admin"          //куки будут доступны для страниц под этим путём
domain="site.com"      //домен определяет, где доступен файл куки
max-age=3600           //срок действия куки в секундах с текущего момента
   
document.cookie = "user=John; max-age=3600";
```
По умолчанию куки, установленные сайтом `http://site.com`, также будут доступны на сайте `https://site.com` и наоборот.
   `document.cookie = "user=John; secure";` Эти куки будут доступны только по `https`

#### Callback
>[!info] Callback — это функция, переданная другой функции, которая вызывается позже.

Асинхронное программирование с использованием колбэков
```javascript
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
```javascript
let promise = new Promise(function(resolve, reject) { // resolve(value) — если работа завершилась
                                                      //     успешно, с результатом value
                                  // reject(error) — если произошла ошибка, error – объект ошибки
   setTimeout(() => resolve("done"), 1000);           // функция-исполнитель (executor)
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
```javascript
let promise = new Promise(function(resolve, reject) {
  // спустя одну секунду будет сообщено, что задача выполнена с ошибкой
  setTimeout(() => reject(new Error("Whoops!")), 1000);
});
```
**.then**
```javascript
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
```javascript
promise.catch(alert);      // выведет "done!" спустя одну секунду
```

**.finally**
выполнится в любом случае, когда промис завершится: успешно или с ошибкой

Пример промисов
```javascript
let promise = loadScript("https://cdnjs.cloudflare.com/ajax/libs/lodash.js/4.17.11/lodash.js");

promise.then(
  script => alert(`${script.src} загружен!`),
  error => alert(`Ошибка: ${error.message}`)
);

promise.then(script => alert('Ещё один обработчик...'));
```

Цепочка промисов(последовательно загружает 3 скрипта)
```javascript
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
```javascript
// response.json() читает данные в формате JSON
fetch('/article/promise-chaining/user.json')
  .then(response => response.json())
  .then(user => alert(user.name)); // получили имя пользователя
```

#### Асинхронные функции
###### ASYNC
>[!info] У слова async один простой смысл: эта функция всегда возвращает промис

```javascript
async function f() {
    return 1;
}
```
эта функция возвратит выполненный промис с результатом 1

###### AWAIT
Работает только в теле функций
await заставит интерпретатор JavaScript ждать до тех пор, пока промис справа от await не выполнится. После чего оно вернёт его результат, и выполнение кода продолжится.
```javascript
// работает только внутри async–функций
let value = await promise;
```

Пример
```javascript
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
>[!info] Это современный подход к работе с AJAX

>[!info] Без options это простой GET-запрос, скачивающий содержимое по адресу `url`

Типичный запрос fetch
```javascript
let response = await fetch(url, options);     // завершается с заголовками ответа
let result = await response.json();           // читать тело ответа в формате JSON
```

```javascript
let response = await fetch(url);

if (response.ok) {                    // если HTTP-статус в диапазоне 200-299; есть ещё .status
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
```javascript
   let response = fetch(protectedUrl, {
     headers: {
       'Authentication': 'secret',
     }
   });
```

Реальный пример
```html
<h1>Тестовые данные</h1>  
<input id="test_button" value="Получить тестовые данные" type="button" />  
<p id="data"></p>

<script>  
    const button = document.getElementById('test_button');  
    button.addEventListener('click', async () => {
        let response = await fetch('http://127.0.0.1:8000/categories/');
              
        if (response.ok) {
            let json_data = await response.json();
            document.getElementById('data').textContent = json_data[0].name
        } else {
            alert("Ошибка HTTP: " + response.status);
        }
    })
</script>
```

###### POST метод в Fetch
Пераметры:
- method – HTTP метод, например POST
- body – тело запроса, одно из списка:
      строка (например, в формате JSON),
      объект FormData для отправки данных как form/multipart,
      Blob/BufferSource для отправки бинарных данных,

```javascript
let user = {
  name: 'John',
  surname: 'Smith'
};

let response = await fetch('/article/fetch/post/user', {
  method: 'POST',
  headers: {
    'Accept': 'application/json',
    'Content-Type': 'application/json;charset=utf-8'
  },
  body: JSON.stringify(user)
});
if (response.ok) {
    let answer = await response.json();             // Получить JSON ответ от сервера
    console.log(answer);
}

let result = await response.json();
alert(result.message);
```

Ещё пример
```javascript
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
```javascript
Response(
         json.dumps({
             "success": False,
             "message": "Unknown user!"
         }),
         media_type="application/json")
```

#### FormData
Отправка формы
```html
<form id="formElem">
  <input type="text" name="name" value="John">
  <input type="text" name="surname" value="Smith">
  <input type="submit">
</form>
```

```javascript
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


#### Устаревший подход с XMLHttpRequest(запрос на API)
```javascript
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

#### Indexed DB
>[!info] IndexedDB – это встроенная объектно-ориентированная база данных. Содержит объекты в JSON виде(ключ - значение)

- Хранит практически любые значения по ключам, несколько типов ключей
- Поддерживает транзакции для надёжности.
- Поддерживает запросы в диапазоне ключей и индексы.
- Позволяет хранить больше данных, чем `localStorage`.

Обычная последовательность шагов при работе с IndexedDB :

1. Открыть базу данных.
2. Создать хранилище объектов в базе данных, над которой будут выполняться наши операции.
3. Запустить транзакцию и выдать запрос на выполнение какой-либо операции с базой данных, например, добавление или извлечение данных.
4. Ждать завершения операции, обрабатывая событие DOM, на которое должен быть установлен наш обработчик.
5. Сделать что-то с результатами (которые могут быть найдены в возвращаемом по нашему запросу объекте ).

###### Создание и открытие базы данных
```javascript
const openRequest = indexedDB.open(``"myDatabase"``, 1);

// Обработайте события `onupgradeneeded` и `onsuccess`, чтобы создать хранилище данных
//  и установить соединение с базой данных.

openRequest.onupgradeneeded = function(event) {
  const db = event.target.result;

  if (!db.objectStoreNames.contains("myData")) {
    db.createObjectStore("myData", { keyPath: "id" });
  }
};

openRequest.onsuccess = function(event) {
  const db = event.target.result;
  console.log("Database opened:", db);
};
```

После того, как база данных открыта, можно добавлять, обновлять, читать и удалять данные.

######  Чтение данных
Чтение данных осуществляется с помощью метода `get()`
```javascript
const getRequest = objectStore.get(1);

getRequest.onsuccess = function(event) {
  console.log("Data read:", event.target.result);
};
```

###### Обновление данных
Обновление данных происходит аналогично добавлению, но с использованием метода `put()`.
```javascript
const updatedData = { id: 1, name: "Jane Doe" };

const updateRequest = objectStore.put(updatedData);

updateRequest.onsuccess = function(event) {
  console.log("Data updated:", updatedData);
};
```

###### Удаление данных
Для удаления данных используйте метод `delete()`.
```javascript
const deleteRequest = objectStore.delete(1);

deleteRequest.onsuccess = function(event) {
  console.log("Data deleted");
};
```

###### Закрытие базы данных
```javascript
db.close();
```

###### Dexie.js
>[!info] [Обёртка для `indexeddb`](https://dexie.org/docs/Tutorial/Getting-started)
```javascript
// Declare DB  
var db = new Dexie("UserDatabase");  
db.version(1).stores({  
    playlists: "++id,playlist",  
    user: "id,username,savelocal",  
    files: '++id, name, data'        // Хранилище для файлов  
});
```

Пример расчёта размера хранилища
```javascript
function get_db_size() {  
    // логика расчёта объёма БД  
     return new Promise((resolve, reject) => {  
         totalIndexedDBSize = 0;  
        db.files.each(item => {  
            totalIndexedDBSize += (item.content.size / 1024 / 1024); // Не вызывайте здесь toFixed(), чтобы сохранить точность  
        }).then(() => {  
            resolve(totalIndexedDBSize.toFixed(1)); // Округление до 1 знака после запятой и передача результата в resolve  
        }).catch(error => {  
            reject(error);  
        });  
    });  
}

get_db_size()  
    .then(result => {  
    t_size.textContent = "Общий размер локальной базы данных: " + result + "Мб"  
})
```

###### NPM и NPX
- **npm** пакетный менеджер, который скачивает исходники и добавляет в проект
- **npx** пакетный менеджер, который скачивает, выполняет и затем удаляет исходники

>[!info] `package.json` - описание проекта(имя, описание, зависимости, скрипты и т.д.)

>[!tip] Дописываем в `package.json` в основную секцию стоку `"homepage": "./",`

Примеры скриптов
```bash
npm run start             # запустить на тестовом сервере
npm run build             # собрать билд
```

После сборки билда можно открывать `index.html` в браузере.
>[!info]  Директория `node_modules` это аналог виртуального окружения в Python. Её добавляют в `.gitignore`

```bash
npm init                 # создать package.json
npm install              # установить зависимости(node_modules) из package.json
npm install react        # установить библиотеку
npm uninstall react      # удалить библиотеку
```
