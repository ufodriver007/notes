![[Pasted image 20240115022815.jpg|300]]

>[!info] Код react-приложения с помощью `create-react-app` и `node js` конвертируется в обычный JavaScript, который уже потом скармливается браузеру.

###### Устанавливаем node js
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

###### Устанавливаем create-react-app
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

###### NPM и NPX
- **npm** пакетный менеджер, который скачивает исходники и добавляет в проект
- **npx** пакетный менеджер, который скачивает, выполняет и затем удаляет исходники

>[!info] package.json - описание проекта(имя, описание, зависимости, скрипты и т.д.)

>[!tip] Дописываем в `package.json` в основную секцию стоку `"homepage": "./",`

Примеры скриптов
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
            return 'good'    # млм например return <div>test</div>
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
    const onj = array[i];

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
       ), [])}
   </div> 
)
...
```

#### Props
>[!info] Props представляет коллекцию значений, которые ассоциированы с компонентом. Эти значения позволяют создавать динамические компоненты, которые не зависят от жестко закодированных статических данных.

```
function Hello(props) {
    return <div>
        <p>Имя: {props.name}</p>
        <p>Возраст: {props.age}</p>
    </div>
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
    const [isDark, setIsDark] = useState(true);  // функция хука возвращ. массив
        // первое значение - это начальная переменная
        // второе значение - функция, которая меняет переменную 1го элемента

    const onClickHandler = () => {
        setIsDark(!isDark);
    };

    return (
        <div className={isDark & 'dark' : 'light'}>
            <button onClick={onClickHandler}>Переключить тему</button>
        </div>
    );
};

export default App;
```

**useEffect** 