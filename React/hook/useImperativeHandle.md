Это хук React, который позволяет вам настроить хэндл-обработчик.

```js
useImperativeHandle(ref, createHandle, dependencies?)
```

#### Параметры

- `ref`: `ref`, полученный в качестве второго аргумента от функции рендеринга [`forwardRef`](https://reactdev.ru/reference/react/forwardRef/#render-function).
- `createHandle`: Функция, которая не принимает аргументов и возвращает хэндл ссылки, которую вы хотите раскрыть. Этот ref handle может иметь любой тип. Обычно вы возвращаете объект с методами, которые вы хотите раскрыть.
- **опционально** `dependencies`: Список всех реактивных значений, на которые ссылается код `createHandle`. Реактивные значения включают пропсы, состояние, а также все переменные и функции, объявленные непосредственно в теле вашего компонента. Если ваш линтер [настроен на React](https://reactdev.ru/learn/editor-setup/#linting), он проверит, что каждое реактивное значение правильно указано в качестве зависимости. Список зависимостей должен иметь постоянное количество элементов и быть написан inline по типу `[dep1, dep2, dep3]`. React будет сравнивать каждую зависимость с предыдущим значением, используя сравнение [`Object.is`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object/is). Если в результате повторного рендеринга изменилась какая-то зависимость, или если вы опустили этот аргумент, ваша функция `createHandle` будет повторно выполнена, и вновь созданный хэндл будет назначен ссылке.

### Возврощает

Хук `useImperativeHandle` возвращает `undefined`.

```js
import { forwardRef, useImperativeHandle } from 'react';

const MyInput = forwardRef(function MyInput(props, ref) {
    useImperativeHandle(
        ref,
        () => {
            return {
                // ... your methods ...
            };
        },
        []
    );
    // ...
});
```

Например, предположим, что вы не хотите раскрывать весь DOM-узел `input`, но хотите раскрыть два его метода: `focus` и `scrollIntoView`. Для этого сохраните реальный DOM браузера в отдельном реферере. Затем используйте `useImperativeHandle`, чтобы раскрыть дескриптор только с теми методами, которые вы хотите, чтобы вызывал родительский компонент:

```js
import {
    forwardRef,
    useRef,
    useImperativeHandle,
} from 'react';

const MyInput = forwardRef(function MyInput(props, ref) {
    const inputRef = useRef(null);

    useImperativeHandle(
        ref,
        () => {
            return {
                focus() {
                    inputRef.current.focus();
                },
                scrollIntoView() {
                    inputRef.current.scrollIntoView();
                },
            };
        },
        []
    );

    return <input {...props} ref={inputRef} />;
});
```

Рефы следует использовать только для _императивного_ поведения, которое вы не можете выразить как пропс: например, прокрутка к узлу, фокусировка узла, запуск анимации, выделение текста и так далее.