`JSON.stringify` для преобразования объектов в JSON

```js
let student = {
  name: 'John',
  age: 30,
  isAdmin: false,
  courses: ['html', 'css', 'js'],
  wife: null
};

let json = JSON.stringify(student);
alert(typeof json); //string
alert(json);
```

Полученная строка `json` называется _JSON-форматированным_ или _сериализованным_ объектом. `JSON.stringify` может быть применён и к примитивам.
JSON поддерживает следующие типы данных:
- Объекты `{ ... }`
- Массивы `[ ... ]`
- Примитивы:
    - строки,
    - числа,
    - логические значения `true/false`,
    - `null`.
```js
// число в JSON остаётся числом
console.log(JSON.stringify(1)); // 1
// строка в JSON по-прежнему остаётся строкой, но в двойных кавычках
console.log(JSON.stringify("test")); // "test"
console.log(JSON.stringify(true)); // true
console.log(JSON.stringify([1, 2, 3])); // [1,2,3]
```

JSON является независимой от языка спецификацией для данных, поэтому `JSON.stringify` пропускает некоторые специфические свойства объектов JavaScript.
А именно:
- Свойства-функции (методы).
- Символьные ключи и значения.
- Свойства, содержащие `undefined`.
  
```js
let user = {
  sayHi() {
    // будет пропущено
    alert("Hello");
  },
  [Symbol("id")]: 123, // также будет пропущено
  something: undefined, // как и это - пропущено
};

alert(JSON.stringify(user)); // {} (пустой объект)
```

Не должно быть циклических ссылок
```js
let room = {
  number: 23,
};

let meetup = {
  title: "Conference",
  participants: ["john", "ann"],
};

meetup.place = room; // meetup ссылается на room
room.occupiedBy = meetup; // room ссылается на meetup
JSON.stringify(meetup); // Ошибка: Преобразование цикличной структуры в JSON
```

```js
let json = JSON.stringify(value[, replacer, space])
```

value - Значение для преобразования в строку JSON.
replacer(?) - Функция, которая изменяет поведение процесса стрингификации, или массив строк и чисел, который задает свойства, которые `value`должны быть включены в вывод. Если `replacer`это массив, все элементы в этом массиве, которые не являются строками или числами (либо примитивами, либо объектами-оболочками), включая [`Symbol`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol)значения, полностью игнорируются. Если `replacer`это что-либо, кроме функции или массива (например [`null`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/null), или не указано), все строковые свойства объекта включаются в результирующую строку JSON.
space - Строка или число, которые используются для вставки пробелов (включая отступы, символы переноса строки и т. д.) в выходную строку JSON для удобства чтения.
Если это число, оно указывает количество пробелов, которые будут использоваться в качестве отступа, ограниченное 10 (то есть любое число больше, чем `10`рассматривается как `10`). Значения меньше 1 указывают, что пробел не должен использоваться.
Если это строка, то строка (или первые 10 символов строки, если она длиннее) вставляется перед каждым вложенным объектом или массивом. Если `space`это что-то иное, чем строка или число (может быть как примитивным, так и объектом-оболочкой) — например, указано [`null`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/null)или не указано — пробелы не используются.

Исключения (TypeError)
	Выбрасывается в одном из следующих случаев:
		- `value`содержит циклическую ссылку.
		- [`BigInt`]Обнаружено значение .
		
Попытка сериализовать [`BigInt`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/BigInt)значения приведет к сбою. Однако, если у BigInt есть `toJSON()`метод (через monkey patching: `BigInt.prototype.toJSON = ...`), этот метод может предоставить результат сериализации.

```js
let room = {
  number: 23,
};

let meetup = {
  title: "Conference",
  participants: [{ name: "John" }, { name: "Alice" }],
  place: room, // meetup ссылается на room
};

room.occupiedBy = meetup; // room ссылается на meetup
alert(JSON.stringify(meetup, ["title", "participants"]));
// {"title":"Conference","participants":[{},{}]}
```

```js
let room = {
  number: 23
};

let meetup = {
  title: "Conference",
  participants: [{name: "John"}, {name: "Alice"}],
  place: room // meetup ссылается на room
};

room.occupiedBy = meetup; // room ссылается на meetup

console.log(JSON.stringify(meetup, ['title', 'participants', 'place', 'name', 'number']) );
```

Всё, кроме `occupiedBy`, сериализовано. Также вместо массива иожно использовать функцию. Функция будет вызываться для каждой пары `(key, value)`, и она должна возвращать заменённое значение, которое будет использоваться вместо исходного. Или `undefined`, чтобы пропустить значение.

```js
let room = {
  number: 23,
};

let meetup = {
  title: "Conference",
  participants: [{ name: "John" }, { name: "Alice" }],
  place: room, // meetup ссылается на room
};

room.occupiedBy = meetup; // room ссылается на meetup

const replacer = (key, value) => (key === "occupiedBy" ? undefined : value);

console.log(JSON.stringify(meetup, replacer));

/* пары ключ:значение, которые приходят в replacer:
:             [object Object]
title:        Conference
participants: [object Object],[object Object]
0:            [object Object]
name:         John
1:            [object Object]
name:         Alice
place:        [object Object]
number:       23
occupiedBy: [object Object]
*/
```

Функция `replacer` получает каждую пару ключ/значение, включая вложенные объекты и элементы массива. И она применяется рекурсивно. Значение `this` внутри `replacer` – это объект, который содержит текущее свойство. Первый вызов – особенный. Ему передаётся специальный «объект-обёртка»: `{"": meetup}`. Другими словами, первая `(key, value)` пара имеет пустой ключ, а значением является целевой объект в общем. Вот почему первая строка из примера выше будет `":[object Object]"`.

### toJson()

Как и `toString` для преобразования строк, объект может предоставлять метод `toJSON` для преобразования в JSON. `JSON.stringify` автоматически вызывает его, если он есть.

```js
let room = {
  number: 23,
};

let meetup = {
  title: "Conference",
  date: new Date(Date.UTC(2017, 0, 1)),
  room,
};

console.log(JSON.stringify(meetup));
```

`date` `(1)` стал строкой. Это потому, что все объекты типа `Date` имеют встроенный метод `toJSON`, который возвращает такую строку.

```js
let room = {
  number: 23,
  toJSON() {
    return this.number;
  },
};

let meetup = {
  title: "Conference",
  room,
};

console.log(JSON.stringify(room)); // 23
console.log(JSON.stringify(meetup)); // {"title":"Conference","room":23}
```

`toJSON` используется как при прямом вызове `JSON.stringify(room)`, так и когда `room` вложен в другой сериализуемый объект.

### JSON.parse()

Статический **`JSON.parse()`**метод анализирует строку JSON, создавая значение JavaScript или объект, описанный строкой.

text - Строка для анализа как JSON. [`JSON`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON)Описание синтаксиса JSON см. в объекте.
reviver(?) - Вызывается для каждого свойства строки объекта.  