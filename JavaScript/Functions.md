Функции в JavaScript

Function declaration

function add(param1, param2) {
    return param1 + param2;
}

- Hoisted (всплывают) - т.е. можно сначала использовать, а потом объявить (Как и var объявления)

Function expression - можно передавать в другие функции

var add = function (param1, param2) {
    return param1 + param2;
};

Можно получить все аргументы в виде объекта через ключевое слово arguments (выглядит как массив, но без методов, можно сделать Array)

Тэговые шаблоны

Можно создать функцию, которая будет содержать массив строк и аргументы, которые мы подставляем в строку

function getTag(strings, value1, value2){ // value1 ’11’, value2 4
  const strBefore = strings[0]; // I got
  const strMiddle = strings[1]; // values and
  const strAfter = strings[2];  // cats

  return `${strBefore}${value1}${strMiddle}${value2}${strAfter}`
}


console.log(getTag`I got ${'11'} values and ${4} cats`)


Factory functionФункция, которая создает и инициализирует объект внутри себя и после его возвращает
Не используем с new, так как результат будет тот же, а объект, создаваемый new, просто выкинется
