![[Pasted image 20240115022815.jpg|300]]

[Документация](https://react.dev/learn)
[Метанит](https://metanit.com/web/react/)

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


###### Импорт CSS
```js
import './index.css'
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

>[!info] В каждом компоненте должен быть только один элемент (тэг/фрагмент)! Если нужно несколько - делаете вложенную структуру.

>[!info] Имя компонента в React (который является обычной функцией в JS) пушется с большой буквы, чтобы React понимал, что это преобразуется в пользовательский тэг, а тэгт с маленькой буквы - это стандартные HTML тэги.

#### JSX
>[!info] JSX — расширение языка JavaScript. Позволяет использовать синтаксис HTML внутри вашего кода JavaScript.

>[!warning] В JSX мы не можем тегам писать `class`, вместо этого пишем `className`. Например `<div className="test"></div>`. 
>- Атрибуты также пишем в camelCase ! `<div style={{ fontSize: "40px" }}>Hello, world!</div>`
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
>[!info] Props представляет коллекцию значений, которые ассоциированы с компонентом. Эти значения позволяют создавать динамические компоненты, которые не зависят от жестко закодированных статических данных. Можно представить их как аргументы для функции, только read-only и передаваемые одним объектом

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

##### Children
Можно передать любой контент(в т.ч. и другие компоненты) внутрь своего компонента

Слово `children` зарезервировано. Определяем место под `prop`
```jsx
export function DangerButton({ isDisabled = false, children }) {
    return (
        <button
            className={`${isDisabled ? "disabled" : ""} red`}
            onClick={() => alert("Test!")}
        >
            {children}
        </button>
    );
}
```

Передаём контент внутрь тэга
```jsx
import { DangerButton } from "./components/Buttons.jsx";

export default function App() {
    return (
        <DangerButton><span>&#11015;</span>Hello world!</DangerButton>
    );
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
// count - это будет переменная, которая будет хранить наше значение
// setCount - функция, которая изменит нашу переменную на то, что мы ей передадим.
//     Например setCount(count + 1)
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

###### useEffect
Служит для определённых side эффектов (любое действие, которое выходит за пределы текущей функции и взаимодействует с чем-то снаружи). Мы можем за чем-либо(переменная, координаты мыши и т.п.) наблюдать и выполнять в случае чего нашу логику.

Вот несколько примеров использования:
- Запрос данных из API
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

В `useEffect` если он срабатывает неоднократно(цилично) нужно описывать функцию очистки, чтобы программа не жрала память.
```jsx
useEffect(() => {
    // основная логика
    console.log('Component mounted')

    // функция очистки
    return () => {
        // логика очистки
    }
}, [])
```
![[2025-05-20_04-49.png]]

Чтобы избежать состояния гонки, передаём в `fetch` сигнал для отмены запроса.
1. Создание `AbortController`: Внутри `useEffect` создаётся новый экземпляр `AbortController`, который предоставляет сигнал (`signal`) для управления отменой запроса.
2. Передача `signal` в `fetch`: В параметрах `fetch` передаётся `signal`, что позволяет отменить запрос, если `AbortController` вызовет `abort()`.
3. Отмена предыдущего запроса: В `useEffect` возвращается функция очистки, которая вызывает `controller.abort().` Это отменяет любой предыдущий запрос, который всё ещё может быть в процессе выполнения, если query изменяется до завершения запроса.
4. Обработка ошибки отмены: Если запрос был отменён, возникает ошибка типа `AbortError`. Мы проверяем её в блоке `catch`, чтобы не отображать сообщение об ошибке пользователю, если запрос был отменён преднамеренно.
```js
function MyComponent() {
  useEffect(() => {
    const controller = new AbortController();  // Создаём AbortController
    const signal = controller.signal;  // Получаем сигнал из контроллера

    async function fetchData() {
      try {
        const response = await fetch('https://api.example.com/data', {signal});
        const data = await response.json();
        console.log(data);
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
Позволяет сохранять значения между рендерами. Хук  похож на `useState`, но не запускает *ререндеринг*.

Увеличение счётчика без дополнительного рендера
```jsx
const renderCounter = useRef(1)  // возвращает объект с
                                 //   единственным атрибутом current
renderCounter.current++
```

Также с его помощью можно получить элемент добавив атрибут `ref`
```jsx
import React, {useEffect, useRef} from "react"

functiom App() {
  const inputRef = useRef(null);

  useEffect(() => {
    console.log(inputRef.current);  // теперь можно манипулировать им
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

    fetchData();
  }, [url]);

  return { data, loading, error };
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

const router = createBrowserRouter([
  {
    path: "/",
    element: <Home />,
  },
  {
    path: "about",
    element: <About />,
  },
  {
    path: "*",
    element: <NotFound />,
  },
]);

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
        <Link to={"about"}>About</Link>
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

###### NavLink
Также есть компонент `<NavLink>`, который работает по аналогии с `Link`, только при нажатии на ссылку добавляет ей класс `active`

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

###### Outlet
Компонент для рендеринга дочерних маршрутов. Работает как `{children}`, только для страниц.

Создадим `Layout.jsx`
```jsx
import Header from "./Header";
import Footer from "./Footer";
import { Outlet } from "react-router-dom";

function Layout() {
  return (
    <>
      <Header />
      <Outlet />
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
      { path: "about", element: <About /> },
      { path: "*", element: <NotFound /> },
    ],
  },
]);

export default function App() {
  return <RouterProvider router={router} />;
}
```

###### Динамическая маршрутизация
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

###### GET параметры
>[!info] Хук `useSearchParams` похож на `useState`. Он тоже вызывает ререндеринг, имеет переменную для объекта, содержащего все пары ключ-значение. Имеет функцию для установки объекта.

```jsx
import { useSearchParams } from "react-router-dom";

function About() {
  const [searchParams, setSearchParams] = useSearchParams();
  const name = searchParams.get("name") || "";  // получение значения
  const hasName = searchParams.has("name");  // true или false

  console.log(name);
  console.log(hasName);

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
|`state` |Объект состояния, переданный при навигации (например, через navigate или Link).|
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

В родительском элементе (например Layout) получаем состояние с помощью хука `useNavigation` (не путать с `useNavigate`) и отображаем `<p>Loading...</p>` пока состояние `loading`. Это надо делать именно в родительском элементе, поскольку загрузка начнётся ещё до рендеринга страницы `Posts`.
```jsx
import Header from "./Header";
import Footer from "./Footer";
import { Outlet, useNavigation } from "react-router-dom";

function Layout() {
  const navigation = useNavigation();

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
        loader: fetchData,                  // наша функция загрузки
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
  const posts = useLoaderData();

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
  const formData = await request.formData();
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

