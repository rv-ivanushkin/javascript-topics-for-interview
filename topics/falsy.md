# Какие значения в JavaScript являются Falsy?

## Содержание

- [Полный список ложноподобных значений](#полный-список-ложноподобных-значений)
- [Источники данных](#источники-данных)

## Полный список ложноподобных значений

| Значение    | Описание |
| ----------- | ----------- |
| false      | Ключевое слово false |
| 0   | Ноль Number (к нему также относятся 0.0, 0x0 и т.д.) |
| -0   | Отрицательный ноль типа Number (к нему также относятся -0.0, -0x0 и т.д.) |
| 0n   | Ноль типа BigInt (также 0x0n). Обратите внимание, что не может быть негативного нуля типа BigInt — отрицательный 0n равняется 0n |
| "", '', ``   | Значение, содержащее пустую строку |
| null   | null — отсутствие какого-либо значения |
| undefined   | undefined — примитивное значение |
| NaN   | NaN — значение, не являющиеся числом |
| document.all   | Объекты считаются ложноподобными тогда и только тогда, когда у них есть внутренний слот [[IsHTMLDDA]]. Этот слот есть только в объекте document.all, и его нельзя задать через JavaScript |

## Источники данных

- [Ложноподобное значение](https://developer.mozilla.org/ru/docs/Glossary/Falsy)