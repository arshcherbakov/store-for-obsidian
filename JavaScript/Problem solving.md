**Полиморфизм и композиция функций**

```js
const pipe = (...args) => {
	const functionList = [...args];
	return (...args) => functionList.reduce((result, fn) => result = fn(result), ...args);
}

const add = (x) => x + 2;
const multiply = (x) => x * 3;
const pow = (x) => x ** 2;

const pipedFunction = pipe(add, multiply, pow);

console.log(pipedFunction(5));
```

**Глубокое клонирование и рекурсия**

  ```js
  const clone = (obj) => {
	  if (!(obj instanceof Object)) {
	    return obj;
	  }

	  if (Array.isArray(obj)) {
	    return obj.map((element) => clone(element));
	  }
  
	  if (obj instanceof Date) {
	    return new Date(obj);
	  }

	  if (obj instanceof RegExp) {
	    return new RegExp(obj);
	  }
  // new Function(obj) не правильно. Function конструктор используется для создания новых функций на основе строки кода,

	  // а не для копирования существующих функций
	  if (obj instanceof Function) {
	    return obj;
	  }

	  return Object.keys(obj).reduce((newObject, prop) => {
	    newObject[prop] = clone(obj[prop]);
	
	    return newObject;
	  }, {});
};

const complexObj = {
  foo: function () {
    return 6;
  },
  data: [1, { nested: true }, [3, 4]],
  info: {
    key: "value",
    anotherKey: {
      deep: "deepValue",
    },
    birthDate: new Date("1979-05-21"),
    pattern: /abc/gi,
    foo: () => 5,
  },
};
const copObj = clone(complexObj);

copObj.lol = "lol";
copObj.data[2].push(1);

console.log("object", complexObj);
console.log("copy object", copObj);
console.log(copObj.foo());

copObj.foo = function () {
  return 69;
};

console.log(copObj.foo());
console.log(complexObj.foo());
console.log(complexObj.info.foo());
```