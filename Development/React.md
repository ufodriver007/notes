![[Pasted image 20240115022815.jpg|300]]

[Документация](https://react.dev/learn)

###### Искусственный пример. Hello, world
Создадим файл `index.html` в котором прямо внутри подключим из внешнего источника библиотеку React и напишем JS код(не JSX). *Так никто не делает. Просто для понимания структуры.*
```html
<!DOCTYPE html>
<html>
  <head>
    <title>My First React Application</title>
    <script src="https://unpkg.com/react@18.3.1/umd/react.development.js"></script>
    <script src="https://unpkg.com/react-dom@18.3.1/umd/react-dom.development.js"></script>
  </head>
  <body>
    <div id="root"></div>
    <script type="text/javascript">
      const reactElement = React.createElement(
        'h1',
        null,
        'Hello world!!!'
      );

      ReactDOM
        .createRoot(document.getElementById('root'))
        .render(React.createElement('h1', null, 'Hello world!'));
    </script>
  </body>
</html>
```

###### Начало работы
>[!info] Чтобы создать React-приложение и начать с ним работать, нужно:
>- Node.js
>- NPM
>- Инструмент для создания проектов и работы с ними(`Vite` или устаревший `Create-react-app`)

###### Устанавливаем node js
>[!info] Node.js — это среда выполнения JavaScript-программ, построенная на движке Chrome V8. Раньше на JS можно было делать только интерактивные сайты, так как это специализированный браузерный язык программирования. С программной платформой Node.js появилась возможность создавать «безбраузерные» приложения для компьютера. Это значит, что при необходимости на JavaScript теперь можно делать то же, что на Python и других скриптовых языках программирования общего назначения.

Проверка, установлен ли Node
```bash
node -v
```
---
Установка
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
- **npm** пакетный менеджер, который скачивает исходники и добавляет в проект
- **npx** пакетный менеджер, который скачивает, выполняет и затем удаляет исходники

>[!info] `package.json` - описание проекта(имя, описание, зависимости, скрипты и т.д.)

>[!tip] Дописываем в `package.json` в основную секцию стоку `"homepage": "./",`

>[!info] `public` - папка для статики

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

Далее последовательно вводим
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
В файле источнике пишем
```js
export const TEST = 42;
export my_func() {
    alert("Hello!")
}
```
В файле, куда хотим подключить
```js
import { TEST, my_func } from './src.js'
```

###### Default import
**Обычный способ подключения React компонентов**
В файле источнике пишем
```jsx
const App = () => {
    return "Hello, world!";
}
export default App
```
В файле, куда хотим подключить(Если рефкт-компонент, то в `'src/index.js'` )
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


###### Импорт CSS
```js
import './index.css'
```

###### Создаём простой компонент

В `'src`' создаём файл `'header.jsx'`
```jsx
function Header() {
    return (
        <header>
            <img src="logo.png" />

            <nav>
                <a href="#">На главную</a>
                <a href="#">Портфолио</a>
                <a href="#">Контакты</a>
            </nav>
        </header>
    );
}

export default Header
```
Затем поключаем его в файле `src/index.js`
```jsx
import React from 'react';
import ReactDOM from 'react-dom/client';
import Header from './header'

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(
    <React.StrictMode>
    <Header></Header>
    </React.StrictMode>
);
```

#### JSX
>[!info] JSX — расширение языка JavaScript. Позволяет использовать синтаксис HTML внутри вашего кода JavaScript.

>[!warning] В JSX мы не можем тегам писать `class`, вместо этого пишем `className`. Например `<div className="test"></div>`. 
>- Атрибуты также пишем в CamelCase !
>- JS код пишем в фигурных скобках. Например `<p>{obj.hello}</p>`
>- Комментарий `{/* */}
`
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
            <p>{age === 18 & 'good' : 'bad'}</p>
        </div>
        );
};

export default App;
```

2. Через функцию
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
const array = [{hello: 'world}, {hello: 'world2}]   # объекты JS
const array ReactElements = []

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
>[!info] Желательно не делать компоненты гигантскими. Ориентир - 100 строк кода.

- Создаём файл `MyComponent.jsx` с функцией и нужными импортами. В конце файла добавляем
```jsx
export default MyComponent;
```
Например
```jsx
import { useState } from 'react'

function MyComponent() {
    return (
        <>                       // Это React фрагмент. Пустой тег, который никак не обрабатывается
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

#### Props
>[!info] Props представляет коллекцию значений, которые ассоциированы с компонентом. Эти значения позволяют создавать динамические компоненты, которые не зависят от жестко закодированных статических данных.

###### Передаём значение
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
```jsx
function Hello(props) {
const { name, age } = props;
    return <div>
        <p>Имя: {name}</p>
        <p>Возраст: {age}</p>
    </div>
}
```

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
        </form>
    );
};

export default App;
```

Есть ещё `onMouseEnter` - событие, когда навели мышью

#### Хуки
>[!info] Вспомогательные функции, которые помогают работать с компонентом
>Функция, которая принимает некое значение.

**Основные хуки:**
- useState
- useEffect
- useCallback
- useRef
- useMemo
- useContext

**useState** Позволяет сохранять значения. Если значение изменено, перерисовывает компонент.
```jsx
//App.jsx
import React, { useState } from 'react';
import './index.css';

const App = () => {
    const [isDark, setIsDark] = useState(true);  // функция хука возвращ. массив из 2х значений
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

Лучшая практика
```jsx
const onClickHandler = () => {
        setIsDark(prev => prev +1);  // Предыдущее состояние изменяется. Так надёжнее, из-за
                                     // асинхронной природы хука
    };
```

>[!tip] Чтобы не плодить в одном компоненте кучу дублирующегося кода с useState, можно передавать один объект с кучей значений

###### useEffect
Служит для определённых side эффектов. Мы можем за чем-либо(переменная, координаты мыши и т.п.) наблюдать и выполнять в случае чего нашу логику

```jsx
import { useEffect } from "react";
```

Базовое использование
```jsx
useEffect(() => {
    console.log('render')  // выполнится при каждом рендере и если здесь будет код,
                // после которого нужно будет перередерить, то хук выполнит рендер
})
```

С пустым массивом зависимостей
```jsx
useEffect(() => {
    console.log('Component mounted')  // выполнится один раз
}, [])
```

Отслеживание переменной из массива зависимостей
```jsx
let myVar = 123;

useEffect(() => {
    console.log(myVar)   // выполнится при каждом изменении переменой myVar
}, [myVar])
```

###### useRef
Позволяет сохранять значения. Ели мы не хотим лишний раз рендерить

Увеличение счётчика без дополнительного рендера
```jsx
const renderCounter = useRef(1)  // возвращает объект

renderCounter.current++
```

Фокус на элементе
```jsx
const inputRef = useRef(null)

function focus() {
    inputRef.current.focus()
}
...
<button onClick={focus}>Фокус</button>
...
```

Также с его помощью можно получить предыдущее состояние

###### useMemo
Используется для оптимизации производительности компонента, позволяя мемоизировать вычисленные значения. Это полезно, если вычисление значений требует значительных ресурсов или времени

`useMemo` запоминает результат вычислений до тех пор, пока не изменятся зависимости, указанные в массиве зависимостей. Если зависимости не изменились, то возвращается мемоизированное значение из предыдущего рендера.

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
**`useContext`** — это хук, который позволяет получать доступ к значениям из контекста без необходимости оборачивать компоненты в `Consumer`. В отличии от глобальных переменных хук автоматически ренднрит при изменении состояния(реактивность).

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

// Создаем контекст
const ThemeContext = createContext();

function App() {
  const [theme, setTheme] = useState("light");

  return (
    // Предоставляем значение контекста
    <ThemeContext.Provider value={{ theme, setTheme }}>
      <Toolbar />
    </ThemeContext.Provider>
  );
}

function Toolbar() {
  return (
    <div>
      <h1>Toolbar</h1>
      <ThemeSwitcher />
    </div>
  );
}

function ThemeSwitcher() {
  // Получаем значение контекста
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

>[!warning] `useContext` не подходит если значение контекста обновляется очень часто, это может привести к повторным рендерам всех компонентов, использующих этот контекст.

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

Установка
```bash
npm i react-router
```

Добавляем в `main.jsx`
```jsx
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

Ссылка на новую страницу БЕЗ перезагрузки
```jsx
<Link to='/new'>Go to new page</Link>
```

Программный переход на другую страницу
```jsx
import { useEffect } from "react";
import { useNavigate } from "react-router";

...
const navigate = useNavigate();
useEffect(() => {
    navigate("/new");  // При загрузи страницы автоматически перейдёт на "/new"
}, [])
...
```

#### Модульные стили
>[!info] Класс стиля будет привязан к определённому компоненту, потому что сгенерируется уникальный ключ. И можно будет лаконично назвать свой класс стиля и он не будет конфликтовать с др. классами.

Создаём файл стилей `MyApp.module.css`
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

#### React Bootstrap
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

