```js
<Suspense fallback={<Loading />}>  
	<SomeComponent />  
</Suspense>
```

Позволяет отображать резервный вариант до тех пор, пока не завершится загрузка его дочерних элементов.

#### Реквизит[](https://react.dev/reference/react/Suspense#props "Ссылка для реквизита")

- `children`:  Если `children`приостанавливается во время отрисовки, граница Suspense переключится на отрисовку `fallback`.
- `fallback`: Альтернативный пользовательский интерфейс для рендеринга вместо фактического пользовательского интерфейса, если он не завершил загрузку. Принимается любой допустимый узел React. 

#### Предостережения[](https://react.dev/reference/react/Suspense#caveats "Ссылка для предостережений")

- React не сохраняет состояние для рендеров, которые были приостановлены до того, как они смогли смонтироваться в первый раз. После загрузки компонента React попытается повторно отрисовать приостановленное дерево с нуля.
- Если Suspense отображал содержимое дерева, но затем снова приостановился, то `fallback`будет показан снова, если только обновление, вызвавшее его, не было вызвано [`startTransition`](https://react.dev/reference/react/startTransition)или [`useDeferredValue`](https://react.dev/reference/react/useDeferredValue).
- Если React необходимо скрыть уже видимый контент, поскольку он снова приостановлен, он очистит [эффекты макета](https://react.dev/reference/react/useLayoutEffect) в дереве контента. Когда контент будет готов к повторному отображению, React снова запустит эффекты макета. Это гарантирует, что эффекты, измеряющие макет DOM, не попытаются сделать это, пока контент скрыт.
- React включает в себя внутренние оптимизации, такие как _потоковый рендеринг сервера_ и _селективная гидратация_ , которые интегрированы с Suspense. Прочитайте [архитектурный обзор](https://github.com/reactwg/react-18/discussions/37) и посмотрите [технический доклад](https://www.youtube.com/watch?v=pj5N-Khihgc) , чтобы узнать больше.