![[Go_Logo_Blue.svg|300]]

>[!info] Go - компилируемый статически типизированный язык программирования от компании Google. Автоматиическая сборка мусора, параллелизм, поддержка многоядерности, кроссплатформенность.

## Hello, world
Создадим файл `hello.go`
```go
package main
import "fmt"

func main() {
    fmt.Println("Hello, world!")
}
```

Запуск компилятора и выполнение программы
```bash
go run hello.go
```

Сделать билд
```bash
go build hello.go
```
