#### Внешний вид кода
- 4 пробела на каждый уровень отступа
- Продолжительные строки должны выравнивать обернутые элементы либо вертикально, используя неявную линию в скобках (круглых, квадратных или фигурных), либо с использованием висячего отступа
```
# Выровнено по открывающему разделителю
foo = long_function_name(var_one, var_two,
                         var_three, var_four)
```
- Ограничьте длину строки максимум 79 символами
- Отделяйте функции верхнего уровня и определения классов двумя пустыми строками.
- Определения методов внутри класса разделяются одной пустой строкой.
- Используйте пустые строки в функциях, чтобы указать логические разделы.
- Кодировка Python должна быть UTF-8
- Каждый импорт, как правило, должен быть на отдельной строке
Импорты всегда помещаются в начале файла, сразу после комментариев к модулю и строк документации, и перед объявлением констант.
Импорты должны быть сгруппированы в следующем порядке:
1.  импорты из стандартной библиотеки
2.  импорты сторонних библиотек 
3.  импорты модулей текущего проекта

>[!info] `from import *` следует избегать

- Всегда окружайте бинарные операторы(присваивание, сравнение, логические) одним пробелом с каждой стороны
- Не используйте пробелы вокруг знака =, если он используется для обозначения именованного аргумента или значения параметров по умолчанию
- Иногда можно писать тело циклов while, for или ветку if в той же строке, если команда короткая, но если
 команд несколько, никогда так не пишите. А также избегайте длинных строк!
- Старайтесь реже использовать встроенные комментарии(на строке с кодом)

#### Именование
- Модули должны иметь короткие имена, состоящие из маленьких букв.
- Имена классов должны обычно следовать соглашению CapWords.
- Имена функций должны состоять из маленьких букв, а слова разделяться символами подчеркивания
- Всегда используйте `self` в качестве первого аргумента метода экземпляра объекта.
- Всегда используйте `cls` в качестве первого аргумента метода класса.
- Константы обычно объявляются на уровне модуля и записываются только заглавными буквами, а слова разделяются символами подчеркивания

#### Общее
- Всегда используйте выражение `def`, а не присваивание лямбда-выражения к имени
- Наследуйте свой класс исключения от Exception
- Пользуйтесь `.startswith()` и '`.endswith()` вместо обработки срезов строк для проверки суффиксов или префиксов.
- Сравнение типов объектов нужно делать с помощью `isinstance()`, а не прямым сравнением типов
- Не сравнивайте логические типы с `True` и `False` с помощью `==` Правильно: `if greeting:`