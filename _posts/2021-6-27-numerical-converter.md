---
layout: post
title: Перевод чисел в разные системы счисления (C#)
comments: true
published: true
---
Как задание из ЕГЭ-2021 вынудило написать конвертер в разные СС.  
Бонус: все возможные конвертации символов и чисел друг в друга.

24 и 25 июня по всей стране проходил ЕГЭ по информатике. Т.к. по основной должности (учитель и зам в общеобразовательной школе) я причастна к данному событию, я не могла проигнорировать появившийся 24 июня вариант "по пересказам" с Дальнего Востока - прорешала, чтобы ученикам было с чем сверяться. Камнем преткновения стал, кто бы мог подумать, 5 номер на обратное воспроизведение предложенного алгоритма: якобы у некоего стримера ответ другой! 

*Разумеется, стример оказался не прав и после указания на ошибку в комментариях на youtube принёс извинения.*

Непосредственно задание:

![]({{site.baseurl}}/images/ege5.png)

Нативно задание решается следующим образом: следует взять число, большее 52 - т.е. 53, перевести его в двоичную систему счисления, проверить, могло ли оно быть составлено по данному алгоритму, если нет, берём следующее (переводить уже не надо, следующее смотрим в двоичном представлении).

Для точности эксперимента было решено написать программу. Изначально она реализована на Pascal, платформа PascalABC.Net, для простоты чтения учащимся, ибо в нашей ОО это основной язык программирования. Здесь было решено пойти иным путём: перебрать числа от 1 до 31 как исходные, добавить им соответствующие разряды в соответствии с чётностью, и посмотреть, какое будет наименьшим.

Исходный код [тут](https://github.com/deadmadara/NumericalSystemConverter/blob/main/ege-example.pas). Соответственно, требовалось две функции перевода и добавление разрядов. 

* **Перевод из 10-й в 2-ю СС**: на вход подаётся десятичное число, на выходе число в двоичной системе в **строковом** представлении (*прим.: почему так? всё просто - число 1024 в двоичном виде занимает аж 11 разрядов, если хранить эти числа в формате integer, может не хватить памяти в ячейке*). Для этого, как и делается в жизни, берём остатки после деления на 2, заносим их в строку. Приятно, что в Pascal перевод из integer в string осуществляется одним движением *IntToStr()*. Важный момент: эти остатки расположены в *обратном* порядке, поэтому строку необходимо перевернуть - для этого проходим до середины массива, через локальную переменную меняя первый и последний необработанный элемент.

* **Перевод из 2-й в 10-ю СС**: на вход подаётся двоичная "строка", на выходе десятичное число. В жизни берётся первая необоработанная цифра числа, умножается на основание СС в степени разряда цифры. Тут то же самое: получаем макс. степень, берём символ из строки, переводим его в число благодаря *IntToStr()*, умножаем на степень, после степень уменьшаем (*прим.: да, получение макс. степени избыточно, формально можно брать последнюю цифру и постепенно увеличивать степень*). 

Программа, как и нативное решение, выдаёт результат 56. Это правильный ответ.

После этого возникла идея переписать конвертер на C#, заодно расширив его - добавить возможность **выбирать основание СС**, а главное выйти за рамки "циферных" СС, ведь есть 16-я система, где для представления используются **символы**.

Исходный код [тут](https://github.com/deadmadara/NumericalSystemConverter/blob/main/NumericalSystemHandler.cs).

При разработке возникли три проблемы:

1) *В C# невозможно вынести методы из класса.* Однако можно создать объявить методы статическими (static) и создавать экземпляр класса для работы с ними не потребуется. 

2) *Перевод числа в символ / строку, перевод кода из ASCII в символ и обратно.* Это целая головная боль, которая решается исключительно посещением одного известного форума. Итак:

* Перевод числа в строку, **5 -> "5"**: num.ToString()
 
* Перевод строки в число, **"5" -> 5**: Convert.ToInt32(str)

* Перевод числа в символ, **5 -> '5'**: Convert.ToChar(num + 55)

* Перевод символа в число, **'5' -> 5**: (int)Char.GetNumericValue(ch)

* Перевод кода в символ, **65 -> 'A'**: Convert.ToChar(num)

* Перевод символа в код, **'A' -> 65**: Convert.ToInt32(ch)

3) *Расширение до СС с символами.* Вариантов решения проблемы два:

* Использовать словарь. Причём необязательно брать структуру словарь, достаточно использовать символьный массив, где 0й элемент - отображение 10, 1й - 11 и т.д. В таком случае необходимо в метод или в конструктор передавать данный массив.

* Использовать коды символов. Получать код символа и вычитать смещение (*прим:. в случае ASCII составляет 55, т.е. 'A' с кодом 65 должна представлять 10*). В таком случае разрядность ограничивается до 32, т.к. после заглавных латинских букв идут спец. символы, а также нельзя использовать свои символы.

В реализованном проекте был выбран вариант с кодами символов для упрощения. В идеале, конечно, следует реализовывать первый вариант, так как не будет привязки к кодировке.
