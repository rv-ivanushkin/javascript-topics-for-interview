# Как можно объявить функцию и какие есть особенности?

## Содержание

- [6 способов объявить функцию](#6-способов-объявить-функцию)
  - [Function declaration](#1-function-declaration)
  - [Function expression](#2-function-expression)
  - [Method-definitions](#3-method-definitions)
  - [Arrow function expressions](#4-arrow-function-expressions)
  - [Generator function](#5-generator-function)
  - [new Function](#6-new-function)
- [Параметры функции](#параметры-функции)
  - [Именованные параметры](#именованные-параметры)
  - [Параметры по умолчанию](#параметры-по-умолчанию)
  - [Деструктуризация для аргумента функции](#деструктуризация-для-аргумента-функции)
  - [Какое наименование функции выбрать?](#какое-наименование-функции-выбрать?)
- [Что такое функции высшего порядка (Higher Order Functions)?](#что-такое-функции-высшего-порядка-higher-order-functions)
- [Источники данных](#источники-данных)

## 6 способов объявить функцию

### 1. Function declaration

> Объявление функции состоит из ключевого слова function, за которым следует обязательное имя функции, списка параметров в круглых скобках (param1, ..., paramN) и фигурных скобок {...}, ограничивающих основной код.

```js
  function getUniqueIDBuyName(){
    // something interesting
  }
```

<h4>
  <span style="color:red">
  <b>Важно:</b> функции поднимается в верхнюю часть текущей области видимости, что означает, что функция может быть вызвана до объявления
</span>
</h4>

```js
  const idCar = getUniqueIDBuyName('kia')

  function getUniqueIDBuyName(args){
      // something interesting
    }
  ```

### 2. Function expression

> Выражение функции определяется ключевым словом function, за которым следует необязательное имя функции, списком параметров в круглых скобках (param1, ..., paramN) и парой фигурных скобок {...}, ограничивающих тело код.

```js
  const getUniqueIDBuyName = function (args) {
    // something interesting
  }
  ```

### 3. Method definitions

> Сокращенное определение метода можно использовать в объявлении метода в литеральных объектах и классах ES2015. Вы можете определить метод, используя имя функции, за которым следует список параметров в круглых скобках (param1, ..., paramN) и пара фигурных скобок {...}, разделяющих операторы тела.

```js
const mySuperObject = {
  add(...args) {
    // something interesting
  }

  remove(...args) {
    // something interesting
  }
}
```

<h4>
  <span style="color:yellow">
  <b>Интересно:</b> вычисляемые свойства объектам позволяют использовать значения переменных для именование свойство объекта
</span>
</h4>

```js
const get = 'get'
const add = 'add'
const del = 'del'

const mySuperObject = {
  [get](...args){
    // something interesting
  },
  [add](...args){
    // something interesting
  },
  [add](...args){
    // something interesting
  }
}
```

### 4. Arrow function expressions

> Стрелочная функция определяется с помощью пары круглых скобок, которые содержат список параметров (param1, ..., paramN), за которым следует стрелка => и пара фигурных скобок {...}, ограничивающих тело функции.

```js
const myFirstArrowFunc = (...args) => {
  // something interesting
}

```

Свойства стрелочной функции:

- Стрелочная функция не создает свой контекст выполнения, но принимает его лексически (в отличие от выражения функции или объявления функции, которые создают собственный this в зависимости от вызова)
- Стрелочная функция анонимна. Однако интерпретатор может вывести её имя из переменной, содержащей функцию
- Объект arguments недоступен в стрелочной функции (в отличие от других типов объявлений, которые предоставляют объект arguments). Однако вы можете использовать остальные параметры (... params)

### 5. Generator function

> Функция-генератор в JavaScript возвращает объект Generator. Его синтаксис аналогичен выражению функции, объявлению функции или объявлению метода, только для него требуется символ звездочки *

Подробная информация в [Что такое итератор, генератор и как ими пользоваться?](./generator_iterator.md)

### 6. new Function

Основное отличие от всех выше перечисленных способов объявления заключается вто, что при использовании конструкции `new Function` функция создается "на лету", т.е. можно создавать функции динамически в зависимости от разных параметров или какой-то иной логи

```js

const args = ['argName1', 'argName2', 'argName3', '...others']
const body = `
  console.log(argName1);
  console.log(argName2);
  console.log(argName3);

  console.log('Veeeeeeeeeeeeee', others);
`
const mySuperFunc = new Function (args, body)

mySuperFunc('send_argName1', 'send_argName2', 'send_argName1', new Array(10).fill(0))

```

В console будет выедено

```js
send_argName1
send_argName2
send_argName1
Veeeeeeeeeeeeee [Array(10)]
```

## Параметры функции

- arguments - локальный псевдомассив, который доступен в внутри любой не стрелочной функции, а так же объект arguments не является Array. Он похож на массив, но не обладает ни одним из его свойств, кроме length. Например, у него нет метода pop.
- ... - буквально это значит: «собери оставшиеся параметры и положи их в массив»

```js
const mySuperFunction = function (){
  console.log(arguments)
}
```

Еще важные определения:

- Параметр – это переменная, указанная в круглых скобках в объявлении функции.
- Аргумент – это значение, которое передаётся функции при её вызове.

### Именованные параметры

Тут все достаточно просто, перечисляем `параметры` функции в круглых скобка

```js
const mySuperFunction = (name, description) => {
  // something interesting
  // name - Параметр
  // description - Параметр
}
```

### Параметры по умолчанию

```js
const mySuperFunction = (name = "Nikola", description="Doctor") => {
  // something interesting
  // name - Параметр, который имеет значение по умолчанию - Nikola
  // description - Параметр, который имеет значение по умолчанию - Doctor
}
```

### Деструктуризация для аргумента функции

Здесь просто используется распространенный паттерн:

```js
const mySuperFunction = ({name, description}) => {
  console.log(name, description)
}
```

## Какое наименование функции выбрать?

> Функция – это действие. Поэтому имя функции обычно является глаголом. Оно должно быть кратким, точным и описывать действие функции, чтобы программист, который будет читать код, получил верное представление о том, что делает функция.
>
> Как правило, используются глагольные префиксы, обозначающие общий характер действия, после которых следует уточнение. Обычно в командах разработчиков действуют соглашения, касающиеся значений этих префиксов.

## Что такое функции высшего порядка (Higher Order Functions)?

> Эта функция, которая принимает или возвращает другие функции

На самом деле тут все достаточно просто, стоит только подобрать хороший пример:

Higher Order Functions в JavaScript

- [map](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map)
- [filter](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/filter)
- [forEach](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/forEach)
- [reduce](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/reduce)

Во всех выше перечисленных функция, в параметрах функции используется другая функция

## Источники данных

- [Синтаксис "new Function"](https://learn.javascript.ru/new-function)
- [6 способов объявления функций в JavaScript](https://fruntend.com/posts/6-sposobov-obyavleniya-funktsiy-v-javascript)
- [Остаточные параметры (...)](https://learn.javascript.ru/rest-parameters-spread-operator)
- [The arguments object](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/arguments)
- [Деструктурирующее присваивание](https://learn.javascript.ru/destructuring-assignment)