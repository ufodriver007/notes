![[Pasted image 20240115022815.jpg|300]]

[Документация](https://react.dev/learn)

###### Искусственный пример. Hello, world
Создадим файл `index.html` в котором прямо внутри подключим из внешнего источника библиотеку React и напишем JS код(не JSX). Так никто не делает. Просто для понимания структуры
```
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
```
curl -sL https://deb.nodesource.com/setup_20.x -o nodesource_setup.sh
```
```
sudo bash nodesource_setup.sh
```
```
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

```
npm run start             # запустить на тестовом сервере
npm run build             # собрать билд
```

После сборки билда можно открывать `index.html` в браузере.
>[!info]  Директория `node_modules` это аналог виртуального окружения в Python. Её добавляют в `.gitignore`

```
npm init                 # создать package.json
npm install              # установить зависимости(node_modules) из package.json
npm install react        # установить библиотеку
npm uninstall react      # удалить библиотеку
```

###### Создаём react-приложение с помощью create-react-app
>[!info] Код react-приложения с помощью `create-react-app` и `node js` конвертируется в обычный JavaScript, который уже потом скармливается браузеру.

```
npx create-react-app@5.0.1 my-app              
```
```
cd my-app
```
```
npm run start
```
Затем в браузере откройте [http://localhost:3000/](http://localhost:3000/)

###### Создаём react-приложение с помощью Vite
[Документация](https://vite.dev/guide/)
[Использование вместе с React](https://www.dev-notes.ru/articles/react/guide-to-using-vite-with-react/)
>[!info]  Это современный инструмент для сборки фронтенд-проектов, который делает разработку быстрее и удобнее. Предназначен для работы с современными JavaScript-фреймворками, такими как Vue, React, Svelte и др.

```
npm create vite@latest my-app -- --template react
```

Далее последовательно вводим
```
cd my-app
npm install            # установить зависимости(node_modules) из package.json
npm run dev
```

Для того чтобы сделать билд
```
npm run build
```

#### React Dev Tools
[Ссылка для Chrome](https://chromewebstore.google.com/detail/react-developer-tools/fmkadmapgofadopljbjfkapdkoienihi)
После установки расширения браузера на сайте с React в инструментах разработчика появятся 2 дополнительные вкладки

#### Import и export
В файле источнике пишем
```
export const TEST = 42;
export my_func() {
    alert("Hello!")
}
```
В файле, куда хотим подключить
```
import { TEST, my_func } from './src.js'
```

###### Default import
**Обычный способ подключения React компонентов**
В файле источнике пишем
```
const App = () => {
    return "Hello, world!";
}
export default App
```
В файле, куда хотим подключить(Если рефкт-компонент, то в `'src/index.js'` )
```
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
```
import './index.css'
```

###### Создаём простой компонент

В `'src`' создаём файл `'header.jsx'`
```
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
```
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

>[!warning] В JSX мы не можем тегам писать `class`, вместо этого пишем `className`.
>Например `<div className="test"></div>`. Атрибуты также пишем в CamelCase.

###### Передача переменных
```
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
```
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
```
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
```
const array = [{hello: 'world}, {hello: 'world2}]   # объекты JS
const array ReactElements = []

for (let i = 0; i < array.length; i++) {
    const obj = array[i];

    arrayReactElements.push(
        <div>obj.hello</div>                       
    )
}
```

Внутрь JSX надо использовать `map()` либо `reduce()`, т.к. они возвращают результат. Например
```
...
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
...
```

#### Создание нового компонента
- Создаём файл `MyComponent.jsx` с функцией и нужными импортами. В конце файла добавляем
```
export default MyComponent;
```
Например
```
import { useState } from 'react'

function MyComponent() {
    return (
        <>
            <p>MyComponent </p>
        </>
    );
}

export default MyComponent
```
- Добавляем импорт в `App.jsx`
- Используем новый компонент как тэг
```
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
```
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
```
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
```
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
```
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
- useContect

**useState** Позволяет сохранять значения. Если значение изменено, перерисовывает компонент.
```
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
                             // и перерисовывает его
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
```
const onClickHandler = () => {
        setIsDark(prev => prev +1);  // Предыдущее состояние изменяется. Так надёжнее, из-за
                                     // асинхронной природы хука
    };
```

**useEffect** 