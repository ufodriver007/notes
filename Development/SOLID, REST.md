#### SOLID
>[!info] Принципы ООП, устанавливающие практики, помогающие создавать ПО, которое можно обслуживать и расширять. Подход как сделать код менее связанным и более обособленным.

###### S (single responsibility principle) Принцип единственной ответственности
1 класс(сущность) = 1 задача(зона ответственности. Но не упарываться черезмерно)
Улучшается читаемось(из-за декомпозиции), уменьшается связываемость(легче совместно разрабатывать и версионировать)

###### O (open-closed principle) Принцип открытости-закрытости
Сущности должны быть открыты для расширения, но закрыты для изменения
Стараться не изменять, а дополнять сущности(создавать новые)

###### L (Liskov Substitution Principle) Принцип подстановки Барбары Лисков
Наследуемый класс должен ДОПОЛНЯТЬ, а не замещать возможности базового класса. И иметь единообразный интерфейс. 
Т.е. функции, использующие базовый класс должны иметь возможность использовать его подтипы.

###### I (interface segregation principle) Принцип разделения интерфейса
Программные сущности не должны зависеть от методов, которые они не используют
Более предсказуемая работа, код становится менее связанным. 
Т.е. создавайте детализированные интерфейсы, специфичные для конкретного клиента(Класс "Круг" не должен уметь рисовать квадрат).

###### D (dependency inversion principle) Принцип инверсии зависимости
Модули высокого уровня не должны зависеть от модулей более низкого уровня все они
должны зависеть от абстракций. Абстракции не должны зависеть от деталей. Детали 
должны зависеть от абстракций. 
Т.е. не сылайтесь(и не наследуйтесь) на изменчивые конкретные классы, ссылайтесь на абстрактные.

#### REST
Representational state transfer(Передача состояния представления)
>[!info] Архитектурный стиль взаимодействия компонентов распределённого приложения в сети. Другими словами, REST — это набор правил того, как программисту организовать написание кода серверного приложения, чтобы все системы легко обменивались данными и приложение можно было масштабировать

###### Принципы REST
1) **Независимость от состояния(Statelessness)**
   Сервер не должен запоминать состояние рользователя между запросами. В каждом запросе
   передаётся инфо, идентифицирующая пользователя и все параметры, необходимые для
   выполнения операции.
2) **Многоуровневая архитектура(Layered System)**
   Деление по слоям проще для отладки.
3) **Единый унифицированный программный интерфейс**
   К примеру для получения списка фильмов вы используете URL вида: /videos.com/movies , а для
   получения информации о конкретном фильме ваш URL будет: /videos.com/movies/1
4) **Кэшируемая архитектура(Cacheable)**
   Ответ сервера может быть кэширован на определённый период времени и использоваться
   повторно без новых запросов к серверу
5) **Удобное представление данных**
   В качестве представления данных объекта передаются данные в формате JSON или XML

###### Методы http
- GET       (получение(чтение))
- POST      (создание)
- PUT       (изменение)
- PATCH     (частичное изменение)
- DELETE    (удаление)
