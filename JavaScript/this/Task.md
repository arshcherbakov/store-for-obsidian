```js
function showThis() { 
	console.log(this); 
} 

const obj = { method: showThis }; 
showThis(); //1
obj.method(); //2
const newMethod = obj.method; 
newMethod() //3
```
Ответ :  1) window; 2) obj {method: showThis}; 3) window

```js
function greet() {
  console.log(`Hello, my name is ${this.name}`);
}

const user1 = { name: 'Alice' };
const user2 = { name: 'Bob' };

greet.call(user1);   // Что выведет?
greet.apply(user2);  // Что выведет?

const boundGreet = greet.bind(user1);
boundGreet();        // Что выведет?
```
Ответ :  1) Alice; 2) Bob 3) Alice

```js
const person = {
  name: 'John',
  sayName() {
    console.log(this.name);
  }
};

const sayNameFunction = person.sayName;
sayNameFunction(); 

```

Ответ :  1) undefined. Так как важно как мы вызвали метод объекта. В это примере вызов был не через дотнатацию(точку). Контекст теряется, так как метод присваивается переменной `newMethod` и затем вызывается как обычная функция без объекта перед точкой. При этом `this` не сохраняется, так как не привязан к исходному объекту `obj`.

```js
const person = {
  name: 'Jane',
  greet: function() {
    const innerGreet = () => {
      console.log(`Hello, my name is ${this.name}`);
    };
    innerGreet();
  }
};

person.greet(); // Что выведет?

```
Ответ :  1) Jane; 

```js
class Car {
  constructor(brand) {
    this.brand = brand;
  }

  showBrand() {
    console.log(this.brand);
  }
}

const myCar = new Car('Toyota');
setTimeout(myCar.showBrand, 1000); // Что произойдет и почему?

```
Ответ :  1) undefined;  Передача метода в качестве аргумента функции (например, в `setTimeout` или обработчике событий) может привести к потере контекста.

Решение 
```js
setTimeout(myCar.showBrand.bind(myCar), 1000);
```
или
```js
class Car {
  constructor(brand) {
    this.brand = brand;
  }

  showBrand = () => {
    console.log(this.brand);
  }
}

const myCar = new Car('Toyota');
setTimeout(myCar.showBrand, 1000); // Что произойдет и почему?

```

```js
const obj = {
  data: 'Important data',
  print() {
    function innerPrint() {
      console.log(this.data);
    }
    innerPrint();
  }
};

obj.print(); // Что выведет и почему?

```
Ответ :  1) undefined. 

```js
const counter = {
  value: 0,
  increment() {
    setTimeout(function() {
      console.log(this.value++);
    }, 1000);
  }
};

counter.increment(); // Какой будет результат?
```
Ответ :  1) Nan. Решпеться если в setTimeout передать стрелочную функцию.

```js
const obj = {
  name: 'Example',
  printName() {
    ['one', 'two', 'three'].forEach(function(item) {
      console.log(this.name, item);
    });
  }
};

obj.printName(); // Что выведет код и почему?
```
Ответ :  Будет выводиться содержимое массива, но имя будет undefined. Так как колбэком в forEach являеться Function Declaration. Она имеет свой this. Правиться это стрелочной функцией.

```js
const obj = {
  name: 'Example',
  getName() {
    return function() {
      console.log(this.name);
    };
  }
};

const fn = obj.getName();
fn(); // Какой будет результат?

const boundFn = obj.getName().bind(obj);
boundFn(); // Какой будет результат и почему?

```
Ответ :  1) undefined; 2) Example; Второй вызов, - `bind` привязывает функцию к конкретному объекту, поэтому даже если функция возвращается и вызывается в другом контексте, она сохраняет привязку к `obj`.Вызов `boundFn()` выведет `'Example'`, так как `this` привязан к `obj` благодаря `bind`.

```js
const obj = {
  name: 'Example',
  printName: function() {
    const innerFunction = () => {
      console.log(this.name);
    };
    innerFunction();
  }
};

obj.printName(); // Что будет выведено?

const anotherObj = {
  name: 'Another',
  printName: obj.printName
};

anotherObj.printName(); // Что будет выведено и почему?

```
Ответ :  1) Example; 2) Another; - Свойство `printName` присваивается `anotherObj`, но это все еще та же функция, что и `obj.printName`. Когда `anotherObj.printName()` вызывается, контекст `this` внутри `printName` становится `anotherObj`. Поскольку стрелочная функция `innerFunction` захватывает `this` из того окружения, где была объявлена `printName`, `this` будет ссылаться на `anotherObj`. Поэтому при вызове `anotherObj.printName()` будет выведено `'Another'`.

```js
class Example {
  constructor(name) {
    this.name = name;
  }

  printName() {
    console.log(this.name);
  }

  delayedPrint() {
    setTimeout(this.printName, 1000);
  }
}

const instance = new Example('Test');
instance.delayedPrint(); // Что будет выведено через 1 секунду?

```
Ответ : undefined;

```js
const button = document.querySelector('button');

button.addEventListener('click', function() {
  console.log(this); // Что будет выведено?
});

button.addEventListener('click', () => {
  console.log(this); // Что будет выведено?
});

```
**Ответ**: В первом случае `this` указывает на элемент `button`, а во втором случае `this` определяется окружением, в котором была создана стрелочная функция (в глобальном контексте или в контексте родительской функции)