# В чем различия `var`, `let`, `const` и что такое `hosing`?

## Содержание

- [Проблема ключевого слова `var`](#проблема-ключевого-слова-var)
- [Сравнение `var`, `let`, `const`](#сравнение-var-let-const)
- [Hosing](#hosing)
- [Hosing функций](#hosting-функций)
- [Порядок по приоритетам](#порядок-по-приоритетам)
- [Источники данных](#источники-данных)

## Проблема ключевого слова `var`

Для начала необходимо понять а что не так с `var`

Возьмем пример

```js
function makeSome() {
  var count = 10

  if (true) {
    for (var count = 20; count < 30; count +=1) {
      // do something
    }
  }
  console.log(count) // ожидаем 10, а получаем 30
}

makeSome()
```

Вся проблема примера выше в том, что ключевое слово `var` имеет глобальную в рамках функции область видимости и следовательно в цикле `for` не создается новая переменная, а переопределяется.

Для решении этой проблемы есть ключевые слова `let`, `const`

## Сравнение `var`, `let`, `const`

|   | Область видимости | Без инициализации | Повторное объявление | Изменение | Hosting |
|---|---|---|---|---|---|
| `var` | текущий контекст выполнения | можно, значение по умолчанию `undefined` | можно | можно | поднимается |
| `let` | блочная | можно, значение по умолчанию `undefined` | нельзя, `SyntaxError:` | можно | не поднимается, `ReferenceError` |
| `const` | блочная | нельзя | нельзя, `SyntaxError:` | нельзя, если только это не `[]`или `{}` | не поднимается, `ReferenceError` |

## Hosing

> Поднятие или hoisting — это механизм, в котором переменные и объявления функций, передвигаются вверх своей области видимости перед тем, как код будет выполнен.

Тут все довольно просто

```js

// для var все hosting срабатывает
console.log(brandLaptop) // выведет undefined
var brandLaptop = 'honor'

// для let и const hosting вызывает исключение или ошибку
console.log(brandCar) // Cannot access 'brandCar' before initialization"
console.log(brandTable) // Cannot access 'brandTable' before initialization"

const brandTable = 'aka'
let brandCar = 'kia'

```

## Hosting функций

1. Function Declaration - **поднимается**
2. Function Expression - **не поднимается**
3. Named Function Expression - **не поднимается**
4. Arrow function - **не поднимается**
5. Generator function - **поднимается**, если объявлены в Function Declaration
6. new Function - **не поднимается**

## Порядок по приоритетам

> Объявления функций «поднимаются» над объявлением переменных, но не над их назначениями.

**Назначение переменной над объявлением функции:**

```js
var double = 22;

function double(num) {
  return (num*2);
}

console.log(typeof double); // Вывод: number
```

**Объявление функции над объявлением переменной:**

```js
var double;


function double(num) {
  return (num*2);
}

console.log(typeof double); // Вывод: function
```

## Источники данных

- [Статья на habr](https://habr.com/ru/company/ruvds/blog/420359/)
- [Статья на learn.javascript](https://learn.javascript.ru/var)
- [Дока mozilla](https://developer.mozilla.org/ru/docs/Web/JavaScript/Reference/Statements/var)
- [Статья на doka](https://doka.guide/js/var-let/)