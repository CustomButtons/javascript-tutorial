# Подсказка при замедлении над элементом

[importance 5]

Нужно написать функцию, которая показывает подсказку при *наведении* на элемент, но не при *быстром проходе* над ним.

То есть, если посетитель именно навёл курсор мыши на элемент и почти остановился -- подсказку показать, а если быстро провёл над ним, то не надо, зачем излишнее мигание?

Технически -- можно измерять скорость движения мыши над элементом, если она маленькая, то считаем, что это "наведение на элемент" (показать подсказку), если большая -- "быстрый проход мимо элемента" (не показывать).

Реализуйте это через универсальный объект `new HoverIntent(options)`, с параметрами `options`:
<ul>
<li>`elem` -- элемент, наведение на который нужно отслеживать.</li>
<li>`over` -- функция-обработчик наведения на элемент.</li>
<li>`out` -- функция-обработчик ухода с элемента (если было наведение).</li>
</ul>

Пример использования такого объекта для подсказки:
```js
// образец подсказки
var tooltip = document.createElement('div');
tooltip.className = "tooltip";
tooltip.innerHTML = "Подсказка";

// при "наведении на элемент" показать подсказку
new HoverIntent({
  elem: elem,
  over: function() {
    tooltip.style.left = this.getBoundingClientRect().left + 'px';
    tooltip.style.top = this.getBoundingClientRect().bottom + 5 + 'px';
    document.body.appendChild(tooltip);
  },
  out: function() {
    document.body.removeChild(tooltip);
  }
});
```

Демо этого кода:

[iframe src="solution" height=110]

Если провести мышкой над "часиками" быстро, то ничего не будет, а если медленно или остановиться на них, то появится подсказка.

Обратите внимание -- подсказка не "мигает" при проходе мыши внутри "часиков", по подэлементам.


