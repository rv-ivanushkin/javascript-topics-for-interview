# Что такое итератор, генератор и как ими пользоваться?

## Содержание

- [Итератор](#итератор)
- [Как создать итерируемую последовательность для обычного объекта?](#как-создать-итерируемую-последовательность-для-обычного-объекта)
- [Как явно вызвать следующее значение в итераторе?](#как-явно-вызвать-следующее-значение-в-итераторе)
- [Генератор](#генератор)
- [Как создать генератор](#как-создать-генератор)
- [Как явно вызвать следующее значение в генераторе?](#как-явно-получить-следующее-значение-в-генераторе)
- [Примеры](#примеры)
- [Источники-данных](#источники-данных)

## Итератор

В интернете можно найти множество определений того, что такое итератор и вот одно из них:

> итератор — это объект, который предоставляет метод next(), возвращающий следующий элемент последовательности.

Кажется, что все по делу, но лично меня смущает `это объект, который предоставляет метод next()`, словно здесь чего-то не хватает.

Мне кажется, что понятнее всего для начала сказать, что:

> итератор - правила или протокол описывающее некое поведение для объекта

И так, что за правило?

Объект является итератором, если в нем определён **обязательный** метод next(), который:

- не принимает входных аргументов
- возвращает объект `IteratorResult`, где определены следующие свойства:
  - `done` (Boolean) - `true`, если итератор достиг конца итерируемой последовательности. А `false`, если итератор может получить следующее значение итерируемой последовательности.
  - `value` - любое JavaScript значение, возвращаемое итератором. Может быть опущено, если done имеет значение true

Получается, что любой объект в JavaScript может быть итератором, нужно лишь определить метод `next`, который соответствует `IteratorResult`

Теперь можно дать следующее определение:
> итератор - протокол, описывающий способ получение последовательности значений (конечной или бесконечной)

Хорошо, есть обязательный метод, а какие **необязательные** методы есть у итератора?

- `return(value)` - метод, который принимает нуль или один аргумент и возвращает объект типа `IteratorResult`, где `done` равен `true`, а `value` равен входному аргументу.
  Не могу не воспользоваться цитатой из ecma262, для полного понимания.
  > The returned object must conform to the IteratorResult interface. Invoking this method notifies the Iterator object that the caller does not intend to make any more next method calls to the Iterator. The returned IteratorResult object will typically have a "done" property whose value is true, and a "value" property with the value passed as the argument of the return method. However, this requirement is not enforced.
- `throw(exception)` - метод, который принимает нуль или один аргумент и возвращает объект типа `IteratorResult`,  где `done` равен `true`, а `exception` является экземпляром **Error**
  Не могу не воспользоваться цитатой из ecma262, для полного понимания.
  > The returned object must conform to the IteratorResult interface. Invoking this method notifies the Iterator object that the caller has detected an error condition. The argument may be used to identify the error condition and typically will be an exception object. A typical response is to throw the value passed as the argument. If the method does not throw, the returned IteratorResult object will typically have a "done" property whose value is true.

И того,
> **Итератор** или **Протокол итератора** определяет тип объекта, в котором есть как минимум метод `next`, а так же могут быть определены два необязательных метода `return` и `throw`. Все эти методы должны возвращать объект типа `IteratorResult`

## Как создать итерируемую последовательность для обычного объекта?

Для начала возьмем обычный объект

```js
const simpleObject = {
  startValue: 0
  endValue: 10
}
```

и добавим в него функционал итерирования

```js
const simpleObject = {
  startValue: 0,
  endValue: 10,
  /*
   * Здесь я вернул this чтобы не создавать внутри return объект с методами next и return
   * такой подход позволяет перенести контекст на уровень выше и сделать код понятным
   */
  [Symbol.iterator]() {
    this.i = this.startValue;
    return this;
  },

  // та самая функция или метод, который вызывается при итерации
  next() {
    if (this.i <= this.endValue) {
      // объект типа IteratorResult
      return { done: false, value: this.i++ };
    }
    // объект типа IteratorResult
    return { done: true };
  },

  return() {
    console.log("hello and goodbye");
    return { done: false };
  }
};
```

*Примечание: есть еще метод `throw`, но хороший пример использования я так и не нашел*

А теперь возьмем конструкцию `for of`

```js
for (let value of simpleObject) {
  console.log(value);
  if (value === 19) {
    break;
  }
}

output: 1
.........
output: 19
output: hello and goodbye 
```

Можно даже попробовать получить массив из значений `value`

```js
console.log(Array.from(simpleObject));

output: (21) [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, …]
```

## Как явно вызвать следующее значение в итераторе?

Для примера возьмем простую строку

```js
const myString = "Hello, i'm big and beautiful string"

// Symbol.iterator как секретное слово для получение внутренних полей объекта
const iterator = myString[Symbol.iterator]()

while (true) {
  let result = iterator.next();
  if (result.done) break;
  console.log(result.value);
}
```

В `console` будет выведена строка по одному символу, что в итоге дает ответ о том, как вызвать итератор явно

## Генератор

В интернете существует разные определения генераторов

> Это функция которая может приостанавливать своё выполнение, возвращать промежуточный результат и далее возобновлять своё выполнение в произвольный момент времени.

Звучит интересно, но нужно разобраться глубже в том что такое генератор

> Генератор - это объект, возвращаемый функцией-генератором и соответствующий протоколу "Итератору"

Можно ли сказать, что генератор - частный случай итератора?
В какой-то степени да, но сходства можно сразу выделить:

- Как и в итераторах, генераторы представляет собой объект
- Как и в итераторах, генераторы должны иметь метод `next`

Но с генератором можно обмениваться данными и тем самым формировать какую-то логику

## Как создать генератор?

Без лишних слов создадим функцию генератор

```js
function* getGenerator() {
  yield 1;
  yield 2;
  yield 3;
}

const myGenerator = getGenerator();

console.log(myGenerator)

output: GeneratorFunctionPrototype {next: ƒ (), throw: ƒ (), return: ƒ (), toString: ƒ (), output: output: constructor: Object}
output: next: ƒ () {}
output: throw: ƒ () {}
output: return: ƒ () {}
output: toString: ƒ () {}
output: <constructor>: "GeneratorFunctionPrototype"
```

Синтаксис достаточно простой и в `console` сразу видно, что все базовые методы соответствуют протоколу "Итератор"

## Как явно получить следующее значение в генераторе?

```js
console.log(myGenerator.next());

output: {value: 1, done: false} // структура уже знакомая нам, IteratorResult
```

так же можно пройтись с `for of`

```js
for (let i of myGenerator) {
  console.log(i)
}

output: 1
output: 2
output: 3
```

Результаты пока достаточны знакомы и понятны, а что с объектами?

```js
const simpleObject = {
  startValue: 0,
  endValue: 10,
  // в самом начале есть *, которая говорит о том, что это генератор
  // так как генератор возвращает структуру IteratorResult, то дополнительного делать не нужно
  *[Symbol.iterator]() {
    for (let value = this.startValue; value <= this.endValue; value++) {
      yield value;
    }
  }
};

for (let i of simpleObject) {
  console.log(i);
}
```

Как мне кажется, то код стал выглядеть "чище" и понятнее.

> Генераторы были добавлены в язык JavaScript, в частности, с целью упростить создание перебираемых объектов.

## Общение с генератором

Если передать методу `next` генератору, то можно добавить разные условия, например

```js
function* gen() {
  let resultFirst = yield;
  if (resultFirst === 100) {
    console.log("Vee, ", resultFirst);
  }
  let resultSecond = yield;
  if (resultSecond === 200) {
    console.log("Vee, ", resultSecond);
  }
}

let generator = gen();
console.log(generator.next());
console.log(generator.next(100));
console.log(generator.next(200));
```

Все достаточно очевидно, за исключением - `generator.next()`.
Этот момент очень интересен тем, что выполнение кода доходит до первого yield и останавливается до следующего вызова метода `next`

## Примеры

<details>
  <summary>Генерация случайного числа</summary>

  ```js
  function* uniqueNumber(multiplier) {
    let value = multiplier;

    while (true) {
      value = (value * 16807) % 2147483647;
      yield value;
    }
  }

  const UUID = uniqueNumber(10);

  for (let i = 0; i <= 10; i += 1) {
    console.log(UUID.next().value);
  }
  ```

</details>

## Источники данных

- [Перебираемые объекты](https://learn.javascript.ru/iterable)
- [Генераторы](https://learn.javascript.ru/generators)
- [The Iterator protocol](https://developer.mozilla.org/ru/docs/Web/JavaScript/Reference/Iteration_protocols)
- [ecma262/sec-iteration](https://tc39.es/ecma262/#sec-iteration)
- [Углублённое руководство по JavaScript: генераторы. Часть 1, основы](https://habr.com/ru/company/vk/blog/539194/)