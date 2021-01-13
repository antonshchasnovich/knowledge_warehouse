# SOLID

## Single Responsibility Principle
**Single Responsibility Principle (Принцип единственной ответственности) - каждый класс должен решать лишь одну задачу.** Класс должен быть ответственен лишь за что-то одно. Если класс отвечает за решение более чем одной задачи его нужно разбить на несколько классов. Принцип применим не только к классам, но и к компонентам программного обеспечения в более широком смысле.

Пример:
```
class Animal {
    constructor(name: string){ }
    getAnimalName() { }
    saveAnimal(a: Animal) { }
}
```

Класс решает две задачи: работа с хранилищем в методе saveAnimal() и манипуляция свойствами объекта в конструкторе и методе getAnimalName(). Проблема в том что если изменится порядок работы с хранилищем данных то придется вносить изменения во все классы, работающие с хранилищем. Для того что бы привести данный код в соответствие с принципом создадим новый класс, единственной задачей которого будет работа с хранилищем данных:

```
class Animal {
    constructor(name: string){ }
    getAnimalName() { }
}
class AnimalDB {
    getAnimal(a: Animal) { }
    saveAnimal(a: Animal) { }
}
```

## Open-Closed Principle

**Open-Closed Principle (Принцип открытости-закрытости) - программные сущности (классы, модули, функции) должны быть открыты для расширения, но не для модификации.**

Пример:

Мы хотим перебрать список с объектами класса Animal и узнать какой звук они издают.
```
function AnimalSound(a: Array<Animal>) {
    for(int i = 0; i <= a.length; i++) {
        if(a[i].name == 'lion')
            return 'roar';
        if(a[i].name == 'mouse')
            return 'squeak';
        if(a[i].name == 'snake')
            return 'hiss';
    }
}
```
Проблема в том что при данной архитектуре при добавлении новых видов животных нужно будет изменять функцию. Для решения данной проблемы лучше добавить абстрактный метод в класс Animal каждый вид животного будет иметь свой класс, наследоваться от класса Animal и иметь свою реализацию метода. 

```
class Animal {
        makeSound();
        //...
}
class Lion extends Animal {
    makeSound() {
        return 'roar';
    }
}
class Squirrel extends Animal {
    makeSound() {
        return 'squeak';
    }
}
class Snake extends Animal {
    makeSound() {
        return 'hiss';
    }
}
//...
function AnimalSound(a: Array<Animal>) {
    for(int i = 0; i <= a.length; i++) {
        a[i].makeSound();
    }
}
AnimalSound(animals);
```

При добавлении новых видов животных нужно будет лишь создать новый класс и реализовать метод makeSound(). Изменений существующего кода не потребуется.


## Liskov Substitution Principle
**Liskov Substitution Principle (Принцип подстановки Барбары Лисков) - необходимо, чтобы подклассы могли бы служить заменой для своих суперклассов.** Цель этого принципа заключается в том, чтобы классы-наследники могли бы использоваться виесто родительских классов, от которых они образованы, не нарушая работу программы. Если оказывается, что в коде проверяется тип сласса, значит принцип подстановки нарушается.

Пример:

Мы хотим пройтись по списку животных и получить информацию о количествах конечностей каждого животного.

```
//...
function AnimalLegCount(a: Array<Animal>) {
    for(int i = 0; i <= a.length; i++) {
        if(typeof a[i] == Lion)
            return LionLegCount(a[i]);
        if(typeof a[i] == Mouse)
            return MouseLegCount(a[i]);
        if(typeof a[i] == Snake)
            return SnakeLegCount(a[i]);
    }
}
AnimalLegCount(animals);
```

Этот код нарушает принцип подстановки (и принцип открытости-закрытости). Этот код должен знать о типах всех обрабатываемых им объектов и, в зависимости от типа, обращаться к соответствующей функции. Как результат, при создании нового типа животного функцию придется переписывать. Что бы функция не нарушала принцип подстановки мы можем сделать следующее: добавить абстрактный метод LegCount() в класс Animal и реализовать его для каждого животного, затем пройти по списку животных и вызвать данный метод.

```
class Animal {
    //...
    LegCount();
}
//...
class Lion extends Animal{
    //...
    LegCount() {
        //...
    }
}
//...
function AnimalLegCount(a: Array<Animal>) {
    for(let i = 0; i <= a.length; i++) {
        a[i].LegCount();
    }
}
AnimalLegCount(animals);
```

Теперь функции AnimalLegCount() не нужно знать о том, объект какого именно подкласса класса Animal она обрабатывает.

## Interface Segregation Principle

**Interface Segregation Principle (Принцип разделения интерфейса) - создавайте узкоспециализированные интерфейсы, предназначенные для конкретного клиента. Клиенты не должны зависеть от интерфейсов, которые они не используют.** Этот принцип направлен на устранение недостатков, связанных с реализацией больших интерфейсов.

Пример:

```
interface Shape {
    drawCircle();
    drawSquare();
    drawTriangle();
}
```

Данный интерфейс описывает методы для рисования разных фигур (круг, квадрат, прямоугольник). В результате классы, реализующие этот интерфейс и представляющие отдельные фигуры должны содержать реализацию всех этих методов. То есть например класс Circle (круг) должен содержать реализацию методов отрисовки квадрата и треугольника - drawSquare(), drawTriangle(). Данный интерфейс решает задачи, для решения которых следует создать отдельные интерфейсы для решения узкоспециализированных задач:

```
interface ICircle {
    drawCircle();
}
interface ISquare {
    drawSquare();
}
interface ITriangle {
    drawTriangle();
}
class Circle implements ICircle {
    drawCircle() {
        //...
    }
}
class Square implements ISquare {
    drawSquare() {
        //...
    }
}
class Triangle implements ITriangle {
    drawTriangle() {
        //...
    }
}
```

Теперь интерфейс ICircle используется лишь для рисования кругов, равно как и другие специализированные интерфейсы - для рисования других фигур.

# Dependency Inversion Principle

**Dependency Inversion Principle (Принцип инверсии зависимостей) - объектом зависимости должна быть абстракция, а не что-то конкретное**. А) Модули верхних уровней не должны зависеть от модулей нижних уровней. Оба типа модулей должны зависеть от абстракций. В) Абстракции не должны зависеть от деталей. Детали должны зависеть от абстракций. 

Пример:

```
class PasswordReminder {
     private $dbConnection;
 
     public function __construct(MySQLConnection $dbConnection) {
         $this->dbConnection = $dbConnection;
     }
}
```

MySQLConnection является низкоуровневым модулем, PasswordReminder - высокоуровневый. Данный код нарушает принцип инверсии зависимостей так как PasswordReminder зависит от MySQLConnection. Проблема в том что если позже изменить ядро базы данных, то придется менять и класс PasswordReminder, что нарушит принцип открытости-закрытости. Класс PasswordReminder не должен беспокоиться об используемой базе данных.

```
interface DBConnectionInterface {
     public function connect();
}

class MySQLConnection implements DBConnectionInterface {
     public function connect() {
         return "Database connection";
     }
}
 
class PasswordReminder {
     private $dbConnection;
 
     public function __construct(DBConnectionInterface $dbConnection) {
         $this->dbConnection = $dbConnection;
     }
}
```

Теперь оба модуля зависят от абстракции.