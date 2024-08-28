Классы

Тело класса всегда выполняется в strict mode. Есть class declaration (не hoistiтся) и class expression

При class expression имя класса будет доступно только внутри его самого

typeof ClassName = function

Конструктор

Метод, созданный для создания и инициализации классов. Выполняется до вызова остальных методов

Если конструктор не определен, будет вызван по умолчанию. Если это наследник, то будет вызван конструктор родителя

Есть проперти new.target, которой можно определить, какого класса создается объект (родителя или наследника)

Нестатические методы


Function declaration

Нестатические методы хранятся в объекте, который задается конструктором класса (указан в prototype конструктора):class SomeClass {
  prototype: {    constructor: class SomeClass
    someClassMethod
  }
}

this у них указывает на содержимое класса - нестатические переменные, functional expressions. Также имеет доступ к functional declarations через прототип и к конструктору

Не имеет доступа к статическим методам и переменным

Function expression (or arrow function)

То же самое, что Function declaration, но уже хранятся в инстансе класса. Разница только в том, что Function expression мы получаем из самого инстанса, а Function declaration - из прототипа


Нестатические переменные

Нестатические методы хранятся в инстансе класса (когда мы создаем его через new)


Статические методы и переменныеРасположены в скоупе конструктора, прототип у них - функция, так что у них нет доступа до нестатических методов. Имеют доступ до статических параметров и переменных.

Они недоступны из нестатических методов, но их можно вызывать SomeClass.staticMethod() или this.constructor.staticMethod()

Синтаксический сахар?

1. Вызов конструктора только со словом new (так как есть концепция new.target)
2. Возможность расширения встроенных классов Да, через прототипы мы можем это сделать, но те же map, slice методы создают новый объект по символу Symbol.species, который указывает, какой конструктор должен использоваться для создания новых объектов

  class MyArray extends Array {
    // Перегружаем species для использования родительского конструктора Array
    static get [Symbol.species]() {
      return Array;
    }
  }
  var a = new MyArray(1, 2, 3);
  var mapped = a.map((x) => x * x);

  console.log(mapped instanceof MyArray); // false
  console.log(mapped instanceof Array); // true



https://habr.com/ru/companies/ruvds/articles/554288/

https://learn.javascript.ru/extend-natives

https://habr.com/ru/companies/ruvds/articles/554288/

https://learn.javascript.ru/weakmap-weakset

https://habr.com/ru/companies/vk/articles/340922/

https://qna.habr.com/q/506475
