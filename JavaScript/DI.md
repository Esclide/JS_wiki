DI и декораторы

IoC - инверсия управления - принцип ООП, при котором объекты не зависят от конкретной реализации других объектов, но знают об их интерфейсах

DI - паттерн, при котором один объект (сервис) создает другие сервисы с их зависимостями, известными только по интерфейсам

DI-контейнер — это такой модуль, который занимается только тем, что обеспечивает зависимостями остальные модули.

Контейнер знает, какие зависимости какому модулю нужны, создаёт и внедряет их, когда это нужно.

https://bespoyasov.ru/blog/di-ts-in-practice/

Контроллер мы описываем для роутинг контроллера, а сервис и репозиторий - для тайп ди. Когда вначале мы деклалируем контроллеры, мы подсовываем роутингу контейнер, чтобы он мог искать в нем свои контроллеры, и тайп ди создает все классы и подсовывает зависимости. Экземпляры создаются только при первом обращении - конструктор до этого не вызывается

Роутинг - сопоставление, на какой кусок кода какой контроллер вызвать.
routingUseContainer - routing controller берет контроллеры из контейнера. Он у DI запрашивает контроллер, а typeDI уже гарантирует, что контроллер придет со всеми зависимостями

Выполняющийся код вообще ничего не знает про типы данных, так что любой объект - это кусок памяти, где есть поинтер на начало и каждое свойство - смещение относительно начала объекта. foo.abc = адрес foo + смещение для abc.
Чтобы была типизация, ее надо докладывать метаданными.
При компиляции имя класса - смещение на начало блока класса. RTTI - понимание, что вот это смещение мы хотим использовать как тип, оно очень недешевое

Декораторы - добавление метаданных к классам и их членам и изменение их без изменения кода
Декораторы представляют функции, которые могут применяться к классам, методам, методом доступа (геттерам и сеттерам), свойствам, параметрам.

При использовании декоратора компилятор вызывает системную функцию __decorate

Декораторы Функций

declare type MethodDecorator =
    <T>(
        target: Object, - объект, у которого функция была вызвана
        propertyKey: string | symbol, - имя функции
        descriptor: TypedPropertyDescriptor<T>)  - дескриптор функции
=> TypedPropertyDescriptor<T> | void;

Дескриптор нужен для доступа к исходной функции

function LogTime() {
    return (target: Object, propertyName: string, descriptor: TypedPropertyDescriptor<Function>) => {
        const method = descriptor.value;
        descriptor.value = function(...args) {
            console.time(propertyName || 'LogTime');
            const result = method.apply(this, args);
            console.timeEnd(propertyName || 'LogTime');
            return result;
        };
    };
}


Код декоратора будет вызываться каждый раз при вызове функции


Декораторы классов

функция, применяющаяся к конструктору класса и позволяющая изменять определение класса. При использовании, среда исполнения сначала вызовет функцию-декоратор, и только потом будет выполнен основной сценарий объекта (если код декоратора содержит этот вызов). При наличии нескольких декораторов, они будет вызваны по очереди, сверху вниз.

В основном используется для добавления классу метаданных. Тут уже используется Reflect-metadata


interface Metadata {
    singleton?: boolean;
}

function CustomBehavior(metadata: Metadata) {
    return function(ctor: Function) {
        Reflect.defineMetadata('metadataKey', metadata, ctor);
    }
}

@CustomBehavior({
    singleton: false,
})
class TestServiceDeco {
    constructor() {
        console.log('TestServiceDeco ctor');
    }
}

Reflect-metadata это хранилище метаданных в Typescript. Его смысл тот же, что и в других языках — хранить информацию о типах для работы с ней в процессе выполнения программы

Контейнер для получения инстанса класса:

const instancesMap: Map<Object, Object> = new Map<Object, Object>();

function getInstance<T>(tType: new () => T): T {
    let metadata = Reflect.getMetadata('metadataKey', tType) as Metadata;
    if (metadata.singleton) {
        if (!instancesMap.has(tType)) {
            instancesMap.set(tType, new tType());
        }
        return instancesMap.get(tType) as T;
    } else {
        return new tType() as T;
    }
}

* наш контейнер состоит из единственной функции getInstance, в которую будет передаваться тип, класс, экземпляр которого необходимо создать
* с помощью Reflect.getMetadata мы получаем информацию, которую передали с помощью декоратора. Так как эта функция возвращает any, нам приходится добавлять as Metadata для приведения к своему типу
* так как нам необходимо создавать экземпляры, нам нужен конструктор. Поэтому накладываем ограничение tType: new () => T
* и конечно нужен какой-то способ хранения созданных экземпляров, в нашем простом случае это Map
Можно заменять конструктор класса. В таком случае мы возвращаем новую функцию, которая будет вызываться вместо конструктора или же возвращаем новый класс

export function AllowTransactions<T extends new (...args: any[]) => {}>(constructor: T): T {
  const result = class extends constructor {

    constructor(...args: any[]) {
      super(...args);
    }
  }
    result.prototype.name = constructor.name;
    return result;
  }

Декораторы полей или свойств классов

Можно, например, навершивать валидацию на поля, добавляя методанные и уже затем в валидаторе получая из метаданных, что нам нужно провалидировать и как

Сам декоратор:

function Age(from: number, to: number) {
    return function (object: Object, propertyName: string) {
        const metadata = {
            propertyName: propertyName,
            range: { from, to },
        };
        Reflect.defineMetadata(`validationMetadata_${propertyName}`, metadata, object.constructor);
    };
}

В методе validate описываем необходимые проверки и вызываем ее, передавая объект созданного класса

Декораторы параметров функций

declare type ParameterDecorator = (target: Object, propertyKey: string | symbol, parameterIndex: number) => void;


Где первый параметр представляет конструктор класса, если метод статический, либо прототип класса, если метод нестатический. А второй параметр представляет имя метода. И третий параметр представляет порядковый индекс параметра в списке параметров.



вап
chrome://inspect/#devices -> Open dedicated DevTools for Node
В рамках ноды await - это пауза, т.е. функция вышла. На await соединение разрывается



Через телеграф собирали метрики докер контейнера
