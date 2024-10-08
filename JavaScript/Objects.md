Объекты

Характеристики, определяющие работу объектов под капотом, собраны в протокол метаобъектов (MOP). С его помощью можно переопределять дефолтное поведение объекта в программе

Property Descriptors - каждая пропертя объекта описывается отдельным метаобъектом с некоторыми аттрибутами.
	Получить свойства: getOwnPropertyDescriptor <- value, enumerable, writable, configurable
	Добавить свойства: defineProperty (мы указываем имя новой проперти и объект свойств)
	Переопределить свойства - если не указано configurable: false, то тоже defineProperty

При переопределении объект свойств заменяется целиком!

Accessor Property - специальный тип для проперти, содержащий геттер/сеттер
Тут нам не надо определять value, вместо этого мы указываем методы, которые будут вызываться, когда мы хотим присвоить или получить значение

Object.defineProperty(anotherObj,"fave",{
  get() { console.log("Getting 'fave' value!"); return 123; },
  set(v) { console.log(`Ignoring ${v} assignment.`); }
});

Аттрибуты Property Descriptor

value - значение свойства
или
get() {return} + set(v) {} - геттер и сеттер, которые будут вызываться на получение или присвоение

configurable - можно ли менять конфиг для проперти
enumerable - будет ли пропертя показываться среди ключей и можно ли по ней итерироваться циклом
writable - можно ли менять значение проперти

Для объекта есть статические методы, меняющие его характеристики

preventExtensions - запрещаем добавлять новые проперти в объект. Не запрещает менять текущие
freeze - ставит configurable и writable в false, а также запрещает добавлять новые проперти
seal -  ставит configurable в false, а также запрещает добавлять новые проперти. Менять текущие не запрещает

Сабтипы объектов

sub-type это объект, который унаследовал все от родителя-объекта, но при этом имеет и что-то свое

Arrays

объекты, предназначенные для числовой индексации. Все еще можно задать символьный индекс, но не рекомендуется
могут содержать пустые слоты (они не заполняются undefined, они просто пустые)
некоторые методы (например, map) просто скипают такие пустые слоты

Функции

есть два предопределенных свойства - name и length (длина параметров за исключением тех, у кого есть значение по умолчанию)

Прототипы

Не надо путать [[Prototype]] и свойство объекта prototype

[[Prototype]] - внутренняя ссылка, которая назначается объекту при его создании, указывая на другой объект. Эта линка спрятана и влияет на поведение объекта. Все объекты прилинкованы к Object.prototype

Объект может использовать методы его прототипа (как, например, toString, hasOwnProperty), наследуя их. Это реализует прототипное наследование

Можно создать объект с кастомным прототипом:

myObj = Object.create(differentObj);

или

myObj = {
  __proto__: differentObj,

  // .. the rest of the object definition
};

Все объекты ссылаются на Object.prototype, а он сам - на null. Мы можем сами сделать объект, прототипом которого является null

При этом свойство функции prototype указывает не на ее прототип, а на то, объект с каким прототипом будет создан при вызове функции. Т.е. new Object = Object.prototype, new Function = Function.prototype.

Конструктор

При помощи ключевого слова new можно вызвать конструктор, в результате которого будет создан новый объект (уже со своим this), после будет выполнено тело конструктора и возвращен результат консутруктора или, если он ничего не возвращает, то созданный объект

Конструктором может быть только function expression (у него указан prototype, у declaration - нет)

const myObj = {
    x: null,
    y: null,

    init1(a, b) {
        console.log('init1 logs');
        console.dir(this);
        this.x = a;
        this.y = b;
    },

    init2: function (a, b) {
        console.log('init2 logs');
        console.dir(this);
        this.x = a;
        this.y = b;
    }
};

init1 - не конструктор (при вызове с new будет ошибка)
init2 - конструктор (прототип - объект с конструктором init2)

const obj2 = new myObj.init2(4, 5)

new keyword

Когда мы создаем новый объект, происходят следующие действия:
1. Создается новый пустой объект
2. К этому объекту привязывается прототип из конструктора
3. Вызывается конструктор с this, установленным как новый пустой контекст {}
4. Если конструктор не возвращает значение, то она вернет созданный объект, иначе она вернет значение и ссылка на созданный объект подотрется

Конструкторами не являются
- Стрелочные функции
- function declarations в объекте


