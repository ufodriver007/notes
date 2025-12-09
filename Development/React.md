![[Pasted image 20240115022815.jpg|300]]

[Документация](https://react.dev/learn)
[Метанит](https://metanit.com/web/react/)

###### Начало работы
>[!info] Чтобы создать React-приложение и начать с ним работать, нужно:
>- Node.js (среда выполнения)
>- NPM (пакетный менеджер)
>- Инструмент для создания проектов и работы с ними(`Vite` или устаревший `Create-react-app`)

###### Устанавливаем node js
>[!info] Node.js — это среда выполнения JavaScript-программ, построенная на движке Chrome V8. Раньше на JS можно было делать только интерактивные сайты, так как это специализированный браузерный язык программирования. С программной платформой Node.js появилась возможность создавать «безбраузерные» приложения для компьютера. Это значит, что при необходимости на JavaScript теперь можно делать то же, что на Python и других скриптовых языках программирования общего назначения.

Проверка, установлен ли Node
```bash
node -v
```
---
Установка

На [сайте](https://nodejs.org/en/download) есть инструкция, как установить node.js с помощью NVM (Node Version Manager)

```bash
curl -sL https://deb.nodesource.com/setup_20.x -o nodesource_setup.sh
```

```bash
sudo bash nodesource_setup.sh
```

```bash
sudo apt install nodejs
```
>[!info] Автоматически установится `npm` - пакетный менеджер для node js

[Статья на DigitalOcean по установке node js](https://www.digitalocean.com/community/tutorials/how-to-install-node-js-on-ubuntu-20-04-ru)

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

###### Создаём react-приложение с помощью create-react-app
>[!info] Код react-приложения с помощью `create-react-app` и `node js` конвертируется в обычный JavaScript, который уже потом скармливается браузеру.

```bash
npx create-react-app@5.0.1 my-app              
```
```bash
cd my-app
```
```bash
npm run start
```
Затем в браузере откройте [http://localhost:3000/](http://localhost:3000/)

###### Создаём react-приложение с помощью Vite
[Документация](https://vite.dev/guide/)
[Использование вместе с React](https://www.dev-notes.ru/articles/react/guide-to-using-vite-with-react/)
>[!info]  Это современный инструмент для сборки фронтенд-проектов, который делает разработку быстрее и удобнее. Предназначен для работы с современными JavaScript-фреймворками, такими как Vue, React, Svelte и др.

```bash
npm create vite@latest my-app -- --template react
```

Далее вводим
```bash
cd my-app
npm install            # установить зависимости(node_modules) из package.json
npm run dev
```

Для того чтобы сделать билд
```bash
npm run build
```

#### React Dev Tools
[Ссылка для Chrome](https://chromewebstore.google.com/detail/react-developer-tools/fmkadmapgofadopljbjfkapdkoienihi)
После установки расширения браузера на сайте с React в инструментах разработчика появятся 2 дополнительные вкладки

#### Import и export
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

###### Default export
Обычный способ подключения React компонентов. Может быть только один на модуль (файл).

В файле источнике пишем
```jsx
const App = () => {
    return "Hello, world!";
}
export default App
```

В файле, куда хотим подключить(Если реакт-компонент, то в `'src/index.js'` )
```jsx
import App from './App';

// подключаем импортированный компонент как тег
const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(
  <React.StrictMode>
    <App></App >
  </React.StrictMode>
);
```

#### CSS
###### Импорт CSS
```js
import './index.css'
```

###### Инлайновые стили 
Передаём объектом
```jsx
// JSX
<div style={{ fontSize: "40px" }}>Hello, world!</div>
```

###### Модульные стили
>[!info] Класс стиля будет привязан к определённому компоненту, потому что сгенерируется уникальный ключ. И можно будет лаконично назвать свой класс стиля и он не будет конфликтовать с др. классами.

Создаём файл стилей `MyComponent.module.css` (Имя должно быть такое же как у компонента)
```css
.mystyle {
    font-size: 3.2em;
    line-height: 1.1;
    color: green;
}
```

Импортируем в требуемый компонент
```jsx
import styles from "./MyApp.module.css";
```

Используем
```jsx
<h1 className={styles.mystyle}>My component!</h1>
```

В браузере после рендера будет что-то вроде
```jsx
<h1 class="_myh_16g1c_1">My component!</h1>
```

#### Tailwind
>[!info] Tailwind - это CSS-фреймворк, ориентированный на утилиты, оснащенный такими классами, как `flex`, `pt-4`, `text-center` и `rotate-90`, которые можно скомпоновать для создания любого дизайна непосредственно в вашей разметке.

[Установка Tailwind](https://tailwindcss.ru/docs/installation/using-vite)

Устанавливаем Tailwind
```bash
npm install tailwindcss @tailwindcss/vite
```

Настраиваем плагин Vite
```jsx
// vite.config.js
import { defineConfig } from 'vite'
import tailwindcss from '@tailwindcss/vite'

export default defineConfig({
	plugins: [ 
		tailwindcss(),
	],
})
```

Импортируем Tailwind CSS в `index.css`
```css
@import "tailwindcss";
```

Начнаем процесс сборки
```bash
npm run dev
```

>[!tip] Убедитесь, что скомпилированный CSS включён в `<head>`

###### Плагины для Tailwind
Для VS Code есть плагин **Tailwind CSS IntelliSense**, который подсказывает классы стилей

Для форматирования и сортировки классов Tailwind:
1. Устанавливаем Prettier и плагин для него
```bash
npm install -D prettier prettier-plugin-tailwindcss
```

2. Создаём файл в корне проекта `prettier.config.cjs` и вписываем туда:
```js
module.exports = {
	plugins: ["prettier-plugin-tailwindcss"],
};
```

**Tailwindcss-typography**
>[!info] [Tailwindcss-typography](https://github.com/tailwindlabs/tailwindcss-typography) — официальный плагин к Tailwind CSS, предоставляющий готовую реализацию типографики для аккуратного стилизованного форматирования заданного текстового контента (например какой-нибудь статьи в новостном сайте).

[Статья](https://olegbarabanov.ru/dev/ispolzovanie-plagina-tailwindcss-typography-dlya-realizacii-tipografiki-na-sajte)

###### Работа с Tailwind
[Документация](https://tailwindcss.ru/docs/colors)
**Настройка своих цветов**
Используйте `@theme` для добавления пользовательских цветов в ваш проект под пространством имен темы `--color-*`:
```css
@import "tailwindcss";

@theme {
	--color-midnight: #121063;
	--color-tahiti: #3ab7bf;
	--color-bermuda: #78dcca;
}
```

**Задание своего размера**
```html
<h1 className="text-[201px]">Header</h1>
```

**Height**
```html
Фиксированная
h-96
h-80
h-64
h-48
h-40
h-32
h-24

Проценты
h-full
h-9/10
h-3/4
h-1/2
h-1/3
```

**Margin и Padding**
```html
p-10          padding 10 * 4
py-10         padding вверх и вниз 
px-10         padding враво и влево
pt-10         padding ввех
pb-10         padding вниз
pr-10         padding справа
pl-10         padding слева
```

```
m-10          margin 10 * 4
my-10         margin вверх и вниз 
mx-10         margin враво и влево
mt-10         margin ввех
mb-10         margin вниз
mr-10         margin справа
ml-10         margin слева
```

**Композиция из классов**
`index.css`
```css
@layer components {
	.hlinks {
		@apply font-semibold text-blue-800 hover:text-red-400;
	}
}
```

Далее можно использовать наш кастомный класс
```jsx
<NavLink className="hlinks" to={"/about"}>About</NavLink>
```

*Однако лучшей практикой будет создание своих React-компонентов для этого*. Например создаём директорию UI и в ней создаём свой компонент
```jsx
import { NavLink } from "react-router-dom";

function NavLinkMenu({ to, children }) {
	return (
		<NavLink className="text-blue-500 hover:text-red-400" to={to}>
			{children}
		</NavLink>
	);
}

export default NavLinkMenu;
```

**Свои шрифты через CDN**
1. Выбираем на [Google fonts](https://fonts.google.com/)
2. Нажимаем `Get font` -> Get embed code -> Web -> `<link>`
3. Записываем полученную директиву `<link>` в  шапку `index.html`
4. Описываем переменную для этого шрифта  в `index.css`
```css
@theme {
	--font-yellowtail: "Yellowtail", cursive;
	--font-roboto-condensed: "Roboto Condensed", sans-serif;
	--font-poppins: "Poppins", sans-serif;
}
```

5. Применяем шрифт на нужном элементе `html` или `jsx`
```html
<body class="font-poppins text-sm text-slate-950">   # Наш новый шрифт
	<div id="root"></div>
	<script type="module" src="/src/main.jsx"></script>
</body>
```

**Адаптивный дизайн**
 [Tailwind - это mobile-first приложение](https://tailwindcss.ru/docs/responsive-design). Поэтому все свойства разрабатывются в первую очередь для самых маленьких размеров. 
 
|Breakpoint prefix|Минимальная ширина вьюпорта|CSS|
|---|---|---|
|`sm`|640px|`@media (min-width: 640px) { ... }`|
|`md`|768px|`@media (min-width: 768px) { ... }`|
|`lg`|1024px|`@media (min-width: 1024px) { ... }`|
|`xl`|1280px|`@media (min-width: 1280px) { ... }`|
|`2xl`|1536px|`@media (min-width: 1536px) { ... }`|

Прмер классов для разных свойств под разные брейкпоинты
```html
// ширина на всю доступню область по умолчанию
// ширина 96*4px для вьюпорта после 768px
// ширина в половину вьюпорта после 1024px
<img className="w-full md:w-96 lg:w-1/2" src="/122.png" />
```

**Group**
Стилизация дочерних элементов
```html
// Когда наводишь мышку на div, текст span становится синим
<div class="group">
	<span class="group-hover:text-blue-600">Name</span>
</div>
```

**Peer**
Стилизация соседних элементов
```html
// Когда наводишь мышку на инпут, текст label становится синим
<input class="peer" type="text id="name" />
<label class="peer-hover:text-blue-600" for="name">Name</label>
```

**Готовые классы анимаций**
[Несколько готовых классов анимации](https://tailwindcss.ru/docs/animation). Добавляются к элементам как и все остальные классы.
```jsx
<span className="animate-pulse">
	{category.name}
</span>
```

#### JSX
>[!info] JSX — расширение языка JavaScript. Позволяет использовать синтаксис HTML внутри вашего кода JavaScript.

>[!warning] В JSX мы не можем тегам писать `class`, вместо этого пишем `className`. Например `<div className="test"></div>`. 
>- Атрибуты также пишем в camelCase ! `<div style={{ fontSize: "40px" }}>Hello, world!</div>`
>- JS код пишем в фигурных скобках. Например `<p>{obj.hello}</p>`
>- Комментарий `{/* */}
`

Под капотом JSX превращается в JS.

###### Передача переменных
```jsx
const App = () => {
    const name = 'Alex'
    
    return (
        <div>
            <p>{name}</p>
        </div>
        );
};

export default App;
```

###### Условия
1. Использование тернарного оператора
```jsx
const App = () => {
    const age = 18
    
    return (
        <div>
            <p>{age === 18 ? 'good' : 'bad'}</p>
        </div>
        );
};

export default App;
```

2. Оператор И
```jsx
function Product({ productObj }) {  // деструктуризация пропса
    return (
     productObj && (  // Если productObj == false, то ничего не вернём
        <div>
            <p>{age === 18 ? 'good' : 'bad'}</p>
        </div>
        )
    );
};

export default App;
```

3. `IF` вне `JSX`
```jsx
const App = () => {
    const age = 18

    if (age < 18) return null;

    return (
        <div>
            <p>Seems like your age is more than 18.</p>
        </div>
        );
};

export default App;
```

4. Через функцию
```jsx
const App = () => {
    const age = 18;
    function choice() {
        if (age === 18) {
            return 'good'    # или например return <div>test</div>
        }
    }
    
    return (
        <div>
            <p>{choice()}</p>
        </div>
        );
};

export default App;
```

###### Циклы
Реакт не понимает объекты JS. Нужно их конвертировать в реакт объекты.
```jsx
const array = [{hello: 'world}, {hello: 'world2}];   # объекты JS
const arrayReactElements = [];

for (let i = 0; i < array.length; i++) {
    const obj = array[i];

    arrayReactElements.push(
        <div>obj.hello</div>                       
    )
}
```

Внутрь JSX надо использовать `map()` либо `reduce()`, т.к. они возвращают результат. 

С помощью `map()`
```jsx
const MENU = [
    {
        name: 'Home',
        link: '/'
    },
        name: 'Products',
        link: '/products'
    {
    }
]

export function App() {
    return (
        {MENU.map(item => (
            <div key={item.link}>{item.name}</div>  // key обязателен всегда
        ))}
    )
}
```

С помощью `reduce()`
```jsx
// reduce()
return (
   <div>
       {array.reduce((acc, obj) => {
           acc.push(
               <p>{obj.hello}</p>
           );

        return acc;
       }, [])}
   </div> 
)
```

#### Создание нового компонента
>[!info] В каждом компоненте должен быть только один элемент (тэг/фрагмент)! Если нужно несколько - делаете вложенную структуру.

>[!info] Имя компонента в React (который является обычной функцией в JS) пишется с большой буквы, чтобы React понимал, что это преобразуется в пользовательский тэг, а тэгт с маленькой буквы - это стандартные HTML тэги.

>[!info] Желательно не делать компоненты гигантскими. Ориентир - 100 строк кода.

- Создаём файл `MyComponent.jsx` с функцией и нужными импортами. В конце файла добавляем
```jsx
export default MyComponent;  // имена функций-компонентов в Реакт пишем с большой буквы
```
Например
```jsx
import { useState } from 'react'

function MyComponent() {
    return (
        <>              // Это React фрагмент. Пустой тег, который никак не обрабатывается
                        // Поскольку нельзя возвращать больше одного элемента
            <h1>New component</h1>
            <p>MyComponent </p>
        </>
    );
}

export default MyComponent
```
- Добавляем импорт в `App.jsx`
- Используем новый компонент как тэг
```jsx
//App.jsx
import { useState } from 'react'
import reactLogo from './assets/react.svg'
import './App.css'
import MyComponent from './MyComponent';

function App() {
    return (
        <>
            <MyComponent />
        </>
    );
}

export default App
```
- Возможно добавляем стили в `MyComponent.css`

###### Как создаётся элемент под капотом
```js
import React from "react";

// тестовая функция компонента React
function Header() {
	return (
		<header>
			<h1>Example</h1>
		</header>
	);
}

// Babel под капотом превращает jsx код(который выше) в такой код js
function Header() {
	return React.createElement("header", null, React.createElement("h1", null, "Example"));
}
```

###### Рендеринг компонента
**Рендеринг компонента** - это процесс, при котором React *вызывает функцию компонента*, чтобы получить описание интерфейса в виде *React-элементов*. Эти элементы используются для создания и обновления **виртуального DOM**, который затем **синхронизируется с реальным DOM**.  Рендеринг запускается при первой загрузке и когда изменяется состояние/изменяются `props` (или когда рендерится родительский компонент). 

Фазы ренднринга:
1. Вызов компонента и получение элементов
2. Построение/обновление виртуального DOM
3. Сравнение виртуального DOM с предыдущей версией

И уже **после** рендеринга наступает следующий шаг - **отрисовка на экране** (*Commit phase*).

#### Props
>[!info] Props (сокр. от *properties*) представляет коллекцию значений, которые ассоциированы с компонентом. Эти значения позволяют создавать динамические компоненты, которые не зависят от жестко закодированных статических данных. Можно представить их как аргументы для функции, только read-only и передаваемые одним объектом

###### Передаём значение
Передаём атрибуты в компонент
```jsx
import { useState } from 'react'
import reactLogo from './assets/react.svg'
import './App.css'

function App() {
    return (
        <>
            <MyComponent name="Alex" age="32"/>
        </>
    )
}

export default App
```

###### Получаем значение
Получаем атрибуты через объект, переданный в аргументах
```jsx
function Hello(props) {
const { name, age } = props;
    return <div>
        <p>Имя: {name}</p>
        <p>Возраст: {age}</p>
    </div>
}
```

###### prop key
>[!warning] Если э**лементы находятся в списке и находятся на одном уровне одновременно** , props должен иметь **ключ**, поскольку если его нет, то React **не может корректно сопоставить элементы** между рендерами. 

###### Пример прокидывания информации с бекэнда
Представим, что `VIDEOS` это массив с объектами, пришедший с бекэнда
В комоненте App в цикле закидываем аргументы
```jsx
import { VIDEOS } from './videos'

function App() {
    return (
        <>
            {VIDEOS.map((video) => (
                <MyComponent 
                 key={video.id}
                 title={video.title}
                 channelName={video.channelName}
                 img={video.img}
                />
            ))}
        </>
    )
}
```

###### Деструктуризация пропсов
Для удобства можно сразу извлекать объект из пропса
```jsx
function Product({ productObj }) {
	...
```

Либо так
```jsx
function Product(props) { 
	const { productObj } = props;
	...
```

Чтобы потом удобнее было обращаться
```jsx
// Не так
<p>{ props.productObj.name }</p>

// А вот так
<p>{ productObj.name }</p>
```


#### Children
>[!info] Можно передать любой контент(в т.ч. и другие компоненты) внутрь своего компонента

Передаём контент внутрь тэга
```jsx
import { DangerButton } from "./components/Buttons.jsx";

export default function App() {
    return (
        <DangerButton>
	        <span>&#11015;</span>Hello world!  // Помещаем контент внутрь
	    </DangerButton>
    );
}
```

Слово `children` зарезервировано. Определяем под него место.
```jsx
// функция компонента
export function DangerButton({ isDisabled = false, children }) {
    return (
        <button
            className={`${isDisabled ? "disabled" : ""} red`}
            onClick={() => alert("Test!")}
        >
            {children}  // Здесь будет весь контент переданный внутрь тэга
        </button>
    );
}
```

#### Memo
>[!info] `React.memo`  предотвращает повторный ререндер **компонента** при тех же пропсах. Если пропс — объект, массив или функция, они будут меняться при каждом рендере, если не стабилизированы через `useMemo` или `useCallback`.

Пример, где при повторном рендере родительского компонента **дочерний компонент** (к примеру очень дорогой по ресурсам) **НЕ ререндерится**.
```jsx
import { useState, useMemo } from "react";

function Parent() {
  const [count, setCount] = useState(0);

  // useMemo кэширует значение если аргументы не изменились
  const user = useMemo(() => ({ name: "Alice" }), []);

  return (
    <div>
      <button onClick={() => setCount(count + 1)}>
        Increment: {count}
      </button>

      <UserInfo user={user} />
    </div>
  );
}

// memo предотвращает рендер компонента если пропсы не изменились
const UserInfo = React.memo(function UserInfo({ user }) {
  // Здесь например загрузка огромного кол-ва информации пользователя
  console.log("UserInfo rendered");
  return <div>User: {user.name}</div>;
});

export default Parent;

```

#### Формы
- Для каждого инпута делаем состояние `useState` и каждому записываем в `value` это состояние
- Делаем обработчик в котором:
	- `e.preventDefault()`
	- Делаем с данными (которые уже хранит `useState`) то что нужно
	- Очищаем все состояния для инпутов

####  Сортировка компонентов
- Делаем состояние в котором храним все типы сортировки
- Делаем состояние в которм храним (`asc`/`desc`)
- Делаем функцию сортировки, которая в зависимости от этих 2х `useState` сортирует нужным образом

#### События
`App.jsx`
```jsx
import React from "react";

const App = () => {
    const onClickHandler = (event) => {
        console.log('click', event);
    };
    const onChangeHandler = (event) => {
        console.log('on change', event.target.value);
    };

    return (
        <form onSubmit = {(event) = {
            event.preventDefault();
            console.log('submit');
        }}>
            Test form
            <input type="text" onChange={onChangeHandler} />
            <button type="submit" onClick={onClickHandler}>BUTTON</button>
            <button onClick={() => alert("Hello!"))}>Alert button</button>
        </form>
    );
};

export default App;
```

|Событие|Описание|
|---|---|
|**События мыши**|**Описание**|
|onClick|Клик левой кнопкой мыши
|onContextMenu|Клик правой кнопкой мыши для вызова контекстного меню
|onDoubleClick|Двойной клик мышью
|onDrag / onDragEnd / onDragEnter / onDragExit / onDragLeave / onDragOver / onDragStart / onDrop|События, связанные с перетаскиванием элементов
|onMouseDown / onMouseEnter / onMouseLeave / onMouseMove / onMouseOut / onMouseOver / onMouseUp|События, связанные с действиями мыши
||
|**События клавиатуры**|**Описание**|
|onKeyDown / onKeyPress / onKeyUp|События нажатия клавиш на клавиатуре|
||
|**События форм**|**Описание**|
|onChange| Изменение значения элемента формы (например, при вводе текста или выборе значения)
|onInput| Ввод данных пользователем в поле ввода
|onSubmit| Отправка формы
||
|**События фокуса**|**Описание**|
|onFocus| Элемент получает фокус
|onBlur| Элемент теряет фокус
||
|**События сенсорного ввода**|**Описание**|
|onTouchCancel / onTouchEnd / onTouchMove / onTouchStart| События, связанные с касаниями на сенсорных устройствах
||
|**События UI**|**Описание**|
|onScroll| Прокрутка элемента или страницы
||
|**События колеса мыши**|**Описание**|
|onWheel| Вращение колесика мыши
||
|**События изображения**|**Описание**|
|onLoad| Загрузка изображения завершена
|onError| Ошибка при загрузке изображения или файла
||
|**Другие события**|**Описание**|
|onSelect| Выделение текста пользователем
|onAnimationStart / onAnimationEnd / onAnimationIteration| События CSS анимаций
|onTransitionEnd| Окончание CSS перехода|

#### Хуки
>[!info] Вспомогательные функции, которые помогают работать с компонентом

**Основные хуки:**

|Хук|Краткое описание|
|---|---|
|**useState**|Позволяет добавлять состояние в функциональный компонент.|
|**useEffect**|Выполняет побочные эффекты (например, fetch, подписки, таймеры) после рендера.|
|**useCallback**|Кэширует функцию, чтобы её ссылка не менялась между рендерами, оптимизация производительности.|
|**useRef**|Хранит мутируемое значение, которое сохраняется между рендерами; используется для доступа к DOM.|
|**useMemo**|Кэширует вычисленное значение между рендерами, оптимизация производительности.|
|**useContext**|Позволяет получать значение из контекста React без проброса через пропсы.|
|**useReducer**|Позволяет управлять сложным состоянием через редьюсер, похож на Redux внутри компонента.|
|**useNavigation**|(React Router) Предоставляет информацию о текущей навигации (например, состояние перехода).|
|**useNavigate**|(React Router) Позволяет программно выполнять навигацию между маршрутами.|
|**useLocation**|(React Router) Получает объект текущего URL (pathname, search, state).|
|**useParams**|(React Router) Получает параметры маршрута из URL (например, `/users/:id`).|
|**useSearchParams**|(React Router) Позволяет читать и изменять query-параметры URL.|

###### useState
**useState** Позволяет сохранять значения между рендерами. Если значение изменено, *перерисовывает* компонент.
```jsx
//App.jsx
import React, { useState } from 'react';
import './index.css';

const App = () => {
    const [isDark, setIsDark] = useState(true);  
        // функция хука возвращ. массив из 2х значений
        // первое значение - это начальная переменная в котор. хранится значение
        // второе значение - функция для обновления переменной(т.н. setState)
        // setIsDark просто желаемое мной название встроен. функции реакта
        // useState(true) здесь true означает начальное состояние isDark

    const onClickHandler = () => {
        setIsDark(!isDark);  // вызывая эту функцию, которая описана в недрах реакта,
                             // мы передаём её свой код
                             // функция исполняет наш код, обновляет состояние компонента
                             // и перерисовывает(!!!) его
    };

    return (
        <div className={isDark ? 'dark' : 'light'}>
            <button onClick={onClickHandler}>Переключить тему</button>
        </div>
    );
};

export default App;
```

Ещё раз
```jsx
const [count, setCount] = useState(0);
// Деструктуризация (распаковка) возвращаемого значения функции useState
//
// count - это будет переменная, которая будет хранить наше значение
//
// setCount - функция, которая изменит нашу переменную:
//  1) на то, что мы ей передадим.
//     Например setCount(33);
//  2) либо передав функцию, которая принимает предидущее значение и возвращает новое
//     Например setCount((prev) => prev + 1);
//
// useState(0) - задаём начальное значение 0
```

Лучшая практика (если нужно установить значение на основе старого значения)
```jsx
const onClickHandler = () => {
        // используем callback
        setIsDark(prev => prev +1);  // Предыдущее состояние изменяется. Так надёжнее, из-за
                                     // асинхронной природы хука
    };
```

>[!tip] Чтобы не плодить в одном компоненте кучу дублирующегося кода с useState, можно передавать один объект с кучей значений

>[!tip] Всегда используем `useState` из возвращаемого значения (JSX), иначе будут постоянные ререндеры в бесконечном цикле

>[!warning] Изменение родительского состояния приводит к ререндерингу всех компонентов-потомков!

###### useEffect
Служит для определённых side эффектов (любое действие, которое выходит за пределы текущей функции и взаимодействует с чем-то снаружи). Мы можем за чем-либо(переменная, координаты мыши и т.п.) наблюдать и выполнять в случае чего нашу логику. *Выполняется после рендера.*

Вот несколько примеров использования:
- Запрос данных из API
- Отрображение индикатора загрузки
- Изменение заголовка страницы
- Подписка на события, такие как изменение размера окна
- Установка таймеров или интервалов
- Чтение и запись в локальное хранилище (localStorage или sessionStorage)
- Получение текущей геолокации пользователя
- Показ системных уведомлений
- Подключение к WebSocket для получения данных в реальном времени
- Изменение глобальных переменных или состояний, доступных за пределами функции.

```jsx
import { useEffect } from "react";
```

Использование без массива зависимостей.** Исполняется после каждого рендера.**
```jsx
useEffect(() => {
    console.log('render')  // выполнится при каждом рендере и если здесь будет код,
                // после которого нужно будет перередерить, то хук выполнит рендер
})
```

С пустым массивом зависимостей. **Исполняется один раз.**
```jsx
useEffect(() => {
    console.log('Component mounted')  // выполнится один раз
}, [])
```

Отслеживание переменной из массива зависимостей. **Исполняется после каждого изменения в массиве зависимостей.**
```jsx
let myVar = 123;

useEffect(() => {
    console.log(myVar)   // выполнится при каждом изменении переменой myVar
}, [myVar])
```

###### Return в useEffect
**Возвращаем функцию очистки**
В `useEffect` если он срабатывает неоднократно (циклично) нужно описывать функцию очистки, чтобы программа не жрала память. Нужно возвращать коллбэк с очисткой. Она выполнится при *размонтировании* компонента.
```jsx
useEffect(() => {
    // основная логика
    console.log('Component mounted')

    // функция очистки
    return () => {
        // логика очистки, например очищаем таймер и т.д.
    }
}, [])
```

| Источник утечки / Что нужно чистить | Почему происходит | Как правильно отписаться |
|-------------------------------------|--------------------|---------------------------|
| Таймеры и интервалы (`setTimeout`, `setInterval`) | Они продолжают работать после удаления компонента | ```return () => clearTimeout(timer);``` |
| Слушатели событий (`addEventListener`) | Обработчики продолжают работать после удаления компонента | ```return () => window.removeEventListener("resize", handleResize);``` |
| WebSocket | Соединение остаётся открытым и передаёт данные | ```return () => socket.close();``` |
| Подписки на данные (например, RxJS) | Подписка продолжает получать данные | ```return () => subscription.unsubscribe();``` |
| Анимации (`requestAnimationFrame`) | Запросы продолжаются | ```return () => cancelAnimationFrame(frameId);``` |
| Геолокация (`watchPosition`) | Отслеживание продолжается | ```return () => navigator.geolocation.clearWatch(watchId);``` |
| Асинхронные операции | Попытка обновить состояние после размонтирования вызывает ошибку | ```return () => { isMounted = false; };``` |
| Внешние API или сторонние библиотеки | Они продолжают использовать ресурсы | ```return () => subscription.unsubscribe();``` |
| Глобальные браузерные события (scroll, keydown и др.) | Обработчики остаются активными | ```return () => window.removeEventListener("scroll", handleScroll);``` |

**Возвращаем прерывание запроса**
Чтобы избежать состояния гонки, передаём в `fetch` сигнал для отмены запроса.
1. Создание `AbortController`: Внутри `useEffect` создаётся новый экземпляр `AbortController`, который предоставляет сигнал (`signal`) для управления отменой запроса.
2. Передача `signal` в `fetch`: В параметрах `fetch` передаётся `signal`, что позволяет отменить запрос, если `AbortController` вызовет `abort()`.
3. Отмена предыдущего запроса: В `useEffect` возвращается функция очистки, которая вызывает `controller.abort().` Это отменяет любой предыдущий запрос, который всё ещё может быть в процессе выполнения, если query изменяется до завершения запроса.
4. Обработка ошибки отмены: Если запрос был отменён, возникает ошибка типа `AbortError`. Мы проверяем её в блоке `catch`, чтобы не отображать сообщение об ошибке пользователю, если запрос был отменён преднамеренно.
```js
function MyComponent() {
  useEffect(() => {
    const controller = new AbortController();  // Создаём AbortController
    const signal = controller.signal;  // Получаем объект сигнала из контроллера

    async function fetchData() {
      try {
        const response = await fetch('https://api.example.com/data', {signal});
        const data = await response.json();
      } catch (err) {
        if (err.name === 'AbortError') {
          console.log('Запрос был отменён');
        } else {
          console.log('Произошла ошибка', err);
        }
      }
    }

  fetchData();

  return () => controller.abort();  // Прерываем запрос при размонтировании или повторном рендере
  }, []);  // Пустой массив зависимостей - эффект выполнится один раз при монтировании
}
```


###### useRef
Позволяет сохранять значения между рендерами. Хук  похож на `useState`, но *не запускает ререндеринг* (в отличии от `useState`). Этим значением может быть ссылка на DOM-элемент.

Увеличение счётчика без дополнительного рендера
```jsx
const renderCounter = useRef(1)  // возвращает объект с
                                 //   единственным атрибутом current
renderCounter.current++
```

Также с его помощью можно получить HTML-элемент, добавив атрибут `ref`
```jsx
import React, {useEffect, useRef} from "react"

functiom App() {
  const inputRef = useRef(null);

  useEffect(() => {
    console.log(inputRef.current);  // теперь можно манипулировать им
    inputRef.current.focus();       // например фокусироваться на нём
  }, []);

  return (
    <div>
      <button ref={inputRef}>Huge BUTTON</button>
    </div>
  );
}
```

###### useMemo
Используется для оптимизации производительности компонента, позволяя мемоизировать вычисленные значения. Это полезно, если вычисление значений требует значительных ресурсов или времени

`useMemo` запоминает результат вычислений до тех пор, **пока не изменятся зависимости**, указанные в массиве зависимостей. Если зависимости не изменились, то возвращается мемоизированное значение из предыдущего рендера.

```jsx
const memoizedValue = useMemo(() => computeExpensiveValue(a, b), [a, b]);

// computeExpensiveValue функция, возвращающая значение, которое нужно мемоизировать.
// [a, b] массив зависимостей. Когда их значения меняются, функция
// computeExpensiveValue вызывается заново.
```

Пример мемоизации
```jsx
import React, { useState, useMemo } from "react";

function ExpensiveComputationComponent() {
  const [count, setCount] = useState(0);
  const [otherState, setOtherState] = useState(0);

  // "Тяжелое" вычисление
  const computeExpensiveValue = (num) => {
    console.log("Выполняется тяжелое вычисление...");
    for (let i = 0; i < 1000000000; i++) {}
    return num * 2;
  };

  // Мемоизация результата
  const memoizedValue = useMemo(() => computeExpensiveValue(count), [count]);

  return (
    <div>
      <h1>Тяжелое значение: {memoizedValue}</h1>
      <button onClick={() => setCount(count + 1)}>Увеличить счетчик</button>
      <button onClick={() => setOtherState(otherState + 1)}>
        Изменить другое состояние
      </button>
    </div>
  );
}

export default ExpensiveComputationComponent;
```

- Функция `computeExpensiveValue` вызывается только тогда, когда `count` изменяется.
- При изменении `otherState` функция **не выполняется повторно**, так как `otherState` не включен в зависимости.

>[!warning] Когда **не нужно** использовать `useMemo`:
> 1.  Если вычисления легковесные — использование `useMemo` не оправдано.
2. Если мемоизация усложняет читаемость кода.
3. Если оптимизация не имеет заметного эффекта (профилируйте перед использованием).

###### useCallback
>[!info] Callback — это функция, переданная другой функции, которая вызывается позже.

**`useCallback`** — это хук в React, который возвращает **мемоизированную** версию функции. Он предотвращает создание новой функции при каждом рендере компонента, что может быть полезно для оптимизации производительности, особенно при передаче функций дочерним компонентам или при работе с `useEffect`.
```jsx
const memoizedCallback = useCallback(
	() => {
	                 // Ваш код функции   
	},
	[dependencies]   // Зависимости
);
```

- **Первый аргумент**: функция, которая будет запоминаться.
- **Второй аргумент**: массив зависимостей, определяющий, когда нужно пересоздать функцию.

Если массив зависимостей пуст (`[]`), функция будет создаваться только один раз — при первом рендере.

`useCallback` полезен в следующих случаях:

1. **Передача функции в дочерний компонент**: Если дочерний компонент оптимизирован с помощью `React.memo`, передача новой функции при каждом рендере может нарушить мемоизацию. Хук `useCallback` предотвращает это, создавая одну и ту же функцию между рендерами, если зависимости не изменились.
    
2. **Использование функций в зависимости от `useEffect` или других хуков**:`useCallback` позволяет избежать повторного создания функции, что может вызывать ненужные эффекты.

###### useContext
**`useContext`** — это хук, который позволяет получать доступ к значениям из контекста без необходимости оборачивать компоненты в `Consumer`. В отличии от глобальных переменных хук автоматически рендерит при изменении состояния(реактивность).

>[!info] Контекст в React — это механизм для передачи данных через дерево компонентов без необходимости явно передавать пропсы на каждом уровне. Например, вы можете передавать глобальные настройки, тему, язык или состояние авторизации.

```jsx
const value = useContext(MyContext);
// MyContext — это объект контекста, созданный с помощью React.createContext.
```

Порядок действий:
1. Сначала создается контекст с помощью `React.createContext`.
2. Значение контекста предоставляется с помощью компонента `Provider`.
3. Любой дочерний компонент может получить доступ к этому значению с помощью хука `useContext`.

Пример смены темы:
```jsx
import React, { createContext, useContext, useState } from "react";

// Создаем компонент-контекст
const ThemeContext = createContext();

// верхнеуровневый компонент с нужным состоянием
function App() {
  const [theme, setTheme] = useState("light");

  return (
    // Предоставляем значение контекста. Оборачиваем необходимые дочерние компоненты
    <ThemeContext.Provider value={{ theme, setTheme }}>
      <Toolbar />
    </ThemeContext.Provider>
  );
}

// Дочерний компонент
function Toolbar() {
  return (
    <div>
      <h1>Toolbar</h1>
      <ThemeSwitcher />
    </div>
  );
}

// Самый вложенный компонент в котором мы и получаем нужное состояние (перменные/функции)
function ThemeSwitcher() {
  // Получаем значение контекста. Это просто объект.
  const { theme, setTheme } = useContext(ThemeContext);

  return (
    <div>
      <p>Текущая тема: {theme}</p>
      <button onClick={() => setTheme(theme === "light" ? "dark" : "light")}>
        Переключить тему
      </button>
    </div>
  );
}

export default App;
```

Пример авторизации пользователя:
```jsx
import React, { createContext, useContext, useState } from "react";

// Создаем контекст для авторизации
const AuthContext = createContext();

function App() {
  const [user, setUser] = useState(null);

  return (
    <AuthContext.Provider value={{ user, setUser }}>
      <Header />
      <Main />
    </AuthContext.Provider>
  );
}

function Header() {
  const { user, setUser } = useContext(AuthContext);

  return (
    <header>
      <h1>Приложение</h1>
      {user ? (
        <p>Добро пожаловать, {user.name}!</p>
      ) : (
        <button onClick={() => setUser({ name: "Иван" })}>Войти</button>
      )}
    </header>
  );
}

function Main() {
  const { user } = useContext(AuthContext);

  return (
    <main>
      {user ? <p>Вы вошли как {user.name}</p> : <p>Пожалуйста, войдите.</p>}
    </main>
  );
}

export default App;
```
Пояснение к коду:
1. Создан контекст для управления состоянием авторизации пользователя.
2. Значение контекста (пользователь и функция для его обновления) предоставляется через `AuthContext.Provider`.
3. Компоненты `Header` и `Main` используют `useContext` для доступа к состоянию авторизации.

**Best practice**
Можно создать отдельный компонент `TaskProvider`,  где будет содержатся весь контекст приложения. 
```jsx
export const TaskContext = createContext();

export function TaskProvider({ children }) {
	const [tasks, setTasks] = useState([]);  // все необходимые переменные/состояния
	
	return (
		<TaskContext.Provider value={{ tasks, setTasks }}>
			{children}        // сюда через children будут проходить все props
		</TaskContext.Provider>
	);
}

export function useTask() {   // сделаем свой хук просто чтобы не ипортировать везде
                              //   useContext и TaskContext. Вместо этого будет один
                              //   импорт - useTask
	const context = useContext(TaskContext);
	
	return context;
}
```

Всё наше приложение оборачиваем в этот компонент
```jsx
import { StrictMode } from "react";
import { createRoot } from "react-dom/client";
import "./index.css";
import App from "./App.jsx";
import { TaskProvider } from "./TaskProvider.jsx";

createRoot(document.getElementById("root")).render(
<StrictMode>
	<TaskProvider>
		<App />
	</TaskProvider>
</StrictMode>
);
```

Далее на любом уровне ниже получаем данные из контекста
```jsx
// App.jsx
import { useTask } from "./TaskProvider";

...
function App() {
	const { tasks, setTasks } = useTask();  // получаем из нашего хука

	return (
...
```

>[!warning] `useContext` не подходит если значение контекста обновляется очень часто, это может привести к повторным рендерам всех компонентов, использующих этот контекст.

###### useReducer
>[!info] **`useReducer`** — это хук, который позволяет управлять сложным состоянием компонента. Он **похож** на `useState`, но представляет **больше возможностей для организации и управления состоянием**, особенно если изменения состояния зависят от различных типов действий. Ререндер происходит при изменении состояния.

```jsx
import { useReducer } from "react";

function reducer(state, action) {  // action - это объект, который передала функция dispatch
	switch (action.type) {
		case "INCREMENT":
		return state + 1;
	case "DECREMENT":
		return state - 1;
	case "RESET":
		return 0;
	case "incrementBy":
		return state + action.payload;
	default:
		return new Error("Invalid action type");
	}
}

function App() {
	const [count, dispatch] = useReducer(reducer, 0);
	// count - это текущее состояние
	// dispatch - просто функция, которая отправляет объект в функцию, 
	//     которую мы создадим (reducer)
	// 0 - начальное значение

	return (
		<div>
			<p>{count}</p>
			<button onClick={() => dispatch({ type: "INCREMENT" })}>+</button>
			<button onClick={() => dispatch({ type: "DECREMENT" })}>-</button>
			<button onClick={() => dispatch({ type: "RESET" })}>Reset</button>
			<button onClick={() => dispatch({ type: "incrementBy", payload: 5 })}>+5</button>
		</div>
	);
}

export default App;
```

Этот же пример, но начальное значение - это объект
```jsx
import { useReducer } from "react";

const initialState = { count: 0, inputValue: "" };

function reducer(state, action) {
	console.log(state);            // { count: 0, inputValue: "" }
	console.log(action);           // {type: 'incrementBy', payload: 5}

	switch (action.type) {
		case "INCREMENT":
			return { ...state, count: state.count + 1 };  // state мутировать нельзя, поэтому
			                                      //  мы создаём новый объект с нужным значением
		case "DECREMENT":
			return { ...state, count: state.count - 1 };
		case "RESET":
			return { ...state, count: 0 };
		case "incrementBy":
			return { ...state, count: state.count + 5 };
		default:
			return new Error("Invalid action type");
	}
}

function App() {
	const [resObj, dispatch] = useReducer(reducer, initialState);
  
	return (
		<div>
			<p>{resObj.count}</p>
			<button onClick={() => dispatch({ type: "INCREMENT" })}>+</button>
			<button onClick={() => dispatch({ type: "DECREMENT" })}>-</button>
			<button onClick={() => dispatch({ type: "RESET" })}>Reset</button>
			<button onClick={() => dispatch({ type: "incrementBy", payload: 5 })}>
				+5
			</button>
		</div>
	);
}

export default App;
```

###### Кастомный хук
>[!info] Кастомных хук должен использовать хотя бы один стандартный хук (`useState`, `useEffect`, `useRef`, `useContext`, и так далее).  Имя функции кастомного хука начинается с префикса `use`

Создаём файл `useFetch.jsx`
```jsx
import { useState, useEffect } from "react";

function useFetch(url) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    async function fetchData() {
      try {
        const response = await fetch(url);
        if (!response.ok) {
          throw new Error(`HTTP Error $(response.status)`);
        }
        const result = await response.json();
        setData(result);
      } catch (err) {
        setError(err.message);
      } finally {
        setLoading(false);
      }
    }

    fetchData(url);
  }, [url]);

  return { data, loading, error };  // { data: data, loading: loading, error: error }
}

export default useFetch;
```

Использование хука в компоненте
```jsx
import useFetch from "./useFetch";

function Posts() {
  const { data, loading, error } = useFetch("https://jsonplaceholder.typicode.com/posts");

  if (loading) return <p>Loading...</p>
  if (error) return <p>Error: {error}</p>

  return (
    <ul>
      {data.map((post) => (
        <li key={post.id}>{post.title}</li>
      ))}
    </ul>
  );
}

export default Posts;
```

## Redux
>[!info]  Redux - библиотека, предназначенная для управления состоянием в больших приложениях.

```bash
npm install @reduxjs/toolkit react-redux
```

[Redux DevTools](https://chromewebstore.google.com/detail/redux-devtools/lmhkpmbekcpmknklioeibfkpmmfibljd)

Основные концепции Redux
1. **Store** — единый объект состояния для всего приложения. Он содержит всё состояние приложения.
2. **Actions** — объекты, которые описывают события или изменения, которые должны произойти в состоянии.
3. **Reducers** — чистые функции, которые принимают текущее состояние и действие, а затем возвращают новое состояние.
4. **Dispatch** (отправка действий) — способ отправить действие в хранилище, чтобы редьюсер обработал его и обновил состояние.
5. **Selectors** — функции, которые извлекают нужные данные из состояния для использования в компонентах.

#### Создаём хранилище
```js
// store.js
import { configureStore } from "@reduxjs/toolkit";
import userListReducer from "./features/usersList/userListSlice";
import userDetailsReducer from "./features/usersDetails/userDetailsSlice";

const store = configureStore({
	reducer: {
		userList: userListReducer,
		userDetails: userDetailsReducer,
	},
});

export default store;
```

#### Создание редьюсера и экшенов
```js
// userListSlice.js
import { createSlice } from "@reduxjs/toolkit";

const initailState = {
	users: [],
	loading: false,
	error: null,
};

// Создаём Slice - это часть состояния Redux
const userListSlice = createSlice({
	name: "userList",
	initialState: initailState,
	reducers: {
		addUser(state, action) {
			state.users.push(action.payload);
		},
		deleteUser(state, action) {
			state.users = state.users.filter((elem) => elem.id !== action.payload);
		},
	},
});

export const { addUser, deleteUser } = userListSlice.actions;
export default userListSlice.reducer;
```

#### Работа с асинхронными запросами
```js
// userListSlice.js
import { createAsyncThunk, createSlice } from "@reduxjs/toolkit";  
  
const initailState = {  
    users: [],  
    loading: false,  
    error: null,  
};  
  
export const fetchUsers = createAsyncThunk(  
    "userList/fetchUsers",  
    async (_, { rejectWithValue }) => {  
        const res = await fetch("https://jsonplaceholder.typicode.com/users");  
  
        if (!res.ok) {  
	        // Здесь можно просто: throw new Error("Failed to fetch info");
            return rejectWithValue({  
                status: res.status,  
                message: "Failed to fetch users",  
            });  
        }  
        return res.json();  
    }  
);  
  
const userListSlice = createSlice({  
    name: "userList",  
    initialState: initailState,  
    reducers: {  
        addUser(state, action) {  
            state.users.push(action.payload);  
        },  
        deleteUser(state, action) {  
            state.users = state.users.filter((elem) => elem.id !== action.payload);  
        },  
    },  
    extraReducers: (builder) => {  
        builder.addCase(fetchUsers.pending, (state) => {  
            state.loading = true;  
            state.error = null;  
        });  
        builder.addCase(fetchUsers.fulfilled, (state, action) => {  
            state.loading = false;  
            state.users = action.payload;  
        });  
        builder.addCase(fetchUsers.rejected, (state, action) => {  
            state.loading = false;  
            state.error = action.payload.message;  
        });  
    },  
});  
  
export default userListSlice.reducer;  
export const { addUser, deleteUser } = userListSlice.actions;
```

#### Использование в компоненте React
```jsx
import { useDispatch, useSelector } from "react-redux";  
import { fetchUsers } from "./userListSlice";  
import { useEffect } from "react";  
  
function UserList() {  
    const dispatch = useDispatch();  
    const { users, loading, error } = useSelector(  
        (state) => state.userList.users  
    );  
  
    useEffect(() => {  
        dispatch(fetchUsers());  // запрос к API
    }, []);
      
    if (loading) return <p>Loading</p>;  
    if (error) return <p>Error</p>;  
  
    return (  
        <ul>  
            {users.map((user) => (  
                <li key={user.id}>{user.name}</li>  
            ))}  
        </ul>  
    );  
}  
  
export default UserList;
```

#### Маршрутизация(Роутинг)
###### Общепринятные маршруты и методы CRUD

|Метод|     Маршрут |              Шаблон   |           Описание|
|---|---|---|---|
|GET |      /schools      |       schools/index.html   | Список школ|
|GET  |     /schools/{id}  |      schools/show.html    | Информация о школе
|GET  |     /schools/new  |       schools/new.html    |  Форма создания новой школы
|POST |     /schools       |                           | Создание новой школы
|GET  |     /schools/{id}/edit |  schools/edit.html   |  Форма редактирования школы
|PATCH/PUT| /schools/{id}  |                          |  Обновление школы
|DELETE |  /schools/{id}   |  

[Встроенный роутер React. Документация](https://reactrouter.com/home)
>[!info] Нужен для того чтобы можно было переходить между страницами

>[!tip] `react-router` - это core библиотека. Может быть использована в `React Native`. Не предназначена для конкретной платформы. `react-router-dom` основана на `react-router` и предоставляет специфические компоненты и функциональность для веб-приложений (браузеров). Если вы разрабатываете веб-приложение, вам, скорее всего, нужна именно **`react-router-dom`**

##### React-Router
Установка
```bash
npm i react-router
```

Добавляем в `main.jsx`
```jsx
// СТАРЫЙ СИНТАКСИС
import { BrowserRouter, Routes, Route } from "react-router";

createRoot(document.getElementById("root")).render(
    <StrictMode>
        <BrowserRouter>
            <Routes>
                <Route path="/" element={<App />} />
                <Route path="/new" element={<MyApp />} />
            </Routes>
        </BrowserRouter>
    </StrictMode>
);
```

Пример вложенных путей
```jsx
<Routes>
  <Route path="dashboard" element={<Dashboard />}>
    <Route index element={<Home />} />
    <Route path="settings" element={<Settings />} />
  </Route>
</Routes>
```

##### React-Router-DOM
https://www.npmjs.com/package/react-router-dom

Установка
```bash
npm install react-router-dom
```

Описание путей в  `App.jsx`
```jsx
import { createBrowserRouter, RouterProvider } from "react-router-dom";
import Home from "./Home.jsx";
import About from "./About.jsx";
import NotFound from "./NotFound.jsx";
import Header from "../components/Header";
import Footer from "../components/Footer";

// createBrowserRouter принимает массив объектов, каждый
//   объект - это страница
const router = createBrowserRouter([
  {
    path: "/",
    element: <Home />,
  },
  {
    path: "about",  // Здесь не надо использовать абсолютный путь
    element: <About />,
  },
  {
    path: "*",
    element: (<>
			    <Header />
			    <NotFound />
			    <Footer />
			  </>),               // так можно возвращать несколько компонентов сразу
  },
]);

// теперь App возвращает компонент RouterProvider
export default function App() {
  return <RouterProvider router={router} />;
}
```

###### Link
Ссылка на новую страницу БЕЗ перезагрузки
```jsx
import { Link } from "react-router-dom";

function Home() {
  return (
    <div>
      <h1>Hope Page</h1>
      <p>
        <Link to={"/about"}>About</Link>  // В ссылках обязательно указываем АБСОЛЮТНЫЙ путь
      </p>
    </div>
  );
}

export default Home;
```

Ещё с помощью такой ссылки можно передать объект состояния. Это просто ообъект. Принимается он с помощью хука `useLocation`
```jsx
<Link to="/category/electronics" state={{ from: "Home Page", maxPrice: 600 }}>Cheapest electronics</Link>
```

Состояние принимается на целевой странице с помощью хука `useLocation`
```js
const location = useLocation();
console.log(location);  // {pathname: '/product/6',
						//             search: '',
						//             hash: '', 
						//             state: {…},
						//             key: 'tuetbpny'}
```

###### NavLink
Также есть компонент `<NavLink>`, который работает по аналогии с `Link`, только при нажатии на ссылку **добавляет ей класс** `active`

###### Outlet
Компонент для рендеринга дочерних маршрутов. Работает как `{children}`, только для страниц. Опционально выбираем для удобства.

Создадим `Layout.jsx`
```jsx
import Header from "./Header";
import Footer from "./Footer";
import { Outlet } from "react-router-dom";

function Layout() {
  return (
    <>
      <Header />
      <main>
	      <Outlet />
	  </main>
      <Footer />
    </>
  );
}

export default Layout;
```
Создадим роутер в `App.jsx`
```jsx
import { createBrowserRouter, RouterProvider } from "react-router-dom";
import Home from "./Home.jsx";
import About from "./About.jsx";
import NotFound from "./NotFound.jsx";
import Layout from "./Layout.jsx";

const router = createBrowserRouter([
  {
    path: "/",
    element: <Layout />,
    children: [
      { path: "", element: <Home /> },
  //  { index: true, element: <Home /> },   тоже что и path: ""
      { path: "about", element: <About /> },
      { path: "*", element: <NotFound /> },
    ],
  },
]);

export default function App() {
  return <RouterProvider router={router} />;
}
```

###### Динамическая маршрутизация (`useParams`)
В роутере указываем `wildcard`
```jsx
const router = createBrowserRouter([
  {
    path: "/",
    element: <Layout />,
    children: [
      { path: "", element: <Home /> },
      { path: "about", element: <About /> },
      { path: "category/:categoryId", element: <Category /> },  // wildcard
      { path: "*", element: <NotFound /> },
    ],
  },
]);
```

В компоненте принимаем с помощью хука `useParams`
```jsx
import { useParams } from "react-router-dom";

function Category() {
  const currentCategory = useParams();  // {categoryID: "clothing"}
  const { categoryId } = useParams();  // clothing

  return (
    <>
      <h1>{ currentCategory }</h1>
    </>
  );
}

export default Category;
```

###### GET параметры (`useSearchParams`)
>[!info] Хук `useSearchParams` похож на `useState`. Он тоже вызывает ререндеринг, имеет переменную для объекта, содержащего все пары ключ-значение. Имеет функцию для установки объекта.

```jsx
import { useSearchParams } from "react-router-dom";

function About() {
  const [searchParams, setSearchParams] = useSearchParams();
  const name = searchParams.get("name") || "";      // получение значения
  const hasName = searchParams.has("name");         // true или false
  
  function handleClick() {
	  setSearchParams({maxPrice: 150});             // установка GET параметра
  }

  console.log(name);
  console.log(hasName);


  searchParams.keys()           // итератор по всем ключам параметров
  searchParams.values()         // итератор по всем значениям параметров
  searchParams.entries()        // итератор по всем парам [ключ, значение]

  // перебор всех пар ключ-значение
  for (const [key, value] of searchParams.entries()) {
    console.log(key, value);
  }

  return <div>About Page</div>;
}

export default About;
```

>[!tip] Удобно делать фильтры на сайте через Get параметры.

###### Хук useLocation
Хук, возвращающий объект текущего URL. Этот объект содержит информацию о текущем пути,
строке запроса, хэше и состоянии, переданном при навигации.

|Свойство |Описание |
|---|---|
|`pathname` |Путь текущего URL (например, /products/123).|
|`search` |Get параметры.(useSearchParams — предпочтительный способ работы с GET параметрами)
|`hash`|Хэш (фрагмент URL после символа `#`, например, `#section1`).|
|`state` |Объект состояния, переданный при навигации (например, через `navigate` или `Link`).|
|`key` |Уникальный идентификатор маршрута (используется React Router для навигации).|

```jsx
const location = useLocation();
```

###### Хук useNavigate
>[!info] Тоже что и тэг `<Link>`, только программное перенаправление.

Программный переход на другую страницу
```jsx
import { useEffect } from "react";
import { useNavigate } from "react-router-dom";

...
const navigate = useNavigate();

useEffect(() => {
    navigate("/new");  // При загрузке страницы автоматически перейдёт на "/new"
}, [])
...
```

Программное перенаправление с таймером
```jsx
import { useNavigate } from "react-router-dom";
import { useEffect } from "react";

function About() {
  const navigate = useNavigate();

  useEffect(() => {
    const timer = setTimeout(() => {
      navigate("/");
    }, 5000);
    
    return () => clearTimeout(timer);
  }, [navigate]);

  return (
    <div>About Page. You will be redirected to the home page in 5 seconds</div>
  );
}

export default About;
```

###### Navigate
Компонент для программного перенаправления. Используется для редиректов.

```jsx
const router = createBrowserRouter([
  {
    path: "/",
    element: <Layout />,
    children: [
      { path: "", element: <Home /> },
      { path: "about", element: <About /> },
      { path: "*", element: <Navigate to="/" /> },
    ],
  },
]);
```

#### Загрузка данных
Создаём функцию загрузки
```jsx
async function fetchData() {
  const res = await fetch("https://jsonplaceholder.typicode.com/posts");
  if (!res.ok) {
    throw new Error("Failed to fetch posts");
  }

  return res.json();
}

export default fetchData;
```

Создаём элемент, который будет отображаться при ошибке загрузки
```jsx
import { useRouteError } from "react-router-dom";

export function ErrorBoundary() {
  const error = useRouteError();  // хук для получения ошибки

  return (
    <div>
      <h1>Error</h1>
      <p>{error.message}</p>
    </div>
  );
}
```

В родительском элементе (например Layout) получаем *состояние* с помощью хука `useNavigation` (не путать с `useNavigate`) и отображаем `<p>Loading...</p>` пока состояние `loading`. Это надо делать именно в родительском элементе, поскольку загрузка начнётся ещё до рендеринга страницы `Posts`.
```jsx
import Header from "./Header";
import Footer from "./Footer";
import { Outlet, useNavigation } from "react-router-dom";

function Layout() {
  const navigation = useNavigation();  // Возвращает объект, который указывает на текущее
                                       //   состояние (state) и может принимать значения:
                                       //   idle, loading или submitting
  return (
    <>
      <Header />
      // idle, loading, submitting
      {navigation.state === "loading" && <p>Loading...</p>}
      <Outlet />
      <Footer />
    </>
  );
}

export default Layout;
```

В роутере указываем `loader` и `errorElement`
```jsx
...
const router = createBrowserRouter([
  {
    path: "/",
    element: <Layout />,
    children: [
      { path: "", element: <Home /> },
      { path: "posts",
        element: <Posts />,
        loader: fetchData,                  // наша функция загрузки срабатывает до
                                            //    рендера компонента <Posts />
        errorElement: <ErrorBoundary />     // элемент для отображения ошибки 
      },  
      { path: "*", element: <NotFound /> },
    ],
  },
]);]);
...
```

Отображаем на странице
```jsx
import { useLoaderData } from "react-router-dom";

function Posts() {
  const posts = useLoaderData();   // специальный хук react-router-dom для получения

  return (
    <div>
      <h1>Posts</h1>

      <ul>
        {posts.map((post) => (
          <li key={post.id}>{post.title}</li>
        ))}
      </ul>
    </div>
  );
}

export default Posts;
```

#### Отправка данных
В роутере определяем путь для `action`
```jsx
const router = createBrowserRouter([
  {
    path: "/",
    element: <Layout />,
    children: [
      { path: "", element: <Home /> },
      { path: "/about", element: <About /> },
      { path: "/create",
       element: <CreatePost />,
       action: createPost,                // указываем функцию для action
       errorElement: <ErrorBoundary /> 
	  },
      { path: "*", element: <NotFound /> },
    ],
  },
]);
```

Создаём страницу с формой
```jsx
import { useActionData, useNavigation } from "react-router-dom";
import { Form } from "react-router-dom";  // используем именно этот тэг

function CreatePost() {
// Данные, возвращаемые из action, доступны в компоненте с помощью хука useActionData.
  const actionData = useActionData();
  const navigation = useNavigation();  // отслеживаем состояние

  return (
    <div>
      <h1>Create Post</h1>
      <Form method="post" action="/create">
        <input type="text" name="title" required />
        <button type="submit" disabled={navigation.state === "submitting"}>
          {navigation.state === "submitting" ? "Sending..." : "Create Post"}
        </button>
      </Form>

      {actionData && (
        <div>
          Post created! ID: {actionData.id}, Title: {actionData.title}
        </div>
      )}
    </div>
  );
}

export default CreatePost;
```

Создаём функцию `createPost`
```jsx
// Функция action получает объект, содержащий request и params
const createPost = async ({ request }) => {
  const formData = await request.formData();  // извлекаем данные тела запроса
  const title = formData.get("title");
  
  const response = await fetch("https://jsonplaceholder.typicode.com/posts", {
    method: "POST",
    headers: {
      "Content-Type": "application/json",
    },
    body: JSON.stringify({ title }),
  });

  if (!response.ok) {
    throw new Error("Failed to create a post");
  }

  const result = await response.json();
  
  // результат будет доступен через useActionData
  return result;
};
```

#### Обработка ошибок
>[!info] Для обработки ошибок создаём состояние для ошибки, если ответ не `ok` - кидаем ошибку, которую ловит блок `catch` и записывает ошибку в состояние. И уже исходя из этого состояния отображаем или нет данные в JSX.

```js
const [error, setError] = useState(null);

async function getData() {
	try {
		const res = await fetch(
			`http://api.weatherapi.com/v1/current.json?key=${KEY}&q=${city}`
		);

		if (!res.ok) {
			throw new Error("Невозможно получить данные.");
		}

		const data = await res.json();
		return data;
	} catch (err) {
		setError("Невозможно получить данные " + err.message);
	}
}
```

## Управление состоянием
`useState` и проброс пропсов - для маленьких приложений
`useContext` - для средних приложений
`Redux` - для больших приложений

## Деплой
Все изображения помещаем во внутренюю директорию в `src` (например `images`) . А также импортируем их. Таким образом сборщик `Vite` создат хэши изображений (данные в директории `public` сборщик игнорирует). 
Зачем нужен хэш?  Если обновить изображение, но оставить имя файла прежним, браузер может продолжать использовать старую версию из кэша.

```js
import laptopImg from "../assets/images/laptop.img"

export const categories = [
  {id: "Electronics", name: "Electronics", img: laptopImg }
]
```

[Плагин для `Vite` для минифицирования изображений](https://github.com/vbenjs/vite-plugin-imagemin)

Билд дистрибутива
```bash
npm run build
```



## React Bootstrap
[Документация](https://react-bootstrap.github.io/docs/getting-started/introduction/)

```bash
npm install react-bootstrap bootstrap
```

В `App.jsx` импортируем
```jsx
import "bootstrap/dist/css/bootstrap.min.css";
```

В нужном компоненте импортируем отдельные компоненты бутстрапа
```jsx
import Button from 'react-bootstrap/Button';
```

И далее используем
```jsx
function onClickHandler() {
    alert("Button is clicked!);
}

<Button variant="primary" size="lg" onClick={onClickHandler}>
    Click me!
</Button>
```


