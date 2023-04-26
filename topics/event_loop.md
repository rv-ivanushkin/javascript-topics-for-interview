
# Что такое event-loop и как он работает?

## Содержание

- [Call stack или Стек вызовов](#call-stack-или-стек-вызовов)
- [Что такое очередь?](#что-такое-очередь)
- [Что такое стек?](#что-такое-стек)
- [В чем различие между микрозадачи и макрозадачи?](#в-чем-различие-между-микрозадачи-и-макрозадачи)
- [Откуда берутся макрозадачи?](#откуда-берутся-макрозадачи)
- [Откуда берутся микрозадачи?](#откуда-берутся-микрозадачи)
- [Event loop](#event-loop)
- [Примеры](#примеры)
- [Источники данных](#источники-данных)

## Call stack или Стек вызовов

Первое определение

> Стек вызовов — механизм, с помощью которого интерпретатор JavaScript определяет, какая функция выполняется в настоящее время, какие функции вызываются из этой функции и т.д.

Чуть более развернутое определение

> Стек вызовов (call stack) - это механизм для интерпретаторов (таких как интерпретатор JavaScript в веб-браузере) для отслеживания текущего местонахождения интерпретатора в скрипте, который вызывает несколько функций, — какая из функций выполняется на данный момент, какие функции вызываются изнутри этой (выполняемой) функции, какая будет вызвана следующей и т. д.

Еще одно определение и оно очень интересное

> Стек вызовов - это стек LIFO (Last In First Out). Это место, куда задача добавляется из очереди макрозадач или очереди микрозадач.

Здесь появились два термина - макрозадач и микрозадач

### Что такое очередь?

> Очередь - список элементов, организованных по принципу FIFO (англ. first in, first out «первым пришёл — первым ушёл») 

### Что такое стек?

> Стек - список элементов, организованных по принципу LIFO (англ. last in — first out, «последним пришёл — первым вышел»)

Получается, что у стека есть две операции

- PUSH, положить элемент в конец
- POP, взять последний элемент

Очень важным момент здесь является то, что call stack - механизм интерпретатора (к примеру **V8**) в **веб-браузере**.

### В чем различие между микрозадачи и макрозадачи?

Разница между макро- и микрозадачами кажется незначительной. Все они помещаются в **стек вызовов** и запускаются в соответствующее время. Event loop извлекает задачу из стека вызовов одну за другой.

Но разница в приоритете, а именно:

> Event loop выполняет все **микро**задачи одну за другой, даже когда одна **микро**задача запланировала другую **микро**задачу, в то время как **макра**задачи выполняются по одной.

Теперь понятно, что есть просто задачи, которые добавляются в сек вызовов, но задачи могу быть разного типа (**микро** и **макро**)

### Откуда берутся макрозадачи?

- setTimeout() - известно, что это =)
- setImmediate() - Этот метод используется для прерывания длительно выполняющихся операций и запуска функции обратного вызова сразу после завершения браузером других операций, таких как события и обновления отображения.
- setInterval() - известно, что это =)
- requestAnimationFrame() - указывает браузеру на то, что вы хотите произвести анимацию, и просит его запланировать перерисовку на следующем кадре анимации. В качестве параметра метод получает функцию, которая будет вызвана перед перерисовкой.
- I/O
- UI rendering

### Откуда берутся микрозадачи?

Очень хорошее определение

> Макрозадача - это любой код JavaScript, выполнение которого запланировано с помощью стандартного механизма, такого как обратный вызов события, интервал или тайм-аут.

- Promise (then/catch/finally), await
- process.nextTick() - для Node
- queueMicrotask() - явно поместит задачу в очередь микрозадач.

## Event loop

И теперь самое главное

> Event loop - это бесконечный цикл, которые следит за `стеком вызовов` и выполняет все, что туда помещено по приципу «последним пришёл — первым вышел», а так же за один `проход` или `такт` выполняет все **микро**задачи а потом одну **макру**задачу

Как видно из определения выше, то вся суть заключается именно в типах задач.

Например, если не правильно реализовать рекурсивную функцию (в частности **микро**задачи), то стек вызов будет заполнены, а браузер не сможет выполнять ничего другого, пока не будут выполнены все **микро**задачи

## Примеры

<details>
  <summary>Пример #1, простой</summary>

```js
  setTimeout(() => console.log('Таймаут'), 0);

  let promise = new Promise((resolve, reject) => {
    console.log('Создание промиса');
    resolve()
  })

  promise.then(() => console.log('Обработка промиса'));

  console.log('Конец скрипта');
```

Решение:

1. Макрозадача `setTimeout(() => console.log('Таймаут'), 0)` в очередь макрозадач (в очереди нет пока никого и задача первая)
1. Микрозадача, но внутри нее основной поток, ничего сложного, выводим `console.log('Создание промиса')` и в очередь микрозадач (в очереди нет пока никого и задача первая)
1. Микрозадача `() => console.log('Обработка промиса')` в очередь микрозадач (сейчас там пункт `2`)
1. Основной поток, ничего сложного, выводим `console.log('Конец скрипта')`

Вывод:

```js
output: 'Создание промиса'  // основной поток
output: 'Конец скрипта'     // основной поток
output: 'Обработка промиса' // микрозадача
output: 'Таймаут'           // макрозадача
```

</details>


<details>
  <summary>Пример #2, сложный</summary>

```js
console.log(1);

setTimeout(() => console.log(2));

Promise.resolve().then(() => console.log(3));

Promise.resolve().then(() => setTimeout(() => console.log(4)));

Promise.resolve().then(() => console.log(5));

setTimeout(() => console.log(6));

console.log(7);
```

1. Основной поток, ничего сложного, выводим `console.log(1)`
1. Макрозадача `setTimeout(() => console.log(2))` в очередь макрозадач (в очереди нет пока никого и задача первая)
1. Микрозадача `console.log(3)` помещаем в очередь (в очереди нет пока никого и задача первая)
1. Микрозадача `setTimeout(() => console.log(4))` в которой лежит макрозадача. Тут главное не переживать и помнить, что когда очередь дойдет до этой задачи, то мы просто поместим в конец макрозадач эту задачу
1. Микрозадача `console.log(5)` помечаем в очередь (сейчас там пункты `3`, `4`)
1. Макрозадача `setTimeout(() => console.log(6))` в очередь (сейчас там пункты `1`, в самом конце `4`)
1. Основной поток, ничего сложного, выводим `console.log(7)`

Вывод:

```js
output: '1' // основной поток
output: '7' // основной поток
output: '3' // микрозадача
output: '5' // микрозадача
output: '2' // макрозадача
output: '6' // макрозадача
output: '4' // создание макрозадачи внутри микрозадачи
```

</details>

<details>
  <summary>Пример #3, сложный</summary>

```js
console.log(1);

setTimeout(() => console.log(2));

Promise.reject(3).catch(console.log);

new Promise(resolve => setTimeout(resolve)).then(() => console.log(4));

Promise.resolve(5).then(console.log);

console.log(6);

setTimeout(() => console.log(7),0);

```

Здесь все как в задаче выше, но стоить заметить, что
> new Promise(resolve => setTimeout(resolve)).then(() => console.log(4))
Макрозадача порождает микрозадачу, а это значит, что теперь выполнение макрозадач завершается пока не выполнятся все микрозадачи.

Поэтому после `2` идет `4` а потом уже `7`

Вывод:

```js
output: '1' // основной поток
output: '6' // основной поток
output: '3' // микрозадача
output: '5' // микрозадача
output: '2' // макрозадача
output: '4' // микрозадача
output: '7' // макрозадача
```

</details>

<details>
  <summary>Пример #4, на самопроверку</summary>

```js
const myPromise = (delay) => new Promise((res, rej) => { setTimeout(res, delay) })

setTimeout(() => console.log('in setTimeout1'), 1000);
myPromise(1000).then(res => console.log('in Promise 1'));
setTimeout(() => console.log('in setTimeout2'), 100);
myPromise(2000).then(res => console.log('in Promise 2')); 
setTimeout(() => console.log('in setTimeout3'), 2000);
myPromise(1000).then(res => console.log('in Promise 3'));
setTimeout(() => console.log('in setTimeout4'), 1000);
myPromise(5000).then(res => console.log('in Promise '));

```

Здесь без таблички мне сложно в голове все смоделировать

Начнем с того, что у нас есть очередь WEB.API

Давайте ее для начала заполним

- () => console.log('in setTimeout1'), 1000
- promise(setTimeout), 1000
- () => console.log('in setTimeout2'), 100
- promise(setTimeout), 2000
- () => console.log('in setTimeout3'), 2000
- promise(setTimeout), 1000
- () => console.log('in setTimeout4'), 1000
- promise(setTimeout), 5000

теперь все задачи зарегистрированы и теперь становится ясно, что тут все дело в таймере, у кого таймер истек, то идет в очередь выполнения

Первый таймер - 100, а там макротаска - console.log('in setTimeout2'), до следующей задачи 900 миллисекунд

Вывод
```js
output: 'in setTimeout2'
output: 'in setTimeout1'
output: 'in Promise 1'
output: 'in Promise 3'
output: 'in setTimeout4'
output: 'in Promise 2'
output: 'in setTimeout3'
output: 'in Promise'
```

</details>

## Источники данных

- [Как работают браузеры. Часть 2: парсинг и выполнение JS](https://habr.com/ru/company/kts/blog/678034/)
- [Event loop на medium](https://medium.com/globant/what-are-micro-tasks-and-macro-tasks-in-the-event-loop-29bc0abdd445)