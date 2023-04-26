# Как происходит приведение типов?

## Содержание

- [Какие виды приведения типов бывают?](#какие-виды-приведения-типов-бывают)
- [Когда происходит "Implicit Conversion" или автоматическое приведение типов?](#когда-происходит-implicit-conversion-или-автоматическое-приведение-типов)
- [Приведение к числу](#приведение-к-числу)
- [Приведение к строке](#приведение-к-строке)
- [Приведение к булеву значению](#приведение-к-булеву-значению)
- [Как производится приведение для объектов?](#как-производится-приведение-для-объектов)
- [Ассоциативность](#ассоциативность)
- [Источники данных](#источники-данных)

## Какие виды приведения типов бывают?

- Implicit Conversion - автоматическое приведение типов
- Explicit Conversion - ручное приведение типов

## Когда происходит "Implicit Conversion" или автоматическое приведение типов?

автоматическое приведение происходит в основном при использовании разных операторов и если их просто перечислить, то тогда не будет понятно в какой примитив должен приведен то или иной операнд.

исходя из это проще сгруппировать приведение по типам примитивам

- приведение к числу `number`
- приведение к строке `string`
- приведение к булеву значению `boolean`

### Приведение к числу

автоматическое приведение в числу `number` происходит, когда используются следующие операторы:

- операторы сравнения (>, <, <=, >=)
- побитовые операторы (|, &, ^, ~)
- арифметические операторы (-, +, *, /, %)
- унарный оператор +
- оператор нестрогого равенства == (а также !=)

Особенность **№1**

```markdown
- оператор `==` (а также `!=`) не приводит операнды к числу, если оба операнда являются **строками**.
- `null` преобразуется в 0
- `undefined` превращается в NaN
```

Особенность **№2**

```markdown
оператор `+` не приводит операнды к числу, если хотя бы один оператор является **строкой**
```

Особенность **№3**

```markdown
Symbol не могут быть преобразованы в число ни явно, ни неявно. при попытке такого преобразования выдаётся ошибка **TypeError**
```

Явное приведение

```js
Number('123')           // 123
Number('123.4')        // 123.4
Number('123,4')        // NaN
Number('true')         // NaN
Number('false')         // NaN
Number('')             // 0
Number(null)           // 0
Number(undefined)      // NaN
Number(true)           // 1
Number(false)          // 0
Number(function () {}) // NaN
Number({})             // NaN
Number([])             // 0,   Внимание!, Number от пустого массива — 0,
Number([1])            // 1,   Внимание!, Number от массива с одним числом — это число
Number([1, 2])         // NaN, Внимание!, Number от массива с несколькими числами — NaN.
```

### Приведение к строке


автоматическое приведение в строке `string` происходит, когда используются следующие операторы:

- оператор `+`, если хотя бы один из операндов является строкой

Явное приведение

```js
String(123)                 // '123'
String(-12.3)               // '-12.3'
String(null)                // 'null'
String(undefined)           // 'undefined'
String(true)                // 'true'
String(false)               // 'false'
String(function () {})      // 'function () {}'
String({})                  // '[object Object]'
String({ key: 42 })         // '[object Object]'
String([])                  // ''
String([1, 2])              // '1,2'
String(Symbol('my symbol')) // 'Symbol(my symbol)'
```

### Приведение к булеву значению


автоматическое приведение в булеву значению `boolean` происходит, когда используются следующие операторы:

- логические операторы `&&` или `||` или `!`
- логическом контексте `if (условие) { ... }`
- тернарном контексте `условие ? true : false`

Особенность **№1**

```markdown
при использовании `expr1 && expr2` - Возвращает операнд expr1, если он может быть преобразован в false; в противном случае возвращает операнд expr2
```

Примеры
```js
var a1 =  true && true;     // t && t возвращает true
var a2 =  true && false;    // t && f возвращает false
var a3 = false && true;     // f && t возвращает false
var a4 = false && (3 == 4); // f && f возвращает false
var a5 = "Cat" && "Dog";    // t && t возвращает Dog
var a6 = false && "Cat";    // f && t возвращает false
var a7 = "Cat" && false;    // t && f возвращает false
```

```markdown
при использовании `expr1 || expr2` - Возвращает операнд expr1, если он может быть преобразован в true; в противном случае возвращает операнд expr2
```

Примеры
```js
var o1 =  true || true;     // t || t возвращает true
var o2 = false || true;     // f || t возвращает true
var o3 =  true || false;    // t || f возвращает true
var o4 = false || (3 == 4); // f || f возвращает false
var o5 = "Cat" || "Dog";    // t || t возвращает Cat
var o6 = false || "Cat";    // f || t возвращает Cat
var o7 = "Cat" || false;    // t || f возвращает Cat
```

```markdown
при использовании `!expr` - Возвращает false, если операнд может быть преобразован в true; в противном случае возвращает true.
```

Примеры
```js
var n1 = !true;  // !t возвращает false
var n2 = !false; // !f возвращает true
var n3 = !"Cat"; // !t возвращает false
```

Особенность **№2**
```markdown
- NaN не равен ничему и даже самому себе, при использовании `==` или `!=`
```

Явное приведение

```js
Boolean('string')            // true
Boolean('false')             // true
Boolean(42)                  // true
Boolean(-42)                 // true
Boolean(function () {})      // true
Boolean({})                  // true
Boolean({ key: 42 })         // true
Boolean([])                  // true
Boolean([1, 2])              // true
Boolean(Symbol('my symbol')) // true

Boolean('')                 // false
Boolean(0)                  // false
Boolean(NaN)                // false
Boolean(null)               // false
Boolean(undefined)          // false

// Грубо говоря, всё, кроме пустой строки, нуля,
// NaN, null и undefined — true.
```

Примеры условного контекст

```js
true      if (true) { /* executes */ }
false     if (false) { /* does not execute */ }
1         if (1) { /* executes */ }
0         if (0) { /* does not execute */ }
-1        if (-1) { /* executes */ }
"true"    if ("true") { /* executes */ }
"false"   if ("false") { /* executes */ }
"1"       if ("1") { /* executes */ }
"0"       if ("0") { /* executes */ }
"-1"      if ("-1") { /* executes */ }
""        if ("") { /* does not execute */ }
null      if (null) { /* does not execute */ }
undefined if (undefined) { /* does not execute */ }
Infinity  if (Infinity) { /* executes */ }
-Infinity if (-Infinity) { /* executes */ }
[]        if ([]) { /* executes */ }
{}        if ({}) { /* executes */ }
[[]]      if ([[]]) { /* executes */ }
[0]       if ([0]) { /* executes */ }
[1]       if ([1]) { /* executes */ }
NaN       if (NaN) { /* does not execute */ }
```

## Как производится приведение для объектов?

В целом, работа алгоритма выглядит следующим образом:

1. Если входное значение является примитивом — не делать ничего и вернуть его.
1. Вызвать `input.toString()`, если результат является значением примитивного типа — вернуть его.
1. Вызвать `input.valueOf()`, если результат является значением примитивного типа — вернуть его.
1. Если ни `input.toString()`, ни `input.valueOf()` не дают примитивное значение — выдать ошибку `TypeError`

## Ассоциативность

Ассоциативность определяет порядок, в котором обрабатываются операторы с одинаковым приоритетом.

[Операторы упорядочены с самого высокого (19) до самого низкого (1) приоритета.](https://developer.mozilla.org/ru/docs/Web/JavaScript/Reference/Operators/Operator_Precedence)

## Источники данных

- [JavaScript-Equality-Table](https://dorey.github.io/JavaScript-Equality-Table/)
- [Преобразование типов](https://doka.guide/js/typecasting/)
- [Приоритет операторов](https://developer.mozilla.org/ru/docs/Web/JavaScript/Reference/Operators/Operator_Precedence)
- [Что такое преобразование типов?](https://www.cat-in-web.ru/type-coercion/)