# Что такое генератор и как им пользоваться?

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

*Примечание: есть еще метод `throw`, но хороший пример использования я так и не нашел*

## Источники данных

- [Перебираемые объекты](https://learn.javascript.ru/iterable)
- [Генераторы](https://learn.javascript.ru/generators)
- [The Iterator protocol](https://developer.mozilla.org/ru/docs/Web/JavaScript/Reference/Iteration_protocols)
- [ecma262/sec-iteration](https://tc39.es/ecma262/#sec-iteration)