# Что такое контекст вызова или `this` и как его явно установить?

## Содержание

## Что такое `this`?

Ключевое слово `this` - это в первую очередь ссылка на контекст исполнения или доступа для функций и объектов.

Контекст формируется динамически, но его можно контролировать, через методы `call`, `apply`, `bind`

Но прежде чем разбираться с контекстом исполнения, нужно понять а где он встречается или где с ним можно столкнуться?

- Использование `this` внутри объектов
- Использование `this` внутри функций, кроме стрелочных
- Использование `this` внутри функции, объявленной через оператор `new`

> контекстом всегда является какой-то объект из под которого был вызван метод (функция).

Довольно интересное определение

> Контекст — это всегда значение ключевого слова this, которое является ссылкой на объект, который запустил метод (функцию). Контекст — это объект "владеющий" исполняемым кодом. А this всегда ссылаться на объект (контекст) запустивший функцию.

### Использование `this` внутри объектов

Достаточно простой пример

```js
const getFullDescription = function () {
  return `FullDescription: ${this.name}, ${this.fullName}`
}

const superUser = {
  name: "Rick",
  fullName: "Mega Rick Dunes",
  getFullDescription: getFullDescription
}

console.log(superUser.getFullDescription())
```

Есть объект `superUser` у которого определен метод getFullDescription, который в свою очередь выводит данные через ключевое слово `this`

Здесь вроде логично и понятно, функция `getFullDescription` получается контекст из "области родителя".

т.е.

```js
console.log(getFullDescription()) // Uncaught TypeError: Cannot read properties of undefined (reading 'name')
```

Будет ошибка, так как у глобального объекта или вышестоящего нет свойств `name` и т.д.

### Использование `this` внутри функции, объявленной через оператор `new`

```js
const GetThis = function () {
  return this
}

console.log(new GetThis());
```

выведет пустой объект, так как вызванные функции через ключевое слово `new` возвращают ссылку на только что созданный объект

так же для проверки того, что объект создается всегда новый ниже приведен пример

```js
const GetThis = function () {
  return this
}

const object_1 = new GetThis()
const object_2 = new GetThis()

console.log(object_1 === object_2);
console.log(object_1, object_2);
```

### This и стрелочная функции

```js
const getFullDescription = () => {
  return `FullDescription: ${this.name}, ${this.fullName}`
}

const superUser = {
  name: "Rick",
  fullName: "Mega Rick Dunes",
  getFullDescriptionArrowOutside: getFullDescription,
  getFullDescription: function () {
    return () => `FullDescription: ${this.name}, ${this.fullName}`
  }
}

console.log(superUser.getFullDescription()())
```

Как видно из примера выше `this` ссылается на `this` функции `getFullDescription`, а та в свою очередь на сам superUser.

Это значит, что - контекст или объект ссылка на которой лежит в `this` определяется в месте объявлении функции.

## Как можно контролировать значение `this`?

Для этого можно использовать следующие методы вызова функций:

- `fun.call(thisArg[, arg1[, arg2[, ...]]])`, Метод call() вызывает функцию с указанным значением this и индивидуально предоставленными аргументами.
- `fun.apply(thisArg, [argsArray])`, Метод apply() вызывает функцию с указанным значением this и аргументами, предоставленными в виде массива
- `fun.bind(thisArg[, arg1[, arg2[, ...]]])`, Метод bind() создаёт новую функцию, которая при вызове устанавливает в качестве контекста выполнения this предоставленное значение.

## Источника данных

- [Context vs. Scope](http://ryanmorr.com/understanding-scope-and-context-in-javascript/)