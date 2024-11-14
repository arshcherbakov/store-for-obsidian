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