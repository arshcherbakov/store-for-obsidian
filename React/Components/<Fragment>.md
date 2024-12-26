Используемый через `<>...</>`синтаксис, позволяет группировать элементы без узла-обертки.

```js
<>  
	<OneChild />  
	<AnotherChild />  
</>
```

`<Fragment>` используется чтобы сгруппировать элементы вместе в ситуациях, когда нужен один элемент. Группировка элементов в `Fragment`не влияет на результирующий DOM; это то же самое, как если бы элементы не были сгруппированы. Пустой тег JSX `<></>`является сокращением для `<Fragment></Fragment>`в большинстве случаев.

### Принимает

- **необязательно** `key` : Фрагменты, объявленные с явным `<Fragment>`синтаксисом, могут иметь [ключи.](https://react.dev/learn/rendering-lists#keeping-list-items-in-order-with-key)

### Предостережения

`key`к фрагменту,  нельзя использовать `<>...</>`синтаксис.  Нужно явно импортировать `Fragment`из `'react'`и отрисовать `<Fragment key={yourKey}>...</Fragment>`.
React не [сбрасывает состояние](https://react.dev/learn/preserving-and-resetting-state) , когда вы переходите от рендеринга `<><Child /></>`к `[<Child />]`или обратно, или когда вы переходите от рендеринга `<><Child /></>`к `<Child />`и обратно. Это работает только на один уровень в глубину: например, переход от `<><><Child /></></>`к `<Child />`сбрасывает состояние. Точную семантику см. [здесь.](https://gist.github.com/clemmy/b3ef00f9507909429d8aa0d3ee4f986b)

![[Pasted image 20241220124540.png]]