# Strong Crypto: Write-up

Посмотрим [алгоритм](public/script.py) и узнаем, что в нём есть.

Видим следующие функции:

## Вспомогательные функции

* `letter` — буква по номеру
* `number` — номер по букве

Пока просто не будем обращать на них внимание

## «Шифрующие» функции

### shiftright

Аналог [шифра Цезаря](https://ru.wikipedia.org/wiki/%D0%A8%D0%B8%D1%84%D1%80_%D0%A6%D0%B5%D0%B7%D0%B0%D1%80%D1%8F) по алфавиту `ALPHABET`

Сдвиг производится на `ord(key) % 32`. Из этого можно сделать два вывода:

1. `key` — строка из **одного символа** — очень надежный ключ, правда?
2. Чтобы обратить, нужно просто поменять старый и новый алфавит местами при создании таблицы преобразования:

```python
<     table = str.maketrans(ALPHABET, new_alphabet) # было
>     table = str.maketrans(new_alphabet, ALPHABET) # станет
```

### shifttext

Циклический сдвиг массива вправо на `ord(key) % 32`.

Чтобы обратить, нужно сдвинуть влево на столько же.

### letter_xor

Выглядит как какая-то странная модификация `xor` — мы ксорим номера символов (полученные функцией `number`) с `(ord(key) + ord(key)) % 32`.

Но это всё ещё обычный `xor`! Обратить можно ровно этой же функцией.

### double

Очень странная замена символов.

Если `2 * number(let) >= 32`, т.е. если номер символа в алфавите больше или равен 16, то мы возвращаем `2 * number(let) - 31` (т.е. 16 → 1, 17 → 3, 18 → 5, ..., 31 → 31).

Иначе возвращаем `2 * number(let)` (т.е. 0 → 0, 1 → 2, 2 → 4, .., 15 → 30).

Таким образом, ставим первые 16 символов на четные места, а последние — на нечетные. 

Обратная функция будет проверять четность номера символа:

```python
def half(s):
    result = ""
    for i in s:
        # < if 2 * number(i) >= 32:
        if number(i) % 2 == 1:
            # < result += letter(2 * number(i) - 31)
            result += letter((number(i) + 31) // 2)
        else:
            # < result += letter(2 * number(i))
            result += letter(number(i) // 2)
    return result
```

### Расшифровка

Меняем порядок функций и заменяем функции на обратные:

```python
def decrypt(s, key):
    # < return double(letter_xor(shifttext(shiftletters(s, key), key), key))
    return shiftlettersback(shifttextback(letter_xor(half(s), key), key), key)
```

### Собираем всё

Осталось собрать все функции расшифровки в один файл, и перебрать ключ среди всех символов алфавита.

Мы [уже сделали это](private/solution.py). Запускаем скрипт и видим единственное читаемое слово. Оно и является флагом.

*Мораль*: 1337 последовательно примененных элементарных шифров ничуть не надежнее одного.

Флаг: **DearFriendDontBecomeACryptologist**