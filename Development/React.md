![[Pasted image 20240115022815.jpg|300]]

#### Подключение react.js
```
<head>
    <script crossorigin src="https://unpkg.com/react@18/umd/react.development.js"></script>
    <script crossorigin src="https://unpkg.com/react-dom@18/umd/react-dom.development.js"></script>
    <script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>  # преобразование
                                                                              #  кода react в JS.
</head>
```

Также создадим `<div id="app"></div>` куда будем выводить нужные элементы.
>[!info] Babel ТРЕБУЕТ, чтобы все скрипты react были с особенным типом (`type="text/babel"`)

В конец `<body>` подключаем свой скрипт, например `<script src="js/main.js" type="text/babel"></script>`
В этом своём скрипте например рендерим заголовок:
```
ReactDOM.render(<h1>Hello from React</h1>, document.getElementById("app"))      # Создаём и рендерим заголовок и помещаем его в элемент с id="app"
```

Весь код:
```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>React JS</title>
  <script crossorigin src="https://unpkg.com/react@18/umd/react.development.js"></script>
  <script crossorigin src="https://unpkg.com/react-dom@18/umd/react-dom.development.js"></script>
  <script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>
</head>
<body>

  <div id="app">
    
  </div>

  <script src="js/main.js" type="text/babel"></script>
</body>
</html>
```

С помощью можно вывести только один элемент. И если нужно вывести много информации, можно выводить `<div>` с дочерними вложенными элементами

#### События
```
const inputClick = () => console.log("Clicked!")
const helpText = "Help TEXT"

const elements = (<div className="abc">      # встроенный JSX парсер, используем className
                                             #  вместо class
    <h1>Example</h1>
    <input placeholder="help text" onClick={inputClick}>      # вызов функции
    <p>{helpText == "Help TEXT" ? "Yes" : "No"}</p>           # условие
</div>)

const app = document.getElementNyId("app")
ReactDOM.render(elements, app)
```