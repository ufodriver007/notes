![[Pasted image 20240115022944.png|300]]

```html
<!DOCTYPE html>
```
```
<!-- comment -->
```

#### Подключение css стилей
###### Внешние
Подключение внешнего файла со стилями - внутрь head добавляем `<link rel="stylesheet" href="static/style.css">`
###### Встроенные
`<style></style>` встраивание css стилей

#### Теги
|Тэг|Описание|
|-----|----------|
|`<div class="main border-div">`|тэг с двумя классами
|`<head>`|Шапка
|`<title>`|Заголовок страницы. Располагается в шапке
|`<h1></h1>`|Заголовок первого уровня. Может быть только один в документе
|`<a href=""></a>`|Ссылка
|`<a href="" target="_blank"></a>`|Ссылка в новом окне
|`<p>`|Абзац
|`<table>`|таблица
|`<ol><li></li></ol>`|нумерованный список
|`<ul><li></li></ul>`|ненумерованный список
|`<img src="" alt="">`|картинка
|`<span>`|строчный контейнер
|`<div>`|блочный контейнер
|`<input type="text">`|поле для ввода текста
|`<button>`|кнопка
|`<button>&#9778;</button>`|кнопка-бургер(используется спец. html код)
|`<header>`|шапка
|`<footer>`|подвал

Пример формы
```html
<form action="/send-feddback/" method="POST">
    <p>Отправьте сообщение</p>
    <input type="text" name="name" placeholder="Ваше имя" />
    <textarea name="message" placeholder="Ваше сообщение"></textarea>
    <input type="submit" value="Отправить" />
</form>
```

Пример таблицы
```html
<table>  
  <tr>  
    <th>Month</th>  
    <th>Savings</th>  
  </tr>  
  <tr>  
    <td>January</td>  
    <td>$100</td>  
  </tr>  
</table>
```