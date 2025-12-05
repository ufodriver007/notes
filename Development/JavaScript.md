![[js.jpg|300]]

>[!info]  Высокоуровневый, JIT компилируемый, мультипарадигменный, однопоточный, динамически типизируемый язык.

[AirBnb Style Guide](https://github.com/airbnb/javascript)
[AirBnb Style Guide(на русском)](https://github.com/leonidlebedev/javascript-airbnb)

[Google JS Style Guide](https://google.github.io/styleguide/jsguide.html)
[Google JS Style Guide(на русском)](https://rostislavdugin.github.io/styleguide/jsguide.html)

[Справочник JS (Doka)](https://doka.guide/js/)
[Справочник JS (learn.javascript)](https://learn.javascript.ru/)

#### Use strict
>[!info] Использование *use strict* настоятельно рекомендуется.
```js
// обязательно в самом начале  файла
'use strict';
```

В строгом режиме многие ошибки, которые в обычном режиме просто игнорируются или работают некорректно, становятся явными и выбрасывают исключения.

Что делает `use strict`:
1. Невозможность объявления переменной без ключевого слова
2. Блочная область видимости
3. Ограничение названия переменных зарезервированными словами, которые могут ввести позднее
4. Невозможность  удаление переменных через delete
5. Невозможность дублируемых параметров, использование with

#### Устанавливаем node js
>[!info] Node.js — это среда выполнения JavaScript-программ, построенная на движке Chrome V8. Раньше на JS можно было делать только интерактивные сайты, так как это специализированный браузерный язык программирования. С программной платформой Node.js появилась возможность создавать «безбраузерные» приложения для компьютера. Это значит, что при необходимости на JavaScript теперь можно делать то же, что на Python и других скриптовых языках программирования общего назначения.

Проверка, установлен ли Node
```bash
node -v
```

На [сайте](https://nodejs.org/en/download) есть инструкция, как установить node.js с помощью NVM (Node Version Manager)

#### Подключение JavaScript к странице
В шапке 
```html
<head>
	<script src="js/main.js"></script>
</head>
```

В конце `<body>`
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

Отложенный запуск асинхронной подгрузки. После парсинга HTML. Предпочтительный способ.
```html
<script defer src="js/main.js"></script>
<!-- Т.е. будет загружаться HTML, одновременно с ним JavaScript.
<!-- Код скрипта будет выполнен только после полной загрузки HTML
```

![[js_loading.png]]

#### Модульность
###### Старый способ CommonJS
Экспорт из файла
```js
const users = ['Alex', 'Julia];

function greet(name) {
	console.log(`Привет ${name}`);
}

module.exports = { users, greet };
```

Импорт в файл
```js
const { users, greet } = require('./test.js');

for (const user of users) {
	greet(user);
}
```

###### Современный способ ES Modules
Подключение в HTML
```html
<script src="js/main.js" type="module"></script>
```

Экспорт из файла
```js
export const users = ['Alex', 'Julia'];

export function greet(name) {
	console.log(`Привет ${name}`);
}
```

Импорт в файл
```js
import { users, greet } from './test.js'

for (const user of users) {
	greet(user);
}
```

|  --- |ES Modules|Script|
|---|---|---|
|Переменные|Ограничены модулем|Глобальные|
|Режим|Strict|НЕ Strict|
|`this`|`undefined`|`Window`|
|Импорты|Да|Нет|
|Использование|`type="module"`|-|
|Загрузка|асинхронная|-|

###### Отличия CommonJS от ES Modules

|CommonJS|ES Modules|
|---|---|
|Require в любом месте|Импорт на верхнем уровне|
|Можно использовать в условии|Нельзя использовать в условии|
|Загрузка всего из модуля|Выборочная загрузка|
|Нет асинхронности|Асинхронное подключение|

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
>[!info] Типы данных в JS делятся на *примитивы* (иммутабельные) и *объекты* (мутабельные)

- **String**. представляет строку. . Примитив. Каычки любые. Встраивание выражения в строку(только косые кавычки): 
```js
text = `Name: ${username}`
```
- **Number**. представляет числовое значение. 
- **BigInt**. предназначен для представления очень больших целых чисел
- **Boolean**. представляет логическое значение `true` или `false`
- **Undefined**. представляет одно специальное значение - `undefined` и указывает, что значение не установлено.
- **Null**. представляет одно специальное значение - `null` и указывает на отсутствие значения
- **Symbol**. представляет уникальное значение, которое часто применяется для обращения к свойствам сложных объектов
- **Object**. представляет комплексный объект. Например: 	
```js
const user = {};
const user = {name: "Tom", age:24};
```

>[!info] Объекты и массивы в JS - ссылочные типы, т.е. при присваивании переменной присваивается *ссылка на объект,* а не само значение.

Узнать тип можно так:
```js
typeof 123     // 'number'
typeof 'asdf'  // 'string'
```

#### Преобразование типов
`parseInt()`     Преобразование строки в целое число или  NaN
```javascript
const num1 = "123hello";
const num2 = parseInt(num1);
console.log(num2); // 123

console.log(parseFloat("10.5"));  // 10.5

const num = 123;
console.log(num.toString()); // конвертация в строку
console.log(String(num));

let num = 255;
let hex = num.toString(16); // конвертация в шестнадцатиричное
```

С помощью специальной функции `isNaN()` можно проверить, представляет ли строка число

Есть ещё функция `Number()`. Это просто более строгий парсинг числа(int или float)
```js
const age = '18';
const intAge = Number(age);
```

#### Math
```js
Math.sqrt(144)                 // квадратный корень
Math.sign(-34);                // знак числа. возвращает 1 или -1
Math.abs(-455);                // модуль числа
Math.max(2, 4, 6, 1, 19, 0);   // максимум
Math.min(2, 4, 6, 1, 19, 0);   // минимум
Math.random();                 // Случайное число от 0 до 1

console.log((Math.random() * 100).toFixed(0));  // Случайное число от 0 до 100
```

кастомная функция получения случайного числа
```js
function random(min, max) {
	return Math.floor(Math.random() * (max - min + 1) + min);
}

console.log(random(3, 8));
```

Большие числа
```js
console.log(233n ** 33n);
console.log(BigInt(233) ** BigInt(33));
console.log(BigInt("233") ** BigInt("33"));
```

#### Округление
```js
let num = 12.34567;
num.toFixed(3);   // "12.346" строка точность в 3 знака 

Math.round(1.5)   // 2  округление до ближайшего целого
```

#### Интернационализация чисел
```js
const options1 = {
	style: "currency",
	currency: "RUB",
};

const options2 = {
	style: "currency",
	currency: "USD",
};

const options3 = {
	style: "decimal",
};

const options4 = {
	style: "percent",
};

const options5 = {
	style: "unit",
	unit: "celsius",
};

console.log(new Intl.NumberFormat("ru-RU", options1).format(23000));  // 23 000,00 ₽
console.log(new Intl.NumberFormat("en-US", options2).format(23000));  // $23,000.00
console.log(new Intl.NumberFormat("ru-RU", options3).format(10000));  // 10 000
console.log(new Intl.NumberFormat("ru-RU", options4).format(0.1));    // 10 %
console.log(new Intl.NumberFormat("ru-RU", options5).format(25));     // 25 °C
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
>[!info] Желательно не использовать `==` вообще

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
>[!info] При использовании такой *оциональной цепочки* в случае **отсутствия** какого-то из свойств не падает рантайм ошибка, а просто проверка не идёт дальше и возвращает `undefined`.

```js
const cities = {
    msk: {
        temp: {
			celcius: 25
		}
	},
	spb: {
		weather: {
		}
	}
}

// у cities.msk нет свойства weather, поэтому дальше не проверяется
console.log(cities.msk?.weather?.celcius);  // undefined

// Если сделать это без цепочки (cities.msk.weather.celcius), то проверка дойдёт до отсутсвующего свойства weather, вернёт undefined и дальше при попытке проверить свойство celcius у undefined выпадет RuntimeError
```

Ещё пример
```js
const film.ratings.rottenTomatoes?.reviewsCount ?? 0
```
Теперь даже если `film.ratings.rottenTomatoes` не существует (и мы получим ошибку) с помощью `film.ratings.rottenTomatoes?.reviewsCount` мы получим `undefined`. И уже после этого с помощью `??` мы возвращаем 0 (потому что первый операнд `undefined`)

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
} else {
    действия, если условие ложно
}
```

###### IF-ELSE IF-ELSE
```javascript
const income = 50;
if(income > 50) {
    console.log("Доход больше 50");
} else if (income === 50){
    console.log("Доход равен 50");
} else {
    console.log("Доход меньше 50");
}
```

###### SWITCH-CASE
```javascript
const income = 200;

switch(income){
    case 100 :   // income === 100
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

```javascript
const income = 200;

switch(true){
    case income < 100 :   // true === income < 100
        console.log("Доход меньше 100");
        break;
    case income == 100 :  // true === income == 100
        console.log("Доход равен 100");
        break;
    case income > 100 :   // true === income > 100
        console.log("Доход больше 300");
        break;
}
```

#### ЦИКЛЫ
>[!info] Как и в Python присутствуют операторы `continue` и `break`

###### FOR
```javascript
for(let i = 0; i < 5; i++){
    console.log(i);
}
```

###### WHILE
```javascript
let i = 1;
while(i <= 5){
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

###### FOR-IN (Для перебора объектов)
Итерация по ключам
```javascript
for (let свойство in объект) {
    // действия
}
```

###### FOR-OF (для перебора наборов данных(строк, массивов))
```js
const text = "Hello";
for(let char of text){
     console.log(char);
}
```

```js
const score = [5, 10, 9, 15];

for (const [index, element] of score.entries()) {
    console.log(`Индекс ${index}, Элемент ${element}`)
}
```

###### forEach (для перебора массивов)
Метод `arr.forEach(callback[, thisArg])` используется для перебора массива.
Он для каждого элемента массива вызывает функцию `callback`.
Этой функции он передаёт три параметра `callback(item, i, arr)`:
- `item` – очередной элемент массива.
- `i` – его номер.
- `arr` – массив, который перебирается.

Например:
```javascript
let arr = ["Яблоко", "Апельсин", "Груша"];
 
arr.forEach(function(item, i, arr) {
    alert( i + ": " + item + " (массив:" + arr + ")" ); 
}); 
```

Простейшее получение элемента и его индекса
```js
arr.forEach((item, index) => {
    console.log(index + ": " + item);
});
```

Второй, необязательный аргумент `forEach` позволяет указать контекст `this` для `callback`. Мы обсудим его в деталях чуть позже, сейчас он нам не важен.
Метод `forEach` ничего не возвращает, его используют только для перебора, как более «элегантный» вариант, чем обычный цикл `for`.

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
В одну строку
```javascript
const sum = (x, y) => x + y;
console.log("Sum =", sum(2, 5));
```

В несколько строк
```javascript
const sum = (x, y) => {
    console.log('test');
    return x + y;
}
console.log("Sum =", sum(2, 5));
```

###### Arguments
Специальный объект, содержащий в себе все аргументы функции. Только для обычных функций
```js
function sumNum(num1, num2) {
    console.log(arguments);
    return num1 + num2;
}

console.log(sumNum(2,3));
// [Arguments] { '0': 2, '1': 3 }
// 5
```

###### Самовызывающиеся функции
Плюс в том, что она вызывается всего один раз.
```javascript
(function(){
    console.log("Привет мир");
}());
```

#### Область видимости
- Локальная
- Глобальная
>[!info] Все переменные и константы, которые объявлены вне функций, являются глобальными.

#### ООП
##### Классы
###### Создание класса
```js
class Person {                   // всегда называем с большой буквы
  // Конструктор для инициализации объекта
  constructor(name, age) {       // зарезервированное слово
    this.name = name;            // Свойство "name" . This указывает на объект этого метода
    this.age = age;              // Свойство "age"
  }

  // Метод класса
  greet() {
    console.log(`Привет, меня зовут ${this.name} и мне ${this.age} лет.`);
  }
}
```

###### Геттеры и сеттеры. 
Обращаемся как к свойству
```js
class Person {
	constructor(name, surname) {    // зарезервированное слово
		this.name = name;           // Свойство "name" . This указывает на объект этого метода
		this.age = surname;         // Свойство "surname"
	}

	set newName(name) {
		this.name = name;
	}

	get fullName() {
		return this.name + " " + this.surname;
	}
}

const p = new Person("Alex", "Nill");

console.log(p);                // Person { name: 'Alex', surname: 'Nill' }

p.newName = "Yana";           // К сеттеру обращаемся как к свойству

console.log(p.fullName);       // Yana Nill  К геттеру обращаемся как к свойству
```

###### Статические методы и свойства
>[!info] Статические нужны для методов или свойств для которых НЕ нужно создавать экземпляр класса. 

```js
class Person {
	static a = 1;

	static hello() {
		return "Hello!";
	}
}

console.log(Person.a);          // 1
console.log(Person.hello());    // Hello!
```

###### Приватные методы и свойства
```js
class Car {
	#vin = 6;                   // Приватное поле класса

	#changeVin() {              // Приватный метод класса
		console.log("change");
	}

	test() {
		this.#changeVin();
	}
}

const car = new Car();
car.test();             // change
console.log(car.vin);   // undefined Обращаясь напрямую свойство недоступно
```

###### Наследование
```js
class Book {
	constructor(title, author) {
		this.title = title;
		this.author = author;
	}

	buy() {
		console.log("Buy");
	}
}

class AudioBook extends Book {
	constructor(title, author, lenMin) {
		super(title, author);               // вызывает конструктор родительского класса
		this.lenMin = lenMin;
	}

	log() {
		console.log(`${this.title} - ${this.lenMin}`);
	}
}

const book = new AudioBook("Lord of the Rings", "Tolkien", 60 * 20);
book.log();     // Lord of the Rings - 1200
book.buy();     // Buy
```

###### Method chaining
Для построения цепочки методов в экземпляре объекта, методы должны возвращать `this`
```js
class Wallet {
	balance = 0;

	add(sum) {
		this.balance += sum;
		return this;               // для method chaining
	}

	remove(sum) {
		this.balance -= sum;
		return this;               // для method chaining
	}
}

const wallet = new Wallet().add(100).remove(25);
console.log(wallet);               // Wallet { balance: 75 }
```

##### Объекты
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

```js
const user = {
    name: 'Vasya',
    surname: 'Poopkin',
    age: 24,
    getFullName: function () {
        return this.name + ' ' + this.surname;
    }
}

console.log(user.getFullName());  // Vasya Poopkin
```

Ещё один способ записи метода объекта (Enhanced Object Literals). Как в классе.
```js
const user = {
    name: 'Vasya',
    surname: 'Poopkin',
    age: 24,
    getFullName() {
        return this.name + ' ' + this.surname;
    }
}
```

###### Свойства объекта
```javascript
user.name = "Tom";
user.age = 26;
user['name'] = "Timmy";
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
const { age, ...userWithoutAge} = user;
const { products } = obj;  // Мы достаём из obj ключ products и присваиваим её константе products

// Смена названия переменной
const { name: title, age } = user;
console.log(title);  // Tom
```

###### Копирование объектов
>[!info] Поскольку в JS объекты - это ссылочные типы, нельзя просто присвоить новой переменной переменную с объектом (в обеих переменных будет просто одна и та же ссылка на объект в куче).

```js
const original = { a: 1, b: { c: 2 } };

// Поверхностное копирование
const shallowCopy = { ...original };
shallowCopy.b.c = 3;
console.log(original.b.c); // 3 (изменилось в оригинале)

// Глубокое копирование
const deepCopy = structuredClone(original);
deepCopy.b.c = 4;
console.log(original.b.c); // 2 (оригинал не изменился)
```

###### Паттерн Модуль
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

#### This
>[!info] *This*- это специальное ключевое слово, которое ссылается на **контекст выполнения**

Куда указывает `this`

|---|Куда указывает|
|---|---|
|метод|На объект этого метода|
|function|`undefined`|
|arrow func|`this` родительского `scope`|
|event listener|DOM элемент к которому он прикреплён|

Для ручного управления контекстом можно использовать функции `call` и `apply`
```js
// Мы вызываем myFunc, но теперь внутри функции this будет указывать на myObj1
myFunc.call(myObj1, arg1, arg2);

// тоже самое, но аргументы передаём в массиве
myFunc.apply(myObj1, [arg1, arg2]);
```

Также есть функция для связывания функции и объекта. По сути тоже самое что и `call`/`apply`, только с отложенным вызовом.
```js
const user = {
	name: 'Vasya',
	surname: 'Poopkin',
	age: 24,
	skills: []
};
  
function addSkill(skill) {
	this.skills.push(skill);
};

// Мы не вызываем функцию, а просто связываем addSkillFunc с addSkill, где this
//    будет указывать на user)
const addSkillFunc = addSkill.bind(user)

// А здесь мы уже выполняем
addSkillFunc("Programming");

console.log(user);
```

#### Строки
>[!info] Строки в JS неизменяемы!

Длина  строки
```js
'Test string'.length
```

Получение символа
```js
'Test string'[3];
// Получение последнего символа
'Test string'.at[-1];  // метод at допускает отрицательные индексы
```

Удаление пробелов
```js
"  привет  ".trim(); // "привет"
```

Изменение регистра
```js
'Test string'.toUpperCase();
'Test string'.toLowerCase();
'Test string'[0].toUpperCase();  // для отдельного символа
```

Замена
```js
const str = string.replace(/([A-Z])/g, ' $1');  // замена по регулярномму выражкению
const str = 'Test string'.replaceAll('t', 'W'); // замена всех сиволов t на W
console.log(str);  // TesW sWring
```

Поиск подстроки если просто нужно проверить есть или нет
```js
'Test string'.includes('bye');     // false
'Test string'.startsWith('Test');  // true
'Test string'.endsWith('bye');     // false
```

Поиск подстроки если нужна позиция(индекс)
```js
s = 'Test string';
console.log(s.indexOf('str'));  // Возвращает индекс вхождения подстроки
console.log(s.indexOf('w'));    // Возвращает -1 если не найдена
```

Поиск нескольких вхождений подстроки.  Нужно запустить `indexOf` в цикле. Каждый раз, получив очередную позицию, начинаем новый поиск со следующей
```js
let str = "Ослик Иа-Иа посмотрел на виадук";
let target = "Иа";

let pos = -1;
while ((pos = str.indexOf(target, pos + 1)) != -1) {
    console.log(pos);
}
```
Также есть похожий метод `str.lastIndexOf(substr, position)` который ищет с конца строки к её началу.

Получение подстроки
```js
s = 'Test string';
console.log(s.slice(2));     // st string
console.log(s.slice(1, 9));  // est stri
```

Заполнение строки
```js
const str = 'Test string';

console.log(str.padStart(20, '*'));       // *********Test string
console.log(str.padEnd(20, '*'));         // Test string*********
str.slice(-4).padStart(str.length, "#");  // Маскировка всей строки, кроме последних 4х символов
```

Повторение
```js
const str = 'Test string';
console.log(str.repeat(3));  // Test stringTest stringTest string
```

Подсчёт символов в строке
```js
function count(string) {  
    const counts = {};  
    
    for (let char of string) {  
        counts[char] = (counts[char] || 0) + 1;  
    }  
    
    return counts;  
}
```

###### Регулярные выражения
1. Когда регулярное выражение известно заранее и не требует динамического создания
```js
const regex = /abc/i; // Поиск "abc" без учёта регистра
```

2. Когда регулярное выражение создаётся динамически или на основе переменных
```js
const regex = new RegExp("шаблон", "флаги");
```

Флаги
- `i` — игнорировать регистр.
- `g` — глобальный поиск (все вхождения).
- `m` — многострочный режим.
- `s` — включает символ новой строки в `.`.
- `u` — поддержка Unicode.
- `y` — поиск с привязкой к позиции (sticky).

Соответствует ли строка регулярному выражению
```js
const regex = /^\d+$/;       // только цифры
const str = "12345";

if (regex.test(str)) {      // .test(` возвращает boolean
  console.log("Строка состоит только из цифр");
} else {
  console.log("Строка содержит не только цифры");
}
```

```js
const regex = /^\d+$/;
const str = "12345";

if (str.match(regex)) {   // .match() возвращает массив совпадений или null, если совпадений нет
  console.log("Совпадение найдено");
} else {
  console.log("Совпадение не найдено");
}
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
console.log(people.at(-1));                   // метод at допускает отрицательные индексы
```

Обращение к элементу двумерного массива
```javascript
people[0][1]
```

###### Предзаполнение массива
```js
const myArray = new Array(5);  // массив из пяти пустых значений
myArray.fill(5);

console.log(myArray);  // [ 5, 5, 5, 5, 5 ]
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
|`at()`|возвращает значение элемента массива по указанному индексу. Отрицательный индекс используется для отсчёта от последнего элемента массива.|
|`concat()`|объединяет элементы двух массивов в один массив
|`includes()`|проверяет, есть ли в массиве значение. Возвращает true/false
|`indexOf()`|возвращают индекс первого включения элемента в массиве. Если не найдено то -1
|`join()`|объединяет элементы массива в строку
|`reverse()`|разворачивает массив на месте
|`map()`|преобразует каждый элемент массива в другое значение на основе функции преобразования. возвращает новый массив
|`reduce(acc, func, startValue)`|применяет указанную функцию к элементам последовательности, сводя её к единственному значению
|`filter()`|создаёт новый массив, содержащий только те элементы, которые удовлетворяют условию. Например `[1,2,3,4].filter(num => num % 2 === 0)`
|`every()`|возвращает `true` если все элементы удовлетворяют условию
|`some()`|возвращает `true` если любой элемент удовлетворяет условию
|`pop()`|удаление элемента с конца массива и его возврат
|`shift()`|удаление элемента с начала массива и его возврат
|`push()`|добавление элемента в конец массива и возврат длины 
|`unshift()`|добавление элемента в начало массива и возврат длины
|`reverse()`|располагает элементы массива в обратном порядке
|`slice(start, end)`|вырезает отдельные элементы из массива. не модифицирует исходный массив. С отрицательные значением считаются элементами с конца
|`splice(start, count)`|вырезает отдельные элементы из массива. модифицирует исходный массив
|`find(elem => elem < 10)`|вернёт первый найденный в массиве элемент, который подходит под условие в переданной колбэк-функции. Если в массиве не найдётся ни одного подходящего элемента, то вернётся значение `undefined`.
|`findIndex(elem => elem < 10)`|возвращает индекс первого найденного в массиве элемента, который подходит под условие переданной функции. Если же ни одного подходящего элемента не найдётся, то метод вернёт -1
|`flat()`|Делает из вложенных массивов один плоский
|`sort((a, b) => a - b)`|сортирует массив на месте. может принимать функцию для сортировки

Удаление произвольного элемента массива
```js
const arr = ['a', 'b', 'c', 'd'];
const indexToRemove = 2;

arr.splice(indexToRemove, 1); // начиная с индекса 2 удаляет 1 элемент
console.log(arr); // ['a', 'b', 'd']
```

![[js_arrays.jpg]]

###### Деструктуризация массивов
```js
const [a, b] = [1, 2];
console.log(a);  // 1
console.log(b);  // 2
```
```js
const [a, _, b] = [1, 2, 3];
console.log(a);  // 1
console.log(b);  // 3
```
```js
const [one, two] = [1, 2, 3, 4, 5, 6];
console.log(one);  // 1
console.log(two);  // 2
```
```js
const [one, two, ...others] = [1, 2, 3, 4, 5, 6];
console.log(one);     // 1
console.log(two);     // 2
console.log(others);  // [3, 4, 5, 6]
```

###### Rest оператор
>[!info] Rest-оператор используется для *сбора* оставшихся элементов массива, объекта или параметров функции в одну переменную.

Пример с деструктуризацией массива
```js
const users = ["Tom", "Sam", "Bob"];
const [firstUser, ...others] = users;  // СОБИРАЕТ оставшиеся элементы в массив

console.log(firstUser);  // Tom
console.log(others);     // ['Sam', 'Bob']
```

###### Spread оператор
>[!info] Spread-оператор *распаковывает* значения из одного массива или объекта в другое место.

`...массив`
```javascript
const users = ["Tom", "Sam", "Bob"];
console.log(...users);  // Tom Sam Bob РАСПАКОВЫВАЕТ элементы массива
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

###### метод массива .map()
Преобразует каждый элемент массива в другое значение на основе функции преобразования. Возвращает новый массив
```js
let arr = ["Яблоко", "Апельсин", "Груша"];

const newArr = arr.map((elem, index) => elem + ' New');
console.log(newArr);  // [ 'Яблоко New', 'Апельсин New', 'Груша New' ]
```

###### метод массива .filter()
Создаёт новый массив, содержащий только те элементы, которые удовлетворяют условию.
```js
const operations = [100, -20, 7, -15, 50];

const positiveOperations = operations.filter(operation => operation > 0);

console.log(positiveOperations);  // [ 100, 7, 50 ]
```

###### метод массива .reduce()
Применяет указанную функцию к элементам последовательности, сводя её к единственному значению
```js
// Сумма всех элементов массива
const operations = [100, -20, 7, -15, 50];

const finalBalance = operations.reduce((acc, value) => acc + value, 0); // 0 нач. значение аккумулятора

console.log(finalBalance);  // 122
```

###### Сортировка
>[!info] Метод `sort()` сортирует массив на месте. По умолчанию сортируются только строки. Для другой сортировки нужно передать свою функцию.

Сортировка массива чисел
```js
 const nums = [5, 2, 3, 1, 0];  
  
nums.sort((a, b) => a - b);  
console.log(nums);  // [ 0, 1, 2, 3, 5 ]
```

Сортировка по нескольким признакам
```js
const words = ['яблоко', 'банан', 'апельсин', 'авокадо'];

words.sort((a, b) => {
  const alphaCompare = a.localeCompare(b, 'ru');    // первый признак: алфавит
  if (alphaCompare !== 0) return alphaCompare;
  
  return a.length - b.length;                       // второй признак: длина строки
});

console.log(words);  // ['апельсин', 'авокадо', 'банан', 'яблоко']
```

Универсальная сортировка
```js
const words = ['яблоко', 'банан', 'апельсин', 'авокадо'];

// Массив функций-компараторов
const comparators = [
  (a, b) => a.localeCompare(b, 'ru'), // 1-й признак: алфавит
  (a, b) => a.length - b.length       // 2-й признак: длина
];

words.sort((a, b) => {
  for (const cmp of comparators) {
    const result = cmp(a, b);
    if (result !== 0) return result; // если текущий признак различает, возвращаем результат
  }
  return 0; // все признаки равны
});

console.log(words);
// ['апельсин', 'авокадо', 'банан', 'яблоко']

```

#### Структура Set
Множество. Уникальность только для примитивных типов.
```js
const flights = ["Russia", "USA", "London", "London", "USA"];
const counties = new Set(flights);

  
counties.delete("USA");
counties.add("Germany");

  
console.log(counties.has("Russia")); // true
console.log(counties.size);

users.clear();

for (const country of counties) {
	console.log(country);
}

console.log([...counties]);      // типичный способ распаковки  Set
```

#### Структура Map
Хранит отображение и позволяет задействовать любые типы ключей.
```js
let ages = new Map();
ages.set("Борису", 39);
ages.get("Борису");      // 39
ages.has("Борису");      // true
ages.size;               // 1

for (const [key, value] of weatherMap) {
	console.log[key];
	console.log[value];
}

console.log([...weatherMap]);
console.log([...weatherMap.keys()]);
console.log([...weatherMap.values()]);
```

Создание Map из объекта
```js
const weatherObject = {
  London: 10,
  Moscow: 7,
  Paris: 14,
};

const weatherMap = new Map(Object.entries(weatherObject));
```

![[map_vs_objects.png]]

#### Структура WeakMap
Ключевые отличия от Map:
1.  Ключи - только объекты
2. При удалении объекта использующегося в качестве ключа, удаляет соотв. ключ-значение

>[!tip] Удобно использовать структуру в качестве кэша

#### Структура WeakSet
Ключевые отличия от Set:
1.  Ключи - только объекты
2. При удалении объекта использующегося в качестве ключа, удаляет соотв. значение

#### Время и дата
###### Дата
```js
console.log(new Date(2025, 1, 14));  // 2025-02-13T21:00:00.000Z 
```

```js
// new Date(year, month, date, hours, minutes, seconds, ms)
let now = new Date();
console.log(now);    // 2025-11-16T20:24:06.093Z
console.log(now.getFullYear());
console.log(now.getMonth());
console.log(now.getDate());  // число
console.log(now.getDay());   // день недели
console.log(now.getHours());
console.log(now.getMinutes());
console.log(now.getSeconds());
console.log(now.getMilliseconds());

let date = new Date("2017-01-26");
console.log(date);   // 2017-01-26T00:00:00.000Z
```

Установка временного интервала
```js
let now = new Date();              // текущее время
now.setHours(now.getHours() + 3);  // добавляем 3 часа
console.log(now);
```

```js
let date1 = new Date(2025, 1, 15);
let date2 = new Date(2025, 1, 18);

console.log(date2 - date1); // разница в мс

function getDaysBetweenDates(date1, date2) {
	return (date2 - date1) / (1000 * 60 * 60 * 24);
}

console.log(getDaysBetweenDates(date1, date2));
```

Сравнение дат
```js
let date1 = new Date(2025, 1, 15);
let date2 = new Date(2025, 1, 18);

console.log(date2 > date1);
console.log(date2.getTime() === date1.getTime());  // сравниваем Timestamp
```

Интернационализация дат. [Подробнее](https://doka.guide/js/intl-datetimeformat/)
```js
let now = new Date();
let locale = navigator.language;

console.log(locale); // ru-RU

const options = {
	year: "2-digit",
	hour: "numeric",
	minute: "numeric",
	month: "long",
	weekday: "short",
};

console.log(new Intl.DateTimeFormat(locale, options).format(now));  // ноябрь 25 г. пт в 06:54
```

###### Замер времени
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

#### Таймеры
>[!info] Таймеры не блокируют выполнение программы, это происходит *асинхронно*.

```js
setTimeout(() => {
	console.log("Boom!");
}, 3000);  // задержка в мс
```

```js
const boomTimer = setTimeout(() => {
	console.log("Boom!");
}, 3000);  // задержка в мс

boomTimer;  // таймер выполняется только один раз и может быть запущен только после clearTimeout

clearTimeout(boomTimer);
```

#### Интервалы
>[!info] Очень похоже на таймеры, но предназначены для *периодического* выполнения кода.

```js
// Задаём периодическое исполнение console.log("Boom!"); каждые 2 сек
setInterval(() => {
	console.log("Boom!");
}, 2000);

// Задаём таймер, очищающий интервал через 5 сек
setTimeout(() => {
	clearInterval(boomInterval);
}, 5000);
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
>[!info] DOM - объектная модель документа, которая представляет все содержимое страницы в виде объектов, которые можно менять. Объект document – основная «входная точка». С его помощью мы можем что-то создавать или менять на странице. DOM - не часть JavaScript это один из WEB API. 

Например
```javascript
// заменим цвет фона на красный,
document.body.style.background = "red";
```

В соответствии с объектной моделью документа («Document Object Model», коротко DOM), каждый HTML-тег является объектом.
 Вложенные теги являются «детьми» родительского элемента. Текст, который находится внутри тега, также является объектом.
Все, что есть в HTML, даже комментарии, является частью DOM

События жизненного цикла DOM:
```js
document.addEventListener("DOMContentLoaded", function (e) {
	console.log("DOMContentLoaded");
});

window.addEventListener("load", function (e) {
	console.log("load");
});
```

>[!info] Событие `DOMContentLoaded` происходит, когда браузер разобрал HTML-страницу и составил DOM-дерево. Если нужно, чтобы страница обязательно загрузилась полностью, лучше присмотреться к `load`.

###### Получение произвольных элементов
```javascript
document.getElementById(id)
document.querySelector()                 // возвращает первый элемент, соответствующий данному
                                         //   CSS-селектору
document.querySelectorAll(css)           // возвращает все элементы внутри elem, 
                                         //   удовлетворяющие данному CSS-селектору.
                                         //   например document.querySelectorAll('.chapter')
document.querySelectorAll(css)[0]        // получение первого элемента
                              
elem.getElementsByClassName(className)
document.getElementsByTagName('input')
```

###### Получение связанных элементов
DOM-коллекции доступны только для чтения
Коллекции перебираются циклом `for..of`
Для проверки наличия дочерних узлов существует также специальная функция `elem.hasChildNodes()`
Свойства `firstChild` и `lastChild` обеспечивают быстрый доступ к первому и последнему дочернему элементу.
```js
const wrapper = document.querySelector('.wrapper');

console.log(wrapper.children);                     // коллекция детей
console.log(wrapper.parentElement);                // родительский элемент
console.log(wrapper.previousElementSibling);       // предыдущий на этом же уровне
console.log(wrapper.nextElementSibling);           // следующий на этом же уровне
console.log(wrapper.closest('.wrapperParent'));    // ближайщий родитель по селектору
```

###### Создание элемента
```javascript
let div = document.createElement('div');
div.className = "alert";
div.innerHTML = "<strong>Всем привет!</strong> Вы прочитали важное сообщение.";
document.querySelector('body').appendChild(div);
```

```js
const newElem = document.createElement('button');
newElem.innerText = 'Mew button';
document.querySelector('body').appendChild(newElem);
```

###### Методы вставки
1. Вставляется как элемент

**appendChild()** Добавляет элемент в конец выбранного родителя.
```js
const parent = document.body;
parent.appendChild(newElem);
```

**insertBefore()** Добавляет элемент перед указанным узлом внутри родителя.
```js
const parent = document.body;
const referenceNode = document.querySelector('someSelector'); // элемент, перед которым нужно вставить
parent.insertBefore(newElem, referenceNode);
```

**append()**  Добавляет элемент в конец родителя (поддерживает несколько элементов).
```js
const parent = document.body;
parent.append(newElem);
```

**prepend()** Добавляет элемент в начало родителя.
```js
const parent = document.body;
parent.prepend(newElem);
```

**before()** Вставляет элемент **перед** выбранным узлом (на одном уровне).
```js
const existingElement = document.querySelector('#someElement');
existingElement.before(newElem); // newElem появится перед existingElement
```

**after()** Вставляет элемент **после** выбранного узла (на одном уровне).
```js
const existingElement = document.querySelector('#someElement');
existingElement.after(newElem); // newElem появится после existingElement
```

**replaceWith()** Заменяет выбранный элемент на новый.
```js
const oldElement = document.querySelector('#oldButton');
oldElement.replaceWith(newElem); // newElem заменит oldElement
```

2. Вставляется как HTML
```js
elem.insertAdjacentHTML(where, html)
```

`"beforebegin"` – вставить html непосредственно перед elem,
`"afterbegin"` – вставить html в начало elem,
`"beforeend"` – вставить html в конец elem,
`"afterend"` – вставить html непосредственно после elem.

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

#### BOM (Browser Object Model)
>[!info] Это дополнительные объекты, предоставляемые браузером (окружением), чтобы работать со всем, кроме документа.

Например
```javascript
navigator.userAgent            // информация о текущем браузере
navigator.platform             // информация о платформе
navigator.geolocation.GetCurrentPosition((position) => {
	console.log(position), 
	(err) => console.error(err)
});                            // координаты
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

document.documentElement.clientWidth  // ширина окна БЕЗ полосы прокрутки
document.documentElement.clientHeight // высота окна БЕЗ полосы прокрутки

window.scrollBy(0, 10)           // прокручивает страницу относительно её текущего положения(на 10px вниз)
window.scrollTo(0, 0)            // прокручивает страницу на абсолютные координаты

window.scrollTo({
  top: 100,
  left: 0,
  behavior: "smooth"             // "smooth"/"instant"
});

elem.scrollIntoView(top)         // прокрутить так, чтобы elem был на первой строчке сверху

elem.getBoundingClientRect()     // получить размеры и позицию элемента
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
**Обработчик может быть назначен прямо в разметке**, в атрибуте, который называется ==on<событие>==(Используется редко)
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

```js
// Объект события автоматически передаётся в аргументы
function changeParagraph(e) {
	paragraph.textContent = 'New text content';
	console.log(e);
}

button.addEventListener('click', changeParagraph);
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

###### Обработчик событий клавиатуры
```js
input.addEventListener('keydown', (e) => {
	if (e.code == 'Enter') {
		myFunc();
	}
});
```

###### Всплытие событий
>[!info] Когда на элементе происходит событие, обработчики сначала срабатывают на нём, потом на его родителе, затем выше и так далее, вверх по цепочке предков.

![[events-model-1200w.webp]]

Самый глубокий элемент, который вызывает событие, называется _целевым_ элементом, и он доступен через `event.target`.

Каждый обработчик имеет доступ к свойствам события `event`:
- `event.target` – самый глубокий элемент, на котором произошло событие.
- `event.currentTarget` (=`this`) – элемент, на котором в данный момент сработал обработчик (тот, на котором «висит» конкретный обработчик)

Любой обработчик может остановить событие вызовом `event.stopPropagation()`, но делать это не рекомендуется, так как в дальнейшем это событие может понадобиться, иногда для самых неожиданных вещей.

###### Делегирование событий
Пример создания 100 `div` и установка всего одного `EventListener` для обработки каждого из них. Делегируем выполнение родительскому элементу.
```js
const wrapper = document.querySelector('.wrapper');

// Создаём 100 элементов
for (let i = 0; i < 100; i++) {
	const elem = document.createElement('div');
	elem .innerHTML = `User id ${i}`;
	elem.setAttribute('data-id', i);

	wrapper.append(elem);
}

// создаём слушателя для родительского элемента
// из-за всплытия событий клик всё равно всплывает в родительский элемент
wrapper.addEventListener('click', (e) => {
	const dataId = e.target.getAttribute('data-id'); // получаем атрибут именно целевого элемента
	console.log(`Deleted user ${dataId}`);
})
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

#### Замыкания
Функция помнит в каком контексте она была создана и может его использовать
```js
function power(pow) {
    return function(num) {
        return num ** pow;
    }
}

const power = pow => num => num ** pow // Аналог функции выше

const powerOfTwo = power(2);  // здесь powerOfTwo уже является функцией function(num)
                              //    но с сохранённым контекстом (значением аргумента pow = 2)
console.log(powerOfTwo(5));   // 25
console.log(powerOfTwo(10));  // 100

console.log(power(3)(2))      // 8
```

```js
function changeBalance() {
	let balance = 0;
	return function(sum) {
		balance += sum;
		console.log(`Balance: ${balance}`);
	}
} 

const change = changeBalance();  // Здесь change является результатом выполнения функции
                                 //   changeBalance, т.е. анонимной функцией(НЕ выполненной),
                                 //   которая ЗАХВАТЫВАЕТ при создании и контекст. И самое
                                 //   главное может этот контекст использовать.
change(100);  // Balance: 100
change(50);   // Balance: 150
```

#### Промисы
>[!info] Promise - это контейнер для значения, которое вернётся в будущем.

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
// В промис передаются 2 функции (для успешного выполнения и для ошибки)
   let promise = new Promise((resolve, reject) => {
     if (new Date() < new Date('01/01/2030')) {
       reject(new Error('Error'));
     }
     resolve('Success');
   });

   // resolve запустит первую функцию, переданную в .then
   promise.then(data => console.log(data))
          .catch(error => console.log(error))
   );
```

```js
fetch("https://dummyjson.com/products")
	.then((response) => response.json())
	.then((data) => {
		console.log(data);
	});
```
**.catch**
   Если мы хотели бы только обработать ошибку. Если хотят отловить ошибку на любой стадии в цепочке промисов. Ставят в самом конце
```javascript
promise.catch(alert);      // выведет "done!" спустя одну секунду
```

```js
fetch("https://dummyjsons.com/products")
	.then((response) => response.json())
	.then((data) => {
		console.log(data);
	})
	.catch((error) => console.log("CATCH:" + error));
```

**.finally**
выполнится в любом случае, когда промис завершится: успешно или с ошибкой
```js
fetch("https://dummyjsons.com/products")
	.then((response) => response.json())
	.then((data) => {
		console.log(data);
	})
	.catch((error) => console.log("CATCH:" + error))
	.finally(() => {
		console.log("Finally");
	});
```

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

###### Генерация ошибок
```js
fetch("https://dummyjson.com/products/dasdasds")  // адрес с ошибкой для примера
	.then((response) => {
		if (!response.ok) {
			throw new Error(`This is error ${response.status}`);  // Ошибка высплывает наверх в catch()
		}
		return response.json();
	})
	.then((data) => {
		console.log(data);
	})
	.catch((error) => console.log("CATCH:" + error))
	.finally(() => {
		console.log("Finally");
	});
	
// Вывод:
// CATCH:Error: This is error 404
// Finally
```

Пример кода
```js
// делаем вспомогательную функцию
function getData(url, errorMessage, method = "GET") {
	return fetch(url, { method }).then((response) => {
		if (!response.ok) {
			throw new Error(`${errorMessage} ${response.status}`);
		}
		return response.json();
	});
}

getData("https://dummyjson.com/products/dsadsa")
	.then(({ products }) => {
		console.log(products);
	})
	.catch((error) => console.log("CATCH:" + error))
	.finally(() => {
		console.log("Finally");
	});
```

###### Promise.*all*(), Promise.*allSettled*() и Promise.*race*()

| метод| описание|
|---|---|
|**all()** |Если один из промисов падает с ошибкой, падает всё
|**allSettled()** |Возвращает массив с  `fullfilled` и `rejected`
|**race()** |Возвращает самый первый результат (это может быть и успешный и упавший)

```js
async function main() {
	const { products } = await getAllProducts();
	const res1 = await Promise.all([
		getProduct(1),
		getProduct(2)]
	);

	const res2 = await Promise.allSettled([
		getProduct(1),
		getProduct(2)
	]);

	const res3 = await Promise.race([
		getProduct(1),
		getProduct(2)
	]);
}

  

main();
```

#### Асинхронные функции
###### ASYNC
>[!info] У слова `async` один простой смысл: эта функция всегда возвращает промис

```javascript
async function f() {
    return 1;
}
```
эта функция возвратит выполненный промис с результатом 1

###### AWAIT
Работает только в теле функций
`await` заставит интерпретатор JavaScript ждать до тех пор, пока промис справа от `await` не выполнится. После чего оно вернёт его результат, и выполнение кода продолжится.
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

Пример 30ти запросов асинхронно
```js
async function getAllProducts() {
	const response = await fetch("https://dummyjson.com/products");
	return response.json();
}

async function getProduct(id) {
	const response = await fetch("https://dummyjson.com/products/" + id);
	return response.json();
}

async function main() {
	const { products } = await getAllProducts();
	// Promise.all принимает массив промисов и запускает их одновременно
	const res = await Promise.all(products.map((product) => getProduct(product.id)));

	console.log(res);
}

main();
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

#### Local Storage
>[!info] **Local Storage** - это механизм в браузере для долговременного хранения данных на стороне клиента (в рамках одного домена). Эти данные сохраняются даже после закрытия браузера и перезагрузки страницы, в отличие от **Session Storage**, который очищается после закрытия вкладки. НЕ предназначено для хранения конфиденциальной информации. Например используется для хранения настроек пользователя (тема, язык), кэширования данных для оффлайн работы, хранения токенов авторизации.

Основные особенности Local Storage:
- **Объём:** Обычно до **5 МБ** на домен (зависит от браузера).
- **Тип данных:** Хранит только **строки**. Если нужно сохранить объект или массив, их сначала преобразуют в строку (например, с помощью `JSON.stringify()`).
- **Доступность:** Данные доступны только для страниц одного протокола и домена.
- **Срок хранения:** Данные не имеют срока годности и сохраняются до явного удаления.

|Метод|Описание|
|---|---|
|`localStorage.setItem(key, value)`|Сохраняет пару ключ-значение.|
|`localStorage.getItem(key)`|Получает значение по ключу.|
|`localStorage.removeItem(key)`|Удаляет значение по ключу.|
|`localStorage.clear()`|Очищает всё хранилище.|
|`localStorage.key(index)`|Получает ключ по индексу.|

```js
// Сохранить данные
localStorage.setItem('username', 'UFODriver');

// Получить данные
const username = localStorage.getItem('username');
console.log(username); // 'UFODriver'

// Удалить данные
localStorage.removeItem('username');

// Очистить всё
localStorage.clear();
```

Хранение объектов
```js
const user = { name: 'UFODriver', age: 30 };
localStorage.setItem('user', JSON.stringify(user));

// Чтение объекта
const savedUser = JSON.parse(localStorage.getItem('user'));
console.log(savedUser.name); // 'UFODriver'
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
**Node Package Manager**
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
npm search http          # поиск пакета
npm install react        # установить библиотеку
npm install -g rollup    # установить библиотеку глобально
npm install -D rollup    # установить библиотеку как DEV
npm uninstall react      # удалить библиотеку
npm uni -g rollup        # удалить глобальную библиотеку

npm cache clean          # очистка кэша
npm audit                # посмотреть детально предупреждения о пакетах
npm audit fix --force    # фиксит уязвимые версии, скачивая новые
npm dedup                # убиает дубликаты

npm list -g --depth=0    # список глобальных зависимостей
```

#### Сборка
>[!info] Сборщики (или **билд-инструменты**, **bundlers**) в JavaScript — это инструменты, которые **объединяют, оптимизируют и трансформируют** исходный код и ресурсы (JS, CSS, HTML, изображения и т.д.) в готовую форму для развертывания в браузере или на сервере.

###### Rollup
```bash
npm i -g rollup
```

```bash
rollup app.js --file bundle.js --format iife
```
