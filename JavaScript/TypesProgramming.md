Программирование на типах


Вкратце, что у нас происходит в deepType. У нас есть какой-то объект. Мы вообще на уровне, когда с ним взаимодействуем, не знаем, какие у него поля - это сделано для того, чтобы всю логику ТДГ (заполнение, отправку полей) вынести в родительский компонент ТДГ, чтобы в дальнейшем, когда уже будет создавать интерфейсы, там описывать просто внешний вид, не задумываясь над тем, как у нас обновляются и отправляются на бэк данные. Вот, но при этом, так как у нас вся логика в родителе, а он вообще не знает, чем он оперирует, нам надо как-то добавить валидации, чтобы мы не могли пропихнуть какой-то левый тип или левое поле. А без валидаций мы это можем сделать просто - мы в дочернем указали, какой интерфейс используется, но он у нас используется только в родителе. В дочернем попытаемся поменять какое-нибудь поле, которое родителю не принадлежит - родитель это проглатывает и мы падаем. Нехорошо, надо сделать так, чтобы у нас родитель мог валидировать, что мы в него вообще пихаем и не принимать то, что ему не подходит

Собственно, для этого все это программирование на типах тут и сделано. Мы дочернему компоненту передаем только сам объект, который из дочернего уже меняется, и сеттер для его изменения. А дочерний указывает, какой тип должен быть у этого объекта. И уже дальше, когда мы объявляем поля и используем функцию сеттер, в нее мы передаем путь, по которому родитель должен будет этот объект поменять. И вот тут у нас включается в работу DeepKeyWithType - мы для конкретного компонента-поля указываем, с каким типом он может работать (пр. чекбокс для булевых, тайпахед для строковых массивов и т.п.) и в дочернем компоненте, когда указываем путь, у нас проверяется, существует ли вообще такой путь в типе, с которым мы оперируем, и соответствует ли тип данных тому, который мы указали
А теперь немного более детально
DeepKey - принимает на вход тип, которым мы оперируем (что от нас ждет ТДГ) и тип объекта, который мы не разматываем (пока нам это не надо, но, если нам нужно будет какой-то тип не разматывать, а обновить его целиком, это пригодится). Здесь мы смотрим и, если у нас этот объект не разматывается дальше (т.е. если у него больше нет уровней размотки, он массив или мы ему сказали, что такой не разматываем) - мы возвращаем never. Если это была первая итерация, то never и вернется. Если не первая, то он просто завершит запись пути и вернет результат. На выходе, кстати, мы получаем все возможные пути в объекте с делителем-точкой
{
  foo: {
    bar: {
      a: string,
      c: string
    },
    d: string
  },
  k: string
}
result: 'foo.bar.a' | 'foo.bar.c' | 'foo.d' | 'k'
Вот, мы в первой итерации получили объект, далее берем у него все ключи, убираем у них опциональность и символы, если есть, и далее составляем путь по каждому ключу - записываем сначала сам ключ, потом вызываем рекурсивно этот же метод для объекта, который уже находится в этом ключе и, если его у нас можно размотать, то мы записываем результат этой рекурсии, если нет - то просто выходим

DeepValue у нас, соответственно, по пути и делителю в этом пути получает тип, который находится в этом поле в интерфейсе, с которым мы оперируем. Тут мы смотрим, если у нас путь указан и путь соответствует виду "что-то1 делитель что-то2", при этом что-то1 у нас является ключом от исходного интерфейса, то мы сваливаемся в рекурсию, пока у нас переданный путь не будет ключом первого уровня, чтобы мы могли просто вернуть тип этого ключа. По поводу вопроса "Почему мы стрингу экстендим от пути, а не наоборот". Y extends Х у нас работает так, что, если у нас в Y содержатся все поля из Х (может содержаться и больше, главное, чтобы все поля их Х содержались), то это true. Т.е. у нас путь может содержать больше полей, чем в стринге, и тогда нам это не подходит

DeepKeyWithType - здесь мы проходимся по всем путям, которые нам вернул DeepKey, и смотрим, если у нас DeepKey по этому пути соответствует тому типу, который мы передали, то мы этот путь возвращаем, если не соответствует - просто откидываем



export type DeepPartial<T> = T extends object
  ? {
      [P in keyof T]?: DeepPartial<T[P]>;
    }
  : T;

export type DeepKey<T> = T extends unknown[]
  ? never
  : T extends object
  ? {
      [K in keyof T]-?: `${Exclude<K, symbol>}${DeepKey<T[K]> extends never ? '' : `.${DeepKey<T[K]>}`}`;
    }[keyof T]
  : never;

export type DeepValue<T, Path extends string, Delimeter extends string> = string extends Path
  ? never
  : Path extends ''
  ? never
  : Path extends `${infer K}${Delimeter}${infer U}`
  ? K extends keyof T
    ? DeepValue<T[K], U, Delimeter>
    : never
  : Path extends keyof T
  ? T[Path]
  : never;

export type DeepKeyWithType<T, V> = T extends object
  ? {
      [K in DeepKey<T>]: DeepValue<T, K, '.'> extends V ? K : never;
    }[DeepKey<T>]
  : never;

export type DeepKeyNumber<T> = DeepKeyWithType<T, number | undefined>;
export type DeepKeyBoolean<T> = DeepKeyWithType<T, boolean | undefined>;
export type DeepKeyString<T> = DeepKeyWithType<T, string | undefined>;
export type DeepKeyStringArray<T> = DeepKeyWithType<T, string[] | undefined>;

export function getAttributeFromPath<T, V>(path: DeepKey<T>, obj: DeepPartial<T>): V | undefined {
  const pathParts = path.split('.');
  let deepObject: T | object = obj;
  let value: V | undefined;

  for (let index = 0; index < pathParts.length; index++) {
    const partTyped = pathParts[index] as keyof typeof deepObject;

    if (deepObject[partTyped] == null) {
      return;
    }

    if (index < pathParts.length - 1) {
      deepObject = deepObject[partTyped];
    } else {
      value = deepObject[partTyped];
      continue;
    }
  }
  return value;
}

export function setAttributeFromPath<T, V>(path: DeepKey<T>, obj: DeepPartial<T>, value: V): void {
  const pathParts = path.split('.');
  let deepObject: T | object = obj;

  for (let index = 0; index < pathParts.length; index++) {
    const partTyped = pathParts[index] as keyof typeof deepObject;

    if (deepObject[partTyped] == null) {
      (deepObject[partTyped] as V) = value;
      return;
    }

    if (index < pathParts.length - 1) {
      deepObject = deepObject[partTyped];
    } else {
      (deepObject[partTyped] as V) = value;
    }
  }
}



Не негативный интеджер и не пустой массив
￼
