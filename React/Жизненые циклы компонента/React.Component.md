![[Pasted image 20241216130714.png]]

ссылка на эту [диаграмму](https://projects.wojtekmaj.pl/react-lifecycle-methods-diagram/)

### Constructor

Он позволяет объявлять состояние и [привязывать](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes/constructor) [методы](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_objects/Function/bind) класса к экземпляру класса:

```js
class Counter extends Component {  

constructor(props) {  
	super(props);  
	this.state = { counter: 0 };  
	this.handleClick = this.handleClick.bind(this);  
}  

handleClick() {  
// ...  
}
```

Если вы используете современный синтаксис JavaScript, конструкторы нужны редко. Вместо этого вы можете переписать этот код выше, используя [синтаксис открытого поля класса](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes/Public_class_fields) 
|
```js
class Counter extends Component {  
	state = { counter: 0 };  

	handleClick = () => {  
}
```

- Конструктор не должен содержать побочных эффектов или подписок. Вместо этого используйте [`componentDidMount`](https://react.dev/reference/react/Component#componentdidmount)для этого.

- При использовании [серверного рендеринга](https://react.dev/reference/react-dom/server) конструктор также будет запущен на сервере, а затем и [`render`](https://react.dev/reference/react/Component#render)метод. Однако методы жизненного цикла, такие как `componentDidMount`или `componentWillUnmount`не будут запущены на сервере.

### static getDerivedStateFromProps(props, state)

React вызовет его прямо перед вызовом [`render`,](https://react.dev/reference/react/Component#render) как при начальном монтировании, так и при последующих обновлениях. Он должен вернуть объект для обновления состояния или `null`не обновлять ничего.
Этот метод существует для [редких случаев использования](https://legacy.reactjs.org/blog/2018/06/07/you-probably-dont-need-derived-state.html#when-to-use-derived-state) , когда состояние зависит от изменений в props с течением времени. Он позволяет компоненту обновлять свое внутреннее состояние в результате **изменений в props**.

#### Параметры[](https://react.dev/reference/react/Component#static-getderivedstatefromprops-parameters "Ссылка для параметров")

- `props`: Следующие реквизиты, которые компонент собирается отрисовать.
- `state`: Следующее состояние, в котором компонент собирается отрисовываться.

#### Возвраты[](https://react.dev/reference/react/Component#static-getderivedstatefromprops-returns "Ссылка для возврата")

`static getDerivedStateFromProps`вернуть объект для обновления состояния или `null`не обновлять ничего.

### shouldComponentUpdate(nextProps, nextState, nextContext)

React вызовет его, чтобы определить, можно ли пропустить повторную визуализацию.

Если вы уверены, что хотите написать его вручную, вы можете сравнить `this.props`с `nextProps`и `this.state`с `nextState`и вернуться, `false`чтобы сообщить React, что обновление можно пропустить.

#### Возвраты[](https://react.dev/reference/react/Component#shouldcomponentupdate-returns "Ссылка для возврата")

Верните, `true`если хотите, чтобы компонент перерисовался. Это поведение по умолчанию.

Вернитесь `false`, чтобы сообщить React, что повторную отрисовку можно пропустить.
#### Параметры[](https://react.dev/reference/react/Component#shouldcomponentupdate-parameters "Ссылка для параметров")

- `nextProps`: Следующие реквизиты, которые компонент собирается отрисовать. Сравните `nextProps`с, [`this.props`](https://react.dev/reference/react/Component#props)чтобы определить, что изменилось.
- `nextState`: Следующее состояние, с которым компонент собирается отрисовываться. Сравните `nextState`с, [`this.state`](https://react.dev/reference/react/Component#props)чтобы определить, что изменилось.
- `nextContext`: Следующий контекст, с которым компонент собирается отрисовать. Сравните `nextContext`с, [`this.context`](https://react.dev/reference/react/Component#context)чтобы определить, что изменилось. Доступно только если указано [`static contextType`](https://react.dev/reference/react/Component#static-contexttype).


- Подумайте об использовании [`PureComponent`](https://react.dev/reference/react/PureComponent)вместо написания `shouldComponentUpdate`вручную. `PureComponent`поверхностно сравнивает реквизиты и состояние и снижает вероятность того, что вы пропустите необходимое обновление.
- Мы не рекомендуем делать глубокие проверки равенства или использовать `JSON.stringify`in `shouldComponentUpdate`. Это делает производительность непредсказуемой и зависящей от структуры данных каждого prop и состояния. В лучшем случае вы рискуете ввести многосекундные задержки в свое приложение, а в худшем — рискуете его сбой.
- Возврат `false`не препятствует повторной отрисовке дочерних компонентов при изменении _их_ состояния.
- Возврат `false`не _гарантирует_ , что компонент не будет перерисован. React будет использовать возвращаемое значение как подсказку, но он все равно может решить перерисовать ваш компонент, если это имеет смысл по другим причинам.

### Render

Метод `render`является единственным обязательным методом в компоненте класса. Ондолжен указывать, что именно отобразить на экране. React может вызваться `render`в любой момент, поэтому не стоит предполагать, что он запустится в определенное время. Вы должны написать `render`метод как чистую функцию, то есть он должен возвращать тот же результат, если props, state и context одинаковы. Он также не должен содержать побочных эффектов (например, настройку подписок) или взаимодействовать с API браузера. Побочные эффекты должны возникать либо в обработчиках событий, либо в методах, таких как [`componentDidMount`](https://react.dev/reference/react/Component#componentdidmount)

- `render`не будет вызван, если [`shouldComponentUpdate`](https://react.dev/reference/react/Component#shouldcomponentupdate)определен и возвращает `false`.
- `render`Между вызовом и последующим вызовом `componentDidMount`или нет однозначного соответствия `componentDidUpdate`. Некоторые `render`результаты вызова могут быть отброшены React, когда это выгодно.

### getSnapshotBeforeUpdate(prevProps, prevState)

Если вы реализуете `getSnapshotBeforeUpdate`, React вызовет его непосредственно перед тем, как React обновит DOM. Это позволяет вашему компоненту захватывать некоторую информацию из DOM (например, позицию прокрутки) до того, как она потенциально изменится. Любое значение, возвращаемое этим методом жизненного цикла, будет передано в качестве параметра в [`componentDidUpdate`.](https://react.dev/reference/react/Component#componentdidupdate)

#### Параметры[](https://react.dev/reference/react/Component#getsnapshotbeforeupdate-parameters "Ссылка для параметров")

- `prevProps`: Реквизит до обновления. Сравните `prevProps`с, [`this.props`](https://react.dev/reference/react/Component#props)чтобы определить, что изменилось.
    
- `prevState`: Состояние до обновления. Сравните `prevState`с, [`this.state`](https://react.dev/reference/react/Component#state)чтобы определить, что изменилось.
#### Возвраты[](https://react.dev/reference/react/Component#getsnapshotbeforeupdate-returns "Ссылка для возврата")

Вы должны вернуть значение снимка любого типа, который вам нужен, или `null`. Возвращенное вами значение будет передано в качестве третьего аргумента в [`componentDidUpdate`.](https://react.dev/reference/react/Component#componentdidupdate)

`getSnapshotBeforeUpdate`не будет вызван, если [`shouldComponentUpdate`](https://react.dev/reference/react/Component#shouldcomponentupdate)определен и возвращает `false`.

```js
class ScrollingList extends React.Component {  

constructor(props) {  
	super(props);  
	this.listRef = React.createRef();  
}  

getSnapshotBeforeUpdate(prevProps, prevState) {  
	// Are we adding new items to the list?  
	// Capture the scroll position so we can adjust scroll later.  
	if (prevProps.list.length < this.props.list.length) {  
		const list = this.listRef.current;  
		
		return list.scrollHeight - list.scrollTop;  
	}  
	
	return null;  
}  

componentDidUpdate(prevProps, prevState, snapshot) {  

	// If we have a snapshot value, we've just added new items.  
	
	// Adjust scroll so these new items don't push the old ones out of view.  
	
	// (snapshot here is the value returned from getSnapshotBeforeUpdate)  

	if (snapshot !== null) {  
		const list = this.listRef.current;  
		
		list.scrollTop = list.scrollHeight - snapshot;  	
	}  
}  

render() {  
	return (  
		<div ref={this.listRef}>{/* ...contents... */}</div>  
	);  
}  

}
```

### componentDidMount
React вызовет его, когда компонент будет добавлен _(смонтирован)_ на экран. Это обычное место для начала извлечения данных, настройки подписок или манипулирования узлами DOM.

#### Параметры[](https://react.dev/reference/react/Component#componentdidmount-parameters "Ссылка для параметров")

`componentDidMount`не принимает никаких параметров.

#### Возвраты[](https://react.dev/reference/react/Component#componentdidmount-returns "Ссылка для возврата")

`componentDidMount`не должен ничего возвращать.

Когда [Strict Mode](https://react.dev/reference/react/StrictMode) включен, в разработке React вызовет `componentDidMount`, затем немедленно вызовет [`componentWillUnmount`,](https://react.dev/reference/react/Component#componentwillunmount) а затем снова вызовет `componentDidMount`. Это поможет вам заметить, если вы забыли реализовать `componentWillUnmount`или если его логика не полностью «зеркально» отражает то, что `componentDidMount`делает.

https://react.dev/reference/react/Component#getsnapshotbeforeupdate

### componentDidUpdate(prevProps, prevState, snapshot?)


React вызовет его сразу после того, как компонент будет повторно отрендерен с обновленными свойствами или состоянием. Этот метод не вызывается для первоначального рендера. Использовать его для управления DOM после обновления. Это также обычное место для сетевых запросов, если вы сравниваете текущие свойства с предыдущими (например, сетевой запрос может не потребоваться, если свойства не изменились).

```js
class ChatRoom extends Component {  

	state = {  
		serverUrl: 'https://localhost:1234'  
	};  

	componentDidMount() {  
		this.setupConnection();  
	}  

  
	componentDidUpdate(prevProps, prevState) {  
		if (this.props.roomId !== prevProps.roomId ||  
			this.state.serverUrl !== prevState.serverUrl  
		) {  
			this.destroyConnection();  
			
			this.setupConnection();  
		}  
	}  
	
	componentWillUnmount() {  
	
	this.destroyConnection();  
	
	}  

  

// ...  

}
```


#### Параметры[](https://react.dev/reference/react/Component#componentdidupdate-parameters "Ссылка для параметров")
- `prevProps`: Реквизит до обновления. Сравните `prevProps`с, [`this.props`](https://react.dev/reference/react/Component#props)чтобы определить, что изменилось.
- `prevState`: Состояние до обновления. Сравните `prevState`с, [`this.state`](https://react.dev/reference/react/Component#state)чтобы определить, что изменилось.
- `snapshot`: Если вы реализовали [`getSnapshotBeforeUpdate`](https://react.dev/reference/react/Component#getsnapshotbeforeupdate), `snapshot`будет содержать значение, которое вы вернули из этого метода. В противном случае это будет `undefined`.
#### Возвраты[](https://react.dev/reference/react/Component#componentdidupdate-returns "Ссылка для возврата")
`componentDidUpdate`не должен ничего возвращать.

- `componentDidUpdate`не будет вызван, если [`shouldComponentUpdate`](https://react.dev/reference/react/Component#shouldcomponentupdate)определен и возвращает `false`.
    
- Логика внутри `componentDidUpdate`обычно должна быть обернута в условия сравнения `this.props`с `prevProps`, и `this.state`с `prevState`. В противном случае есть риск создания бесконечных циклов.

### componentWillUnmount()

React вызовет его до того, как ваш компонент будет удален _(размонтирован)_ с экрана. Это обычное место для отмены извлечения данных или удаления подписок.

Логика внутри `componentWillUnmount`должна «зеркалить» логику внутри [`componentDidMount`.](https://react.dev/reference/react/Component#componentdidmount) Например, если `componentDidMount`устанавливает подписку, `componentWillUnmount`следует очистить эту подписку. Если логика очистки в вашем `componentWillUnmount`считывает некоторые свойства или состояние, вам обычно также нужно будет реализовать [`componentDidUpdate`](https://react.dev/reference/react/Component#componentdidupdate)очистку ресурсов (таких как подписки), соответствующих старым свойствам и состоянию.

#### Параметры[](https://react.dev/reference/react/Component#componentwillunmount-parameters "Ссылка для параметров")

`componentWillUnmount`не принимает никаких параметров.

#### Возвраты[](https://react.dev/reference/react/Component#componentwillunmount-returns "Ссылка для возврата")

`componentWillUnmount`не должен ничего возвращать.

#### Предостережения[](https://react.dev/reference/react/Component#componentwillunmount-caveats "Ссылка для предостережений")

- Когда [Strict Mode](https://react.dev/reference/react/StrictMode) включен, в разработке React вызовет [`componentDidMount`,](https://react.dev/reference/react/Component#componentdidmount) затем немедленно вызовет `componentWillUnmount`, а затем снова вызовет `componentDidMount`. Это поможет вам заметить, если вы забыли реализовать `componentWillUnmount`или если его логика не полностью «зеркально» отражает то, что `componentDidMount`делает.

Этот API был переименован с `componentWillMount`на [`UNSAFE_componentWillMount`.](https://react.dev/reference/react/Component#unsafe_componentwillmount) Старое имя устарело. В будущей основной версии React будет работать только новое имя.
