**`useRef`** - это хук React, позволяющий ссылаться на значение, которое не нужно для рендеринга. Он обходит VDOM и напрямую взаимодействует с DOM. Рефы дают возможность получить доступ к DOM-узлам или React-элементам, созданным в рендер-методе.

#### Параметры

- `initialValue`: Значение свойства `current` объекта ref было первоначальным. Это может быть значение любого типа. Этот аргумент игнорируется после первоначального рендеринга.

#### Возвращаемое значение

Функция `useRef` возвращает объект с одним свойством `current`. Изначально оно установлено на `initialValue`, которое было передано.  Если передадть объект ref в React как атрибут `ref` узла JSX, React установит его свойство `current`.

Когда изменяется свойство `ref.current`, React не перерисовывает компонент. React не знает, когда вы изменяете его, потому что ref - это обычный объект JavaScript. Это означает, что `ref.current`идеально подходит для хранения информации, которая не влияет на визуальный вывод компонента.

```js
import { useRef } from 'react';

function Stopwatch() {
    const intervalRef = useRef(0);
    // ...
    
    intervalRef.current = 1; // обновление объекта
}
```

Используя реф-ссылку, вы гарантируете, что:

- Вы можете **сохранять информацию** между повторными рендерами (в отличие от обычных переменных, которые сбрасываются при каждом рендере).
- Ее изменение **не вызывает повторного рендеринга** (в отличие от переменных состояния, которые вызывают повторный рендеринг).
- Информация **является локальной** для каждой копии вашего компонента (в отличие от внешних переменных, которые являются общими).
  
Особенно часто ссылка используется для манипуляций с [DOM](https://developer.mozilla.org/docs/Web/API/HTML_DOM_API).

```js
import { useRef } from 'react';

function MyComponent() {
    const inputRef = useRef(null);

	// ...
	return <input ref={inputRef} />;
}
```
  
  Передается объект `ref` в качестве атрибута `ref` в JSX узла DOM, которым хотим манипулировать. После того как React создаст DOM-узел и поместит его на экран, React установит свойство `current` вашего объекта ref на этот DOM-узел. Теперь вы можете получить доступ к DOM-узлу `input` и вызвать такие методы, как [`focus()`](https://developer.mozilla.org/docs/Web/API/HTMLElement/focus):
  
```js
function handleClick() {
    inputRef.current.focus();
}
```

React установит свойство `current` обратно в `null`, когда узел будет удален с экрана.

Для реализации манипулирования DOM в дочернем компоненте, нужно передать `ref` и обернуть компонент к которому передается `ref` в `forwarRef`.

```js
import { forwardRef, useRef } from 'react';

const MyInput = forwardRef((props, ref) => {
    return <input {...props} ref={ref} />;
});

export default function Form() {
    const inputRef = useRef(null);

    function handleClick() {
        inputRef.current.focus();
    }

    return (
        <>
            <MyInput ref={inputRef} />
            <button onClick={handleClick}>
                Focus the input
            </button>
        </>
    );
}
```

Это происходит потому, что по умолчанию React не позволяет компоненту обращаться к узлам DOM других компонентов. Даже своим собственным детям! Это намеренно. Ссылки - это аварийный люк, который следует использовать очень редко. Ручное манипулирование DOM-узлами _другого_ компонента делает ваш код еще более хрупким. Вместо этого, компоненты, которые _хотят_ раскрыть свои узлы DOM, должны **оптировать** такое поведение. В приведенном выше примере `MyInput` раскрывает исходный элемент ввода DOM. Это позволяет родительскому компоненту вызывать `focus()` на нем. Однако это также позволяет родительскому компоненту делать что-то еще - например, изменять стили CSS. В редких случаях вы можете захотеть ограничить открытую функциональность. Вы можете сделать это с помощью `useImperativeHandle`:

```js
import {
    forwardRef,
    useRef,
    useImperativeHandle,
} from 'react';

const MyInput = forwardRef((props, ref) => {
    const realInputRef = useRef(null);
    
    useImperativeHandle(ref, () => ({
        focus() {
            realInputRef.current.focus();
        },
    }));
    
    return <input {...props} ref={realInputRef} />;
});

export default function Form() {
    const inputRef = useRef(null);

    function handleClick() {
        inputRef.current.focus();
    }

    return (
        <>
            <MyInput ref={inputRef} />
            <button onClick={handleClick}>
                Focus the input
            </button>
        </>
    );
}
```


В версии React 19, `ref` работает как пропс. Не нужно оборачивать в `forwardRef`.

Ситуации, в которых использование рефов является оправданным:

- Управление фокусом, выделение текста или воспроизведение медиа.
- Императивный вызов анимаций.
- Интеграция со сторонними DOM-библиотеками.
- Хранить значение и не вызыввать рендер при изменения этого значения.
  
https://reactdev.ru/reference/react/useRef/#manipulating-the-dom-with-a-ref