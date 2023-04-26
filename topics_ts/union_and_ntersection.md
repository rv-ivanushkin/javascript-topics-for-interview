# Что такое `Union` и `Intersection`?

## Содержание

- [Что такое `Union`?](#что-такое-union)
- [Что такое `Intersection`?](#что-такое-intersection)

## Что такое `Union`?

> Объединение (Union) - принадлежность данных к одному из перечисленных типов

Иными словами - имея перечень некоторых типов, например - `A`, `B`, `C`, `D` можно определить тип некоторой переменной таким образом, что она (переменная) может принимать одно значение из `A`, `B`, `C`, `D`

Для полного понимания `union` можно посмотреть на пример ниже

```ts
type Qualification = 'senior' | 'middle' | 'junior'

/** уровень владения иностранным языком */ 
type LanguageSkill = 'A1' | 'A2' | 'B1' | 'B2' | 'C1' | 'C2'

type UserInfo = {
  level: Qualification
  languageLevel: LanguageSkill
}

const Roman: UserInfo = {
  level: "senior",
  languageLevel: "B2"
}

```

`unions` используется для того чтобы выбрать один из типов перечисленных ранее.

Т.е. показать значения, которые могут быть, но может быть только одно из значений

А если использовать вот такой пример

```ts

type A = {
  name: string
}
type B = {
  age: number
}

type C = A | B

const c: C = {
    name: 'string',
    age: 10
}
```

то в таком случае, тип `С` предполагает, что возможен использовать ключи как из `A` так и из `B`

Т.е. тип `C`

- может иметь только ключи из `A`
- может иметь только ключи из `B`
- может как ключи из `A` так и из `B` одновременно

## Что такое `Intersection`?

> Пересечение (Intersection) - принадлежность данных к единому типу из всех перечисленных

Иными словами - имея перечень некоторых типов, например - `A`, `B`, `C`, `D` можно определить тип некоторой переменной таким образом, что она (переменная) должна принимать все значение из `A`, `B`, `C`, `D`

Для полного понимания `intersection` можно посмотреть на пример ниже

```ts
type UserRegistered = {
  name: string
  id: string
}

type UserInStaff = {
  department: 'IT' | 'Support' | 'CallCenter'
}

type User = UserRegistered & UserInStaff

const Roman: User = {
  name: "Roman"
  id: "1",
  department: "IT"
}
```

Как видно из примера, что самое главное это понимание зачем нужны `intersection` и нужны они для создание нового типа, который должен включать в себя все свойства из всех перечисленных типов.

Если взять пример из `union`

```ts

type A = {
  name: string
}
type B = {
  age: number
}

type C = A & B

// Property 'age' is missing in type '{ name: string; }' but required in type 'B'.
const c: C = {
    name: 'string',
}
```

то тут тип `C` должен иметь ключи как из `A` так и из `B`, т.е. обязательное условие - наличие всех ключей из `A` и `B`

## Источники данных

- [Union Types](https://www.typescriptlang.org/docs/handbook/2/everyday-types.html#union-types)