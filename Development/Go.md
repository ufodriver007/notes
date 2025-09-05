![[Go_Logo_Blue.svg|300]]

>[!info] Go - компилируемый статически типизированный язык программирования от компании Google. Автоматиическая сборка мусора, параллелизм, поддержка многоядерности, кроссплатформенность.

[Сайт разработчиков](https://go.dev/)
[Документация](https://go.dev/doc/)
[GO Playground](https://go.dev/play/)

[Установка](https://go.dev/doc/install)

Проверка версии
```go
go version
```

## Hello, world
Создадим файл `hello.go`
```go
package main                      // пакет точка входа в приложение

import "fmt"

func main() {                     // функция тока входа в приложение
    fmt.Println("Hello, world!")
}
```

Запуск компилятора и выполнение программы (запуск файла)
```bash
go run hello.go
```

Запустить проект из текущей директории
```bash
go run .
```

Сделать билд
```bash
go build hello.go
```

## Пакеты
>[!info] Весь код в языке Go организуется в **пакеты**. **Пакет** представляют удобную организацию разделения кода на отдельные части или модули. Физически **пакет** — это просто каталог/папка, которая содержит несколько файлов с исходным кодом на Go. Импортируя пакет, мы можем использовать функциональность его файлов многократно в различных программах.

Виды пакетов:
- Точка входа в приложение (`main`)
- Стандартный пакет библиотеки Go (напр. `fmt`)
- Наши собственные пакеты
- Внешние пакеты

## Модули
>[!info] Модуль в языке Go представляет коллекцию пакетов, который имеет встроенное версионирование и который можно опубликовать для использования в других проектах.

**Инициализация модуля**. Чтобы превратить проект (каталог) в модуль, в корневом каталоге проекта надо выполнить команду
```bash
go mod init [имя_модуля]
```

Появится файл `go.mod`. Этот файл объявляет путь к модулю, версию Go, используемую в проекте, и список всех зависимостей с их минимальными требуемыми версиями.
Если наш проект будет использовать Git и располагаться где-то во репозитории, то обычно в качестве имени модуля используется URL репозитория. Например
```bash
go mod init github.com/your-username/your-project
```

## Импорты
Можно так
```go
import "fmt"
import "math"
```

Но лучшей практикой будет использование скобок для группировки
```go
import (
	"fmt"
	"math"
)
```

При использовании имени в пакете оно должно быть с **большой** буквы
```go
fmt.Println(math.Pi)  // Правильно
fmt.Println(math.pi)  // undefined: math.pi
```

При импорте своего пакета должен буть указан его путь (который в `.mod` файле)
```go
import (
	"fmt"
	"math"
	"demo/mypackage"
)

mypackage.MyFunc()  // чтобы использовать ресурсы пакета его функции нужно
//    вызывать с БОЛЬШОЙ буквы, хотя в пакете они были названы с маленькой
```

#### Сторонние пакеты
[Поиск пакетов на go.dev](https://pkg.go.dev/)
Пример импорта и использования стороннего пакета.

Скачиваем пакет
```bash
go get github.com/fatih/color
```

```go
package main

import (
	"math/rand"  
	"github.com/fatih/color"
)

func main() {
	runes := "qwertyuiopasdfghjklzxcvbnmQWERTYUIOPASDFGHJKLZXCVBNM1234567890_!@$"
	result := ""
	for i := 0; i < 15; i++ {
		result += string(runes[rand.Intn(len(runes))])
	}
	color.Cyan(result)
}
```

Удаляем неиспользуемые зависимости
```bash
go mod tidy
```

## Типы данных
```go
bool
----------------------------------------
string
----------------------------------------
int  int8  int16  int32  int64             // целые положительные и отрицательные
uint uint8 uint16 uint32 uint64 uintptr    // целые положительные
byte // alias for uint8

rune // alias for int32
     // represents a Unicode code point

float32 float64

complex64 complex128
----------------------------------------
array                                       // Агрегатные
struct
----------------------------------------
slice                                       // Ссылочные
map
function
channel
pointer
----------------------------------------
interface
----------------------------------------
error
```

## Переменные
>[!info] В Go используют CamelCase, т.е. `userHeight`

Переменным, объявленным без явного начального значения, присваивается _нулевое значение_
- `0`для числовых типов,
- `false`для булевого типа и
- `""`(пустая строка) для строк.

Объявление нескольких переменных с **одним** типом
```go
var c, python, java bool
```

Объявление с инициализацией. Если присутствует инициализатор, тип можно опустить
```go
var i, j int = 1, 2
var c, python, java = true, false, "no!"
```

Внутри функции можно использовать оператор `:=` для быстрого объявления с неявным типом
```go
func main() {
	k := 3
}
```

## Константы
```go
const Truth = true
```

В одном определении можно объявить разу несколько констант
```go
const (
    pi float64 = 3.1415
    e float64 = 2.7182
)
```

Инициализировать константу значением переменной нельзя
```go
var m int = 7
const k = m   // ОШИБКА
```

## Преобразование типов
Выражение `T(v)` конвертирует значение `v` в тип `T`
```go
var i int = 42
var f float64 = float64(i)
```

## Функции
Типы в аргументах идут **после** имени переменной
```go
func add(x int, y int) int {  // возвращаемый тип int
	return x + y
}
```

Если у всех аргументов один тип, можно его ставить только последней переменной
```go
func add(x, y int) int {  // возвращаемый тип int
	return x + y
}
```

Функции могут возвращать любое количество аргументов
```go
func swap(x, y string) (string, string) {  // возвращаемый тип (string, string)
	return y, x
}
```

Возвращаемое значение может быть именованным
```go
// Это называется "голый" возврат. Ухудшает читаемость в длинных функциях
func split(sum int) (x, y int) {
	x = sum * 4 / 9
	y = sum - x
	return
}
```

#### Defer
Оператор defer позволяет выполнить определенную функцию в конце окружающей функции. Обычно с помощью оператора **defer** выполняется операции очистки, например, закрытие файлов, или какие-то действия, которые должны быть выполнены в самом конце.
```go
package main
import "fmt"

func main() {
    defer finish()
    fmt.Println("Program has been started")
    fmt.Println("Program is working")
}

func finish(){
    fmt.Println("Program has been finished")
}
```


## Циклы
Go имеет только цикл **FOR**
```go
func main() {
    for i := 0; i < 10; i++ {
        fmt.Println(i)
    }
}
```

Инициализатор и пост-условие *необязательны*. Так получается аналог `while`
```go
func main() {
	sum := 1
	for sum < 1000 {
		sum += sum
	}
	fmt.Println(sum)
}
```

Бесконечный цикл
```go
for {
}
```

Также присутствуют `break` и `continue`
```go
for i := 0; i < 10; i++ {
    fmt.Println(i)
    if i == 5 {
        break
    }
}
```

## Логические операции
```go
a == b         // Равно
a < b
a <= b
a > b
a => b
a != b         // НЕ равно
!a             // Инвертирует значение
a && b         // И
a || b         // ИЛИ
```

## Условные конструкции
#### If
```go
if x < 0 {
	fmt.Println(x)
}
```

#### If-else
```go 
a := 6
b := 7

if a < b {
    fmt.Println("a меньше b")
} else {
    fmt.Println("a больше b")
}
```

```go
a := 6

if a < b {
    fmt.Println("a меньше b")
} else if a == b {
    fmt.Println("a равно b")
} else if a > b {
    fmt.Println("a больше b")
}
```

#### Switch
```go
a := 8
switch(a) {
    case 9:
        fmt.Println("a = 9")
    case 8:
        fmt.Println("a = 8")
    case 7:
        fmt.Println("a = 7")
}
```

Также конструкция switch может содержать *необязательных* блок `default`, который выполняется, если ни один из операторов case не содержит нужного значения
```go
a := 8
switch(a) {
    case 9:
        fmt.Println("a = 9")
    case 8:
        fmt.Println("a = 8")
    case 7:
        fmt.Println("a = 7")
    default:
        fmt.Println("значение переменной a не определено")
}
```

Также можно указывать после оператора case сразу несколько значений
```go
a := 8
switch(a) {
    case 9, 8, 7:
        fmt.Println("a = 9 или 8 или 7")
    default:
        fmt.Println("значение переменной a не определено")
}
```

Аналог `if-else if`
```go
a := 8
switch {         // аналог switch true
    case a < 9:
        fmt.Println("a < 9")
    case a == 9:
        fmt.Println("a = 9")
    case a > 9:
        fmt.Println("a > 9")
}
```

## Ввод пользователя
```go
var yourVariable float64

fmt.Scan(&yourVariable)  // указатель на переменную
```

## Строки
Многострочный вывод
```go
myString = `d32asd32as
dasdasd45
sad32
343`
```

Для возврата функций `Print` существуют их аналоги `Sprint`, `Sprintf`, `Sprintln`
```go
result := fmt.Sprintf("Ваш индекс массы тела: %.2f \n", IMT)
```

Итерация по строке
```go
var str = "Hello!"

func main() {
    for _, v := range str {
        fmt.Println(v, string(v))  // вывод кода Unicode и приведение его к строке
    }
}
// 72 H
// 101 e
// 108 l
// 108 l
// 111 o
// 33 !
```

#### Форматирование
Вывод с форматированием
```go
var yourVariable float64 = 122.7745634208234
fmt.Printf("Ваше значение: %v", yourVariable)
```

[Описание подстановочных значений](https://pkg.go.dev/fmt#section-documentation)
Значение
```go
fmt.Printf("Now you have %g problems", math.Sqrt(7))
```

Тип
```go
fmt.Printf("Now you have %T problems", math.Sqrt(7))
```

## Исключения
```go
import "errors"

func myFunc(number int) error {                // Указываем что будет возвращатся ошибка
    if number > 2 {
        return errors.New("Кастомная ошибка")  // Создаём новую ошибку
    } else {
        return nil                             // Возвращаем ничего (аналог None)
    }
}

str, err := myFunc(5)
str, _ := myFunc(5)     // пустая переменная вместо err если надо проигнорировать

if err != nil {
    fmt.Println(err)
}
```

###### panic
Если мы НЕ можем обработать ошибку(что-то критическое) выдаём `panic` вместо создания ошибки
```go
panic("Какая-то катастрофичекая ошибка. Невозможно продолжить")
```
При этом в терминале при панике будет выход из приложения с другим статусом(отличным от нормального), будет выведено наше сообщение и указано место ошибки в коде.

## Массивы
>[!info] Однотипная последовательность фиксированной длины. При присваивании существующего массива какой-то переменной происходит ПОЛНОЕ КОПИРОВАНИЕ массива.

Объявление массива
```go
var exampleArray [2]int  //[0 0]
// ИЛИ
exampleArray := [2]int{}
```

```go
// [длина_массива]тип{элемент1, элемент2, элемент3}
numbers := [4]int{99, 88, 77, 66}
strgs := [2]string{"Hello", "World"}
```

Доступ к элементам
```go
fmt.Println(exampleArray[0])
```

Изменеие элемента
```go
exampleArray[0] = 32
```

Длина массива
```go
len(exampleArray)
```

Вместимость массива. Capacity определяет сколько места осталось от начала массива/среза до конца
```go
cap(exampleArray)
```

Итерация по массиву
```go
for index, value := range myArray {  // Если индекс не нужен используйте _ вместо index
    fmt.Println(index, value)
}
```

## Slice
>[!info] Срезы - это ссылки и при изменении значения среза меняется и исходный массив

Срез массива `array[start:stop]`.Конечное значение не включается в срез. Как в Python 
```go
numbers := [5]int{99, 88, 77, 66, 55}  // Массив для примера
mySlice := numbers[1:4]  // Срез массива [88 77 66]
mySlice := numbers[1:]  // С индекса 1 до конца
mySlice := numbers[:3]  // С начала до индекса 3
```

Срез динамической длины
```go
mySlice := []int{1,2,3,4,5,6}   // под капотом будет создан массив
mySlice = append(mySlice, 100)  // [1 2 3 4 5 6 100]
```

Распаковка. Оптимальный способ соединения массивов и срезов
```go
mySlice := []int{1,2,3}
hugeSlice := []int{4,5,6}
mySlice = append(mySlice, hugeSlice...)  // [1 2 3 4 5 6]
```

Чтобы оптимизировать выделение памяти можно явно задать `capacity` с помощью функции `make`
```go
mySlice := make([]int, 0, 5)  // длина 0, capacity(макс. вместимость) 5
mySlice = append(mySlice, 1)  // и теперь здесь не происходит доп. выделение памяти
mySlice = append(mySlice, 2)
mySlice = append(mySlice, 3)
```

## Map
Создание
```go
myMap := map[string]string{  // map[тип_ключа]тип_значения
    "Key": "Value",
}
```

Доступ
```go
// Чтение
fmt.Println(myMap["Key"])

// Изменение
myMap["Key"] = "asdf"

// Добавление
myMap["New Key"] = "qwerty"

// Удаление
delete(myMap, "New Key")
```

Итерация по Map
```go
for key, value := range myMap {
    fmt.Println(key, value)
}
```

Чтобы оптимизировать выделение памяти можно явно задать кол-во элементов
```GO
m = make(map[string]string, 10)
```

## Labels
Именование циклов и свичей
```go
Menu:                // Label
    for {
        a := 8
        
        MySwitch:    // Label
            switch(a) {
                case 9:
                    fmt.Println("a = 9")
                case 8:
                    break Menu
                case 7:
                    fmt.Println("a = 7")
                default:
                    break MySwitch
             }
    }
```

## Type Alias
Алиасы для удобства
```go
type bookmarkMap = map[string]string

bookmarks := bookmarkMap{}
```

## Указатели
>[!info] Указатели представляют собой объекты, значением которых служат адреса других объектов (например, переменных). С помощью указателей можно передавать объекты по ссылке (предотвращая лишнее копирование) и таким образом мутировать исходное значение.

```go
var p *int          // определяем указатель
```

```go
num := 10                  // объявление переменной
numPointer := &num         // получение адреса и присвоение его переменной numPointer
fmt.Println(*numPointer)   // получение значения из указателя
```

Пример функции наботающей с указателем и изменяющей исходное значение без копирования
```go
func double(num *int) {  // аргумент - указатель на int
    *num = *num * 2      // получаем значения из указателя и умножаем его на 2
}
```

Пример разворота массива с помощью указателя "на месте"
```go
func main() {
    a := [4]int{1, 2, 3, 4}
    reverseArray(&a)
    fmt.Println(a)
}

func reverseArray(arr *[4]int) {
    // берутся два индекса первый и последний и оба стремятся к середине,
    //    меняя значение друг на друга
    for i, j := 0, len(arr)-1; i < j; i, j = i+1, j-1 {
        arr[i], arr[j] = arr[j], arr[i]
    }
}
```

## Struct
>[!info] Структуры представляют составной тип данных, определяемый разработчиком и служащий для представления каких-либо объектов.

```go
type account struct {
    login string
    password string
    url string
}

my_login = "xxx"
my_password = "12345"
my_url = "asdf"

account1 := account{
    my_login,
    my_password,
    my_url,
}

// ещё один синтаксис
account2 := account{
    login: my_login,
    password: my_password,
    url: my_url,
}

// создание пустого инстанса
account2 := account{}

// доступ к полям инстанса
fmt.Println(account2)
fmt.Println(account2.password)
account2.password = "yyy"
```

#### Методы Struct
```go
func (acc *account) printPassword {  // указатель на struct account
    fmt.Println(acc.password)
}
```

#### Конструктор
```go
func newAccount(login, password, url string) (*account, error) {  // возвращаем указатель
    // тут можно проводить валидацию
    
    return &account{
        url: url,
        login: login,
        password: password,
    }, nil
}

myAccount, err := newAccount("name", "12345", "https://a.ru")
if err != nil {
    ...
}
```

#### Композиция (встраивание)
```go
type account struct {
    url string,
    login string,
    password string,
}

type accountWithTimeStamp struct {
    createdAt time.Time,
    updatedAt time.Time,
    account
}

newAcc := &accountWithTimeStamp{
    createdAt: time.Now(),
    updatedAt: time.Now(),
    account: account{
        url: "https:\\google.com",
        login: "xxx",
        password: "123456",
    }
}
```

## Случайные числа
```go
package main
import (
    "fmt"
    "math/rand/v2"
)

func main() {
    fmt.Println(rand.IntN(10))  // Псевдо-случайное число от 0 до 10 НЕ включая его
}
```

## Запись в файл
```go
import (
    "os"
    "fmt"
)


func WriteFile(content string, name string) {
    file, err := os.Create(name)
    if err != nil {
        fmt.Println(err)
    }
    _, err = file.WriteString(content)
    if err != nil {
        file.Close()
        fmt.Println(err)
        return
    }
    fmt.Println("Запись успешна")
    file.Close()
}
```


