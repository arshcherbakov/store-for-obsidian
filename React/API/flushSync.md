Вызовите `flushSync`, чтобы заставить React промыть все ожидающие работы и синхронно обновить DOM.

```js
import { flushSync } from 'react-dom';

flushSync(() => {
    setSomething(123);
});
```

Используйте `flushSync` в крайнем случае.

**Параметры**

- `callback`: Функция. React немедленно вызовет этот обратный вызов и синхронно прошьет все содержащиеся в нем обновления. Он также может прошить все ожидающие обновления, или эффекты, или обновления внутри эффектов. Если обновление приостанавливается в результате этого вызова `flushSync`, фалбэки могут быть показаны заново.

**Возврат**

`flushSync` возвращает `undefined`.

**Предостережения**

- `flushSync` может значительно снизить производительность. Используйте его осторожно.
- `flushSync` может заставить ожидающие границы приостановки показать свое состояние `отката`.
- `flushSync` может запускать отложенные эффекты и синхронно применять все содержащиеся в них обновления перед возвратом.
- `flushSync` может промывать обновления вне обратного вызова, когда это необходимо для промывки обновлений внутри обратного вызова. Например, если есть ожидающие обновления от клика, React может промыть их, прежде чем промыть обновления внутри обратного вызова.

https://reactdev.ru/reference/react-dom/flushSync/#flushing-updates-for-third-party-integrations