
# Set, Map, WeakSet и WeakMap

Новые типы коллекций в JavaScript: `Set`, `Map`, `WeakSet` и `WeakMap`.


## Map

`Map` -- коллекция для хранения записей вида `ключ: значение`.

В отличие от объектов, в которых ключами могут быть только строки, в `Map` ключом может быть произвольное значение, например:

```js
//+ run
'use strict';

let map = new Map();

map.set('1', 'str1');   // строка
map
  .set(1, 'num1')       // число
  .set(true, 'bool1');  // булевое

// в обычном объекте это было бы одно и то же
alert( map.get(1)   ); // 'num1'
alert( map.get('1') ); // 'str1'

alert( map.size ); // 3
```

Как видно из примера выше, для сохранения и чтения значений используются методы `get` и `set`, причём `set` можно чейнить. И ключи и значения сохраняются "как есть", без преобразований типов.

Свойство `map.size` хранит общее количество записей в `map`.

**При создании `Map` можно сразу инициализовать списком значений.**

Объект `map` с тремя ключами, как и в примере выше:

```js
let map = new Map([
  ['1',  'str1'],
  [1,    'num1'],
  [true, 'bool1']
]);
```

Аргументом `new Map` должен быть итерируемый объект (не обязательно именно массив), которые должен возвратить объект с ключами `0`,`1` -- также не обязательно массив. Везде утиная типизация, максимальная гибкость.

**В качестве ключей можно использовать и объекты:**

```js
//+ run
'use strict';

let user = { name: "Вася" };

let visitsCountMap = new Map();

*!*
// объект user является ключом в visitsCountMap
visitsCountMap.set(user, 123);
*/!*

alert( visitsCountMap.get(user) ); // 123
```

[smart header="Как map сравнивает ключи"]
Для проверки значений на эквивалентность используется алгоритм [SameValueZero](http://www.ecma-international.org/ecma-262/6.0/index.html#sec-samevaluezero). Он аналогичен строгому равенству `===`, отличие -- в том, что `NaN` считается равным `NaN`.

Этот алгоритм жёстко фиксирован в стандарте, его нельзя изменять или задавать свою функцию для него.
[/smart]

Для удаления записей:
<ul>
<li>`map.delete(key)` удаляет запись с ключом `key`, возвращает `true`, если такая запись была, иначе `false`.</li>
<li>`map.clear()` -- удаляет все записи, очищает `map`.</li>
</ul>

Для проверки существования ключа:

<ul>
<li>`map.has(key)` -- возвращает `true`, если ключ есть, иначе `false`.</li>
</ul>

Ещё раз заметим, что используемый алгоритм сравнения ключей аналогичен `===`, за исключением `NaN`, которое равно самому себе:

```js
//+ run
'use strict';

let map = new Map([ [NaN: 1] ]);

alert( map.has(NaN) ); // true
alert( map.get(NaN) ); // 1
alert( map.delete(NaN) ); // true
```

### Итерация 

Для итерации используется один из трёх методов:
<ul>
<li>`map.keys()` -- возвращает итерируемый объект для ключей,</li>
<li>`map.values()` -- возвращает итерируемый объект для значений,</li>
<li>`map.entries()` -- возвращает итерируемый объект для записей `[ключ, значение]`, он используется по умолчанию в `for..of`.</li>
</ul>

Например:

```js
//+ run
'use strict';

let recipeMap = new Map([
  ['огурцов',   '500 гр'],
  ['помидоров', '350 гр'],
  ['сметаны',   '50 гр']
]);

for(let fruit of recipeMap.keys()) {
  alert(fruit); // огурцов, помидоров, сметаны
} 

for(let amount of recipeMap.values()) {
  alert(amount); // 500 гр, 350 гр, 50 гр
} 

for(let entry of recipeMap) { // то же что и recipeMap.entries()
  alert(entry); // огурцов,500 гр , и т.д., массивы по 2 значения
} 
```

[smart header="Перебор идёт в том же порядке, что и вставка"]
Перебор осуществляется в порядке вставки. Объекты `Map` гарантируют это, в отличие от обычных объектов `Object`.
[/smart]

Кроме того, у `Map` есть стандартный методы `forEach`, аналогичный массиву:

```js
//+ run
'use strict';

let recipeMap = new Map([
  ['огурцов',   '500 гр'],
  ['помидоров', '350 гр'],
  ['сметаны',   '50 гр']
]);

recipeMap.forEach( (value, key, map) => {
  alert(`${key}: ${value}`); // огурцов: 500 гр, и т.д.
});
```


У `Map` есть и другие свой методы:

<ul>
<li>`map.size()` -- возвращает количество записей,</li>
<li>`map.clear()` -- удаляет все записи.</li>
</ul>

## Set

`Set` -- коллекция для хранения множества значений, причём каждое значение может встречаться лишь один раз.

Например, к нам приходят посетители, и мы хотели бы сохранять всех, кто пришёл. Повторные визиты не должны приводить к дубликатам.

`Set` для этого отлично подходит:

```js
//+ run
'use strict';

let set = new Set();

let vasya = {name: "Вася"};
let petya = {name: "Петя"};
let dasha = {name: "Даша"};

// посещения
set.add(vasya);
set.add(petya);
set.add(dasha);
set.add(vasya);
set.add(petya);

alert( set.size ); // 3

set.forEach( user => alert(user.name ) ); // Вася, Петя, Даша
```

В примере выше многократные добавления одного и того же объекта в `set` не создают лишних копий.

Альтернатива `Set` -- это массивы с поиском дубликата при каждом добавлении, но это гораздо хуже по производительности. Или же объекты, где в качестве ключа выступает какой-нибудь уникальный идентификатор посетителя. Но это менее удобно, чем простой и наглядный `Set`.

Основные методы:
<ul>
<li>`set.add(item)` -- добавляет в коллекцию `item`, возвращает `set` (чейнится).</li>
<li>`set.delete(item) -- удаляет `item` из коллекции, возвращает `true`, если он там был, иначе `false`.</li>
<li>`set.has(item)` -- возвращает `true`, если `item` есть в коллекции, иначе `false`.</li>
<li>`set.clear()` -- очищает `set`.</li>
</ul>

Перебор `Set` осуществляется через `forEach` или `for..of` аналогично `Map`:

```js
//+ run
'use strict';

let set = new Set(["апельсины", "яблоки", "бананы"]);

// то же, что: for(let value of set)
set.forEach((value, valueAgain, set) => {
  alert(value); // апельсины, затем яблоки, затем бананы
}); 
```

Заметим, что в `Map` у функции в `.forEach` три аргумента: ключ, значение, объект `map`.

В `Set` для совместимости с `Map` сделано похожим образом -- у `.forEach`-функции также три аргумента. Но первые два всегда совпадают и содержат очередное значение множества. 

## WeakMap и WeakSet

`WeakSet` -- особый вид `Set` не препятствующий сборщику мусора. То же самое -- `WeakMap` для `Map`.

То есть, если некий объект присутствует только в `WeakSet/WeakMap` -- он удаляется из памяти.

Это нужно для тех ситуаций, когда сами объекты используются где-то в другом месте кода, а здесь мы хотим хранить для них "вспомогательные" данные, существующие лишь пока жив объект.

Например, у нас есть элементы на странице или, к примеру, пользователи, и мы хотим хранить для них вспомогательную инфомацию, например обработчики событий или просто данные, но действительные лишь пока объект, к которому они относятся, существует.

Если поместить такие данные в `WeakMap`, а объект сделать ключом, то они будут автоматически удалены из памяти, когда удалится элемент. 

Например:

```js
// текущие активные пользователи
let activeUsers = [
  {name: "Вася"}, 
  {name: "Петя"},
  {name: "Маша"}
];

// вспомогательная информация о них, 
// которая напрямую не входит в объект юзера,
// и потому хранится отдельно
let weakMap = new WeakMap();

weakMap[activeUsers[0]] = 1;
weakMap[activeUsers[1]] = 2;
weakMap[activeUsers[2]] = 3;

alert( weakMap[activeUsers[0]] ); // 1

activeUsers.splice(0, 1); // Вася более не активный пользователь

// weakMap теперь содержит только 2 элемента

activeUsers.splice(0, 1); // Петя более не активный пользователь

// weakMap теперь содержит только 1 элемент
```

У WeakMap есть ряд ограничений:
<ul>
<li>Нет свойства `size`.</li>
<li>Нельзя перебрать элементы итератором или `forEach`.</li>
<li>Нет метода `clear()`.</li>
</ul>

Иными словами, `WeakMap` работает только на запись (`set`, `delete`) и чтение (`get`, `has`) элементов по конкретному ключу, а не как полноценная коллекция. Нельзя вывести всё содержимое `WeakMap`, нет соответствующих методов.

Это связано с тем, что содержимое `WeakMap` может быть модифицировано сборщиком мусора в любой момент, независимо от программиста. Сборщик мусора работает сам по себе. Он не гарантирует, что очистит объект сразу же, когда это стало возможным. Нет какого-то конкретного момента, когда такая очистка точно произойдёт -- это определяется внутренними алгоритмами сборщика и его сведениями о системе.

Поэтому содержимое `WeakMap` в произвольный момент, строго говоря, не определено. Может быть, сборщик мусора уже удалил какие-то записи, а может и нет. С этим, а также с требованиями к эффективной реализации `WeakMap`, и связано отсутствие методов, осуществляющих доступ ко всем записям.

То же самое относится и к `WeakSet`: можно добавлять элементы, проверять их наличие, но нельзя получить их список и даже узнать количество.

Эти ограничения могут показаться неудобными, но по сути они не мешают `WeakMap/WeakSet` выполнять свою основную задачу -- быть "вторичным" хранилищем данных для объектов, актуальный список которых (и сами они) хранятся в каком-то другом месте.

## Итого

<ul>
<li>`Map` -- коллекция записей вида `ключ: значение`, лучше `Object` тем, что перебирает всегда в порядке вставки и допускает любые ключи.</li>
<li>`Set` -- коллекция уникальных элементов, также допускает любые ключи.
<li>`WeakMap` и `WeakSet` -- "урезанные" по функционалу варианты `Map/Set`, которые позволяют только "точечно" обращаться элементам (по конкретному ключу или значению). Они не препятствуют сборке мусора, то есть если ссылка на объект осталась только в `WeakSet/WeakMap` -- он будет удалён.</li>
</ul>





















