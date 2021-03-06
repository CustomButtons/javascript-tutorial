
# Деструктуризация

*Деструктуризация* (destructuring assignment) -- способ присвоить массив или объект сразу нескольким переменным.

## Массив

Пример деструктуризации массива:

```js
'use strict';

let [firstName, lastName] = "Илья Кантор".split(" ");

alert(firstName); // Илья
alert(lastName);  // Кантор
```

При таком присвоении первые два значения массива будут помещены в переменные, а последующие -- будут отброшены.

Однако, можно добавить ещё один параметр, который получит "всё остальное", при помощи троеточия ("spread"):

```js
//+ run
'use strict';

*!*
let [firstName, lastName, ...rest] = "Юлий Цезарь Император Рима".split(" ");
*/!*

alert(firstName); // Юлий
alert(lastName);  // Цезарь
alert(rest);      // Император,Рима (массив из 2х элементов)
```

Значением `rest` будет массив из оставшихся элементов массива.

**Можно задать и значения по умолчанию, если массив почему-то оказался короче, чем ожидалось.**

Они задаются через знак `=`, например:

```js
//+ run
'use strict';

*!*
let [firstName="Гость", lastName="Анонимный"] = [];
*/!*

alert(firstName); // Гость
alert(lastName);  // Анонимный
```

В качестве значений по умолчанию можно использовать не только примитивы, но и выражения, содержащие вызовы функций:

```js
//+ run
'use strict';

function defaultLastName() {
  return Date.now() + '-visitor';
}

*!*
// lastName получит значение, соответствующее текущей дате:
let [firstName, lastName=defaultLastName()] = ["Вася"];
*/!*

alert(firstName); // Вася
alert(lastName);  // 1436...-visitor
```

Заметим, что вызов функции `defaultLastName` будет осуществлён только при необходимости, то есть если значения нет в массиве. 

**Ненужные элементы массива можно отбросить, поставив лишнюю запятую:**

```js
//+ run
'use strict';

*!*
// первый и второй элементы не нужны
let [, , title] = "Юлий Цезарь Император Рима".split(" ");
*/!*

alert(title); // Император
```

В коде выше первый и второй элементы массива никуда не записались, они были отброшены. Как, впрочем, и все элементы после третьего.

## Деструктуризация объекта

Деструктуризация может "мгновенно" разобрать объект по переменным.

Например:

```js
//+ run
'use strict';

let menuOptions = {
  title: "Меню",
  width: 100,
  height: 200
};

*!*
let {title, width, height} = menuOptions;
*/!*

alert(`${title} ${width} ${height}`); // Меню 100 200
```

Как видно, свойства автоматически присваиваются соответствующим переменным.

Если хочется присвоить свойство объекта в переменную с другим именем, можно указать соответствие через двоеточие, вот так:

```js
//+ run
'use strict';

let menuOptions = {
  title: "Меню",
  width: 100,
  height: 200
};

*!*
let {width: w, height: h, title} = menuOptions;
*/!*

alert(`${title} ${w} ${h}`); // Меню 100 200
```

В примере выше свойство `width` отправилось в переменную `w`, свойство `height` -- в переменную `h`, а `title` -- в переменную с тем же названием. 

Если каких-то свойств в объекте нет, можно указать значение по умолчанию через знак равенства `=`, вот так;

```js
//+ run
'use strict';

let menuOptions = {
  title: "Меню"
};

*!*
let {width=100, height=200, title} = menuOptions;
*/!*

alert(`${title} ${width} ${height}`); // Меню 100 200
```

Можно и сочетать одновременно двоеточие и равенство:


```js
//+ run
'use strict';

let menuOptions = {
  title: "Меню"
};

*!*
let {width:w=100, height:h=200, title} = menuOptions;
*/!*

alert(`${title} ${w} ${h}`); // Меню 100 200
```

А что, если в объекте больше значений, чем переменных? Можно ли куда-то присвоить "остаток"?

Такой возможности в текущем стандарте нет. Она планируется в будущем стандарте, и выглядеть она будет аналогично массивам:

```js
//+ run
'use strict';

let menuOptions = {
  title: "Меню",
  width: 100,
  height: 200
};

*!*
let {title, ...size} = menuOptions;
*/!*

// title = "Меню"
// size = { width: 100, height: 200} (остаток)
```

Этот код будет работать, например, при использовании Babel со включёнными экспериментальными возможностями, но ещё раз заметим, что в текущий стандарт такая возможность не вошла.

[smart header="Деструктуризация без объявления"]

В примерах выше переменные объявлялись прямо перед присваиванием. Конечно, можно использовать и уже существующие переменные.

Однако, для объектов есть небольшой "подвох". В JavaScript, если в основном потоке кода (не внутри другого выражения) встречается `{...}`, то это воспринимается как блок.

Например, можно использовать для ограничения видимости переменных:
```js
//+ run
'use strict';
{
  let a = 5;
  alert(a); // 5
}
alert(a); // ошибка нет такой переменной
```

...Но в данном случае это создаст проблему при деструктуризации:

```js
let a, b;
{a, b} = {a:5, b:6}; // будет ошибка, оно посчитает, что {a,b} - блок
```

Чтобы избежать интерпретации `{a, b}` как блока, нужно обернуть всё присваивание в скобки:

```js
let a, b;
({a, b} = {a:5, b:6}); // внутри выражения это уже не блок
```
[/smart]

## Вложенные деструктуризации

Деструктуризации можно как угодно сочетать и вкладывать друг в друга.

Пример с подобъектом и подмассивом:

```js
//+ run
'use strict';

let menuOptions = {
  size: {
    width: 100,
    height: 200
  },
  items: ["Пончик", "Пирожное"]
}

let {
  title="Меню", 
  size: {width, height}, 
  items: [item1, item2]
} = menuOptions;

// Меню 100 200 Пончик Пирожное
alert(`${title} ${width} ${height} ${item1} ${item2}`);
```


## Итого

<ul>
<li>Деструктуризация позволяет разбивать объект или массив на переменные при присвоении.</li>
<li>Синтаксис:
```js
let {prop : varName = default, ...} = object
let [var1=default, var2, ...rest] = array
```
 
Здесь двоеточие `:` задаёт отображение свойства в переменную, а `=` задаёт выражение, которое будет использовано, если значение отсутствует (не указано или `undefined`).

Объявление переменной в начале конструкции не обязательно. Можно использовать и существующие переменные. Однако при деструктуризации объекта может потребоваться обернуть выражение в скобки.
</li>
<li>Вложенные объекты и массивы тоже работают, деструктуризации можно вкладывать друг в друга, сохраняя ту же структуру, что и исходный объект/массив.</li>
</ul>

Как мы увидим далее, деструктуризации особенно пригодятся удобны при чтении объектных параметров функций.






