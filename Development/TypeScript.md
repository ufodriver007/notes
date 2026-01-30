![[Typescript.svg]]

>[!info] **TypeScript (TS)** — это язык программирования, разработанный Microsoft, который является надмножеством JavaScript, добавляющим строгую статическую типизацию. TS компилируется в обычный JavaScript (JS), позволяя использовать его в любых браузерах или Node.js, но при этом значительно повышает безопасность и читаемость кода, помогая находить ошибки до запуска программы.

[Документация](https://www.typescriptlang.org/docs/)
[[JavaScript]]

## Установка
```bash
npm i -g typescript
```

Компилятор
```bash
tsc --version
```

Инициализация конфига `tsconfig.json`
```bash
tsc --init
```

Компиляция ts файлов
```bash
tsc
```

## Основные типы данных

|Тип данных|Пример|
|---|---|
|**number**|`let x: number = 10;`|
|**string**|`let test: string = "Test";`|
|**boolean**|`let isTrue: boolean = true;`|
|**object**|_при проверке, а не при определении_`userEntity: { firstName: string, surname: string }`|
|**Array**|`const skills: string[] = ["Dev", "Devops"];`|
|**Tuples**|`const skill: [string, number, ...boolean[]] = ["Dev", 1, true, true, true];`|
|**Enum**|`enum StatusCodes { OK = 200, CREATED = 201 }`|
|**any**|_лучше не использовать_|

#### Функции
Обычная
```ts
function getFullName(firstName: string, surname: string): string {
	return `${firstName} ${surname}`;
}
```

Стрелочная
```ts
const getFName = (firstName: string, surname: string): string => {
	return `${firstName} ${surname}`;
};
```

#### Объекты
```ts
const user = {
	firstname: "Anton",
	surname: "Larichev",
	city: "Moscow",
	age: 23,
	isProgrammer: true,
};

// Хоть у самого объекта и больше свойств, чем в принимаемом типе,
//   использовать можно только описанные в типе свойства
function getObjectName(userEntity: { firstName: string, surname: string }): string {
	return `${userEntity.firstName} ${userEntity.surname}`;
}
```

#### Массивы
```ts
const skills: string[] = ["Dev", "Devops"];

// Generic
const sk: Array<string> = ["Dev", "Devops"];
```

неизменяемый массив
```ts
const skills: readonly string[] = ["Dev", "Devops"];

// Generic
const ski: ReadonlyArray<string> = ["Dev", "Devops"];
```

#### Tuples
```ts
// перечисляем на каком месте какой тип
const skill: [string, number] = ["Dev", 1];
```

```ts
const skill: [string, number, ...boolean[]] = ["Dev", 1, true, true, true];
```

неизменяемый кортеж
```ts
const skill: readonly [string, number, ...boolean[]] = ["Dev", 1, true, true, true];
```

#### Enums
Перечисления
```ts
enum StatusCodes {
  OK = 200,
  CREATED = 201,
  BAD_REQUEST = 400,
  UNAUTHORIZED = 401,
  FORBIDDEN = 403,
  NOT_FOUND = 404,
  INTERNAL_SERVER_ERROR = 500,
}

if (result.statusCode === StatusCodes.OK) {
  console.log("200 OK");
}

function greet(status: StatusCodes): string {
  return "test";
}
```

## Продвинутые типы данных
#### Union
```ts
const arr: (string | number | boolean)[] = [1, "asdf", true];
```

###### Сужение типов (Narrowing)
```ts
function myFunction(id: string | number | boolean) {
  if (typeof id === "string") {
    console.log(id.toLocaleLowerCase);
  } else if (typeof id === "number") {
    console.log(id.toFixed(2));
  } else {
    console.log("Boolean ID:", id);
  }
}

function logError(err: string | string[]) {
  if (Array.isArray(err)) {
    console.log(err);
  } else {
    console.log(err);
  }
}

function logObject(obj: { a: number } | { b: number }) {
  if ("a" in obj) {
    console.log(obj.a);
  } else {
    console.log(obj.b);
  }
}

```

#### Literal Types
```ts
let a = "asdf";    // тип string
const a = "asdf";  // тип asdf
```

```ts
function fetchWithAuth(
  url: string,
  method: "GET" | "POST" | "PUT" | "DELETE",
  type: 1 | 2,
): 1 | -1 {
  return -1;
}
```

#### Type Aliases
Псевдонимы для типов
```ts
type httpMethod = "GET" | "POST" | "PUT" | "DELETE";

function fetchW(
  url: string,
  method: httpMethod,
) {
  return -1;
}
```

```ts
type User = {
  name: string,
  age: number,
  skills: string[],
};

let User = {
  name: 'Alex',
  age: 33,
  skills: ['Python', 'JavaScript'],
};
```

Пересечение типов
```ts
type Role = {
  id: number,
};

type UserWithRole = User & Role;  // пересечение (И)

let user: UserWithRole = {
  name: 'Alex',
  age: 33,
  skills: ['Python', 'JavaScript'],
  id: 1,
};
```

Также может описывать объект с функцией
```ts
type User = {
  name: string,
  age: number,
  skills: string[],
  
  log: (id: number) => string;
};
```

```ts
type UserDict = {
	[index: number]: User;
}
```

#### Interfaces
>[!info] Интерфейсы могут работать только с объектами и классами. Для простых типов  используйте **Type Aliases**

```ts
interface User {
  name: string,
  age: number,
  skills: string[],
};

let user: User = {
  name: 'Alex',
  age: 33,
  skills: ['Python', 'JavaScript'],
};
```

```ts
interface Role {
  roleId: number;
}

interface UserWithRole extends User, Role {  // пересечение интерфейсов
  createdAt: Date;
}

let roleUser: UserWithRole = {
  name: 'Alex',
  age: 33,
  skills: ['Python', 'JavaScript'],
  roleId: 1,
  createdAt: new Date(),
};
```

```ts
interface User {
  name: string,
  age: number,
  skills: string[],
  
  log: (id: number) => string;
};
```

```ts
interface UserDic {
  [index: number]: User;
}
```

#### Optional
Необязательные поля пишем с `?`
```ts
interface User {
  login: string,
  password?: string,
}

type User = {
  login: string,
  password?: string,
}


const user: User = {
  login: 'alex'
};
```

Передача необязательного аргумента в функцию
```ts
function multiply(first: number, second?: number): number {
  if (!second) {
    return first * first;
  }
  return first * second;
}
```

Для доступа к свойства объекта, которых может не быть, используем _optional chaining_
```ts
interface UserPro {
  login: string,
  password?: {
    type: string,
  }
}

function createUser(user: UserPro) {
  const u = user.password?.type;
}
```

#### Void
>[!info] Если функция в принципе ничего не возвращает. Если существует вероятность возврата какого--либо значения, её тип возврата будет **тип возвращ. значения** | **undefined**

```ts
function log(id: string): void {
  console.log(id);
}

const a = log('123');  // тип a - void
```

#### Unknown
>[!info] Когда мы хотим типизацию, но не можем, потому что неизвестно что придёт в переменную

```ts
let input: unknown;
```

```ts
async function getData() {
  try {
    await fetch('https://example.com/data');
  } catch (error: unknown) {
    if (error instanceof Error) {
      console.log(error.message);
    }
  }
}
```

#### Never
>[!info] Никогда не будет присвоено значение

```ts
const a: never = undefined;  // ошибка! значение присваивается
const a: void = undefined;   // а void может вернуть undefined
```

```ts
function generateError(id: string): never {
  throw new Error(`Error: ${id}`);
}
```

#### Null
```ts
const n: null = null;
```

#### Приведение типов
```ts
let a = 5;
let b: string = a.toString();
let c: string = new String(a).valueOf();
let d: boolean = new Boolean(a).valueOf();

let e = 'asdf';
let f: number = parseInt(e);
```

#### Type Guard
>[!info] **Type Guard**  — это механизм **сужения типа** значения во время выполнения, чтобы компилятор мог безопасно работать с конкретным типом.

```ts
function isString(x: string | number): x is string {
  return typeof x === "string";
}
```

```ts
interface User {
  id: number;
  email: string;
}

interface Admin extends User {
  role: string;
}

function isAdmin(user: User | Admin): user is Admin {
  return 'role' in user;
}
```

## Классы
>[!tip] Чтобы в классах type checker не требовал инициализатора в `tsconfig.json` указываем `"strictPropertyInitialization": false`

```ts
class User {
  name: string;

  constructor(name: string) {
    this.name = name;
  }
}
```

Если инициализатор не проверяется, то как обычно
```ts
class Admin {
  role: number;
}
```

#### Конструктор
1. Вызывается при использовании `new`
2. Всегда возвращает экземпляр класса и это не типизируется
3. Не можем использовать дженерики
4. Можем перегружать конструктор

>[!tip] Как работает перегрузка:
>1. **Опишите несколько сигнатур** для метода (только типы, без реализации).
>2. **Реализуйте метод** с общей сигнатурой, которая покрывает все случаи.

```ts
class User {
  name: string;
  age: number;

  constructor();
  constructor(name: string);
  constructor(age: number);
  constructor(name: string, age: number);
  constructor(ageOrName?: string | number, age?: number) {
    if (typeof ageOrName === "string") {
      this.name = ageOrName;
    } else if (typeof ageOrName === "number") {
      this.age = ageOrName;
    }
    if (typeof age === "number") {
      this.age = age;
    }
  }
}

const user = new User();
const user2 = new User(33);
const user3 = new User("Vasya");
const user4 = new User("Vasya", 33);
```

#### Методы
```ts
enum PaymentStatus {
  Holded,
  Processed,
  Reversed,
}

class Payment {
  id: number;
  status: PaymentStatus = PaymentStatus.Holded;
  createdAt: Date = new Date();
  updatedAt: Date;

  constructor(id: number) {
    this.id = id;
  }

  getPaymentLifetime(): number {
    return new Date().getTime() - this.createdAt.getTime();
  }

  unholdPayment(): void {
    if (this.status == PaymentStatus.Processed) {
      throw new Error("Платёж не может быть возвращён");
    }
    this.status = PaymentStatus.Reversed;
    this.updatedAt = new Date();
  }
}
```

Пример перегрузки методов
```ts
class User {
  skills: string[];
  
  addSkill(s: string): void;
  addSkill(skillsList: string[]): void;
  
  addSkill(skillOrList: string | string[]): void {
    if (Array.isArray(skillOrList)) {
      for (let skill of skillOrList) {
        this.skills.push(skill);
      }
    } else {
      this.skills.push(skillOrList);
    }
  }
}
```

#### Getters и Setters
>[!info!] Геттеры и сеттеры НЕ могут быть асинхронными. Если нужно асинхронка - используем *методы*
>
```ts
class User {
  _login: string;
  password: string;

  set login(l: string) {
    this._login = "user-" + 1;
  }

  get login() {
    return "no_login";
  }
}

const user = new User();
console.log((user.login = "Alex"));  // Alex
console.log(user.login);             // no_login
```

#### Имплементация (реализация) интерфейсов
```ts
interface IPayable {
  pay(paymentId: number): void;
  price?: number;
}

interface IDeletable {
  delete(): void;
}

class User implements IPayable, IDeletable {
  pay(paymentId: number): void {
    //
  }
  delete(): void {
    //
  }
}
```

#### Наследование
>[!info] Предпочитайте наследование, если это одна предметная область, в противном случае используйте композицию.

>[!info] При наследовании сначала инициализируются свойства род. класса, затем род. конструктор и только потом свойства дочернего класса и его конструктор

```ts
type PaymentStatus = 'new' | 'paid';

class Payment {
  id: number;
  status: PaymentStatus = 'new';
  
  constructor(id: number) {
    this.id = id;
  }
  
  pay() {
    this.status = 'paid';
  }
}

class PersistedPayment extends Payment {
  databaseId: number;
  paidAt: Date;
  
  constructor() {
    const id = Math.random();
    super(id);  // обязательно вызываем родит. конструктор, если переопределяем 
                //   конструктор в дочернем классе
  }
  
  override pay(date?: Date) {  // перегрузка родит. метода
    super.pay();               // можно вызвать родит. метод (это поможет избежать копирования)
    if (date) {
      this.paidAt = date;
    }
  }
}
```

#### Видимость свойств класса
```ts
class Vehicle {
  color: string;              // публичное свойство
  public model: string;       // явноое задание публичного свойства
  private damages: string[];  // видимо только изнутри класса
  protected run: number;      // видимо только изнутри класса и для его дочерних классов
  #price: number;             // приватное св-во из JS, оставленное для обратной совместимости
  
  addDamage(damage: string) {
    this.damages.push(damage);
  }
}
```

#### Статические свойства и методы
```ts
class UserService {
  static db: any;
  
  static getUser(id: number) {
    return UserService.db.findById(id);
  }
}

UserService.db;
```

#### Абстрактные классы
>[!info] Абстрактные классы - это классы у которых невозможно создать экземпляр. В отличии от интерфейсов у них можно писать обычные (не абстрактные) методы

>[!info]  Абстрактыне методы ДОЛЖНЫ быть реализованы в дочерних классах!

```ts
abstract class Controller {
  abstract handle(req: any): void;
  
  handleLogs(req: any) {
    console.log(req);
    this.handle(req);
  }
}

class UserController extends Controller {
  handle(req: any): void {
    console.log(req);
  }
}
```