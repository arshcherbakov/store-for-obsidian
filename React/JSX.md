JSX — синтаксический сахар для функции `React.createElement(component, props, ...children)`.

```js
<MyButton color="blue" shadowSize={2}>Click me</MyButton>

// компилируеться в 
React.createElement(
	MyButton,
	{ color: 'blue', shadowSize: 2 },
	'Click me'
)
```

Посмотреть, как JSX-код компилируется в JavaScript,  [онлайн-компилятор Babel](https://babeljs.io/repl/#?presets=react&code_lz=GYVwdgxgLglg9mABACwKYBt1wBQEpEDeAUIogE6pQhlIA8AJjAG4B8AEhlogO5xnr0AhLQD0jVgG4iAXyJA).

Название компонента должно быть с большой буквы, так как react может воспринимать этот компонент как тэг(<div />, <section />)
```js
import React from 'react';

// Неправильно! Это компонент и он должен быть записан с заглавной буквы:function hello(props) {  // Правильно! Использование <div> разрешено, так как это валидный HTML-тег:
  return <div>Привет, {props.toWhat}</div>;
}

function HelloWorld() {
  // Неправильно! React думает, что <hello /> - это HTML-тег,  // потому что он записан с маленькой буквы:  return <hello toWhat="Мир" />;
}
```

Оператор `if` и цикл `for` не являются выражениями в JavaScript, поэтому их нельзя непосредственно использовать в JSX. Вместо этого, вы можете окружить ими JSX-код. К примеру:

```js
function NumberDescriber(props) {
  let description;
  
  if (props.number % 2 == 0) {    
	  description = <strong>чётным</strong>;  
  } else {    
	  description = <i>нечётным</i>;  
  }  
  
  return <div>{props.number} является {description} числом</div>;
}
```

https://ru.legacy.reactjs.org/docs/jsx-in-depth.html