
**AbortController** — это класс, представленный в JavaScript, который позволяет управлять асинхронными операциями, такими как `Fetch запросы, Promise, fs, setTimeout и setInterval`. С его помощью можно прерывать выполнение асинхронных задач и предотвращать нежелательные побочные эффекты от выполнения задач, которые уже неактуальны. Он решает проблему [race condition](https://ru.wikipedia.org/wiki/%D0%A1%D0%BE%D1%81%D1%82%D0%BE%D1%8F%D0%BD%D0%B8%D0%B5_%D0%B3%D0%BE%D0%BD%D0%BA%D0%B8) (**Состояние гонки** или **неопределённость параллелизма**).

```js
const controller = new AbortController();

// После создания экземпляра AbortController, можно получить экземпляр AbortSignal, используя свойство signal:

const signal = controller.signal;

// Имитация отмены запроса через 3 секунды

setTimeout(() => {
	controller.abort();
}, 3000);

fetch('https://api.example.com/data', { signal })
.then(response => response.json())
.then(data => console.log(data))
.catch(error => {
	if (error.name === 'AbortError') {
		console.log('Fetch request aborted');
	} else {
		console.error('Fetch request failed:', error);
	}
});

```

В этом примере через 3 секунды после начала запроса к API будет вызван метод `abort()`, что приведет к отмене Fetch запроса. Если запрос не будет завершен в течение 3 секунд, обработчик ошибок перехватит событие отмены и выведет соответствующее сообщение в консоль. Если же запрос успеет завершиться раньше, результат будет обработан и выведен в консоль без отмены.

Пример использование в react хуке https://habr.com/ru/companies/tensor/articles/779718/

```js
import { useEffect, useState } from "react";

interface IError {
  message: string;
  error: object | null;
}

const initErrorState: IError ={
  message: '',
  error: null,
}

const useFetch = <T>(url : string) => {
  const [isLoading, setLoading] = useState(false);
  const [response, setResponse] = useState<T | null>(null);
  const [error, setError] = useState<IError>(initErrorState);

  useEffect(() => {
    if (!url) {
      setError({
        message: "Отсутствует url",
        error: null
      })

      return;
    };

    const conttroller = new AbortController();
    const signal: AbortSignal = conttroller.signal;

    const getData = async (): Promise<void> => {
      setLoading(true)
      setResponse(null);
      setError(initErrorState);

      try {
        const responseFetch: Response = await fetch(url, {
          signal
        });

        if (!responseFetch.ok) {
          throw new Error(`HTTP error! Status: ${responseFetch.status}`);
        }

        const responceData: T = await responseFetch.json();
      
        setResponse(responceData);
      } catch (err: unknown) {
        err instanceof DOMException && err.name === "AbortError" ? (
          console.warn("Запрос отменен")
        ) : (
          setError({
            message: "Отсутствует url",
            error: err instanceof Error ? { ...error } : null
          })
        );
      } finally {
        setLoading(false);
      }   
    }

    getData();

    return () => conttroller.abort();
  }, [url, error]);

  return {
    isLoading,
    response,
    error,
  }
}

export default useFetch;
```

https://github.com/arshcherbakov/my-hooks/pull/5

- Каждый раз, когда изменяется `url`, старый запрос отменяется, и создается новый `AbortController` для нового запроса.

- Если компонент размонтируется до завершения запроса, он также отменяется, предотвращая обновление состояния размонтированного компонента.

- Это гарантирует, что состояние всегда соответствует последнему активному запросу.

controller.abort() отменяет запрос к бэкэнду. Использование AbortController может позволить бэку более эффективно использовать свои ресурсы (ну и траффик между фронтом и бэком немного сэкономим). **Пример задача с реализацией автокомплита.**

#### Отмена setTimeout и setInterval

```js
function createInterval(callback, interval, signal) {
  if (signal.aborted) {
    return;
  }

  const intervalId = setInterval(() => {
    callback();
    if (signal.aborted) {
      clearInterval(intervalId);
    }
  }, interval);

  signal.addEventListener('abort', () => {
    clearInterval(intervalId);
  });
}

const controller = new AbortController();
const signal = controller.signal;

createInterval(() => {
  console.log('Interval callback executed');
}, 1000, signal);

// Отменяем интервал через 5 секунд

setTimeout(() => {
  controller.abort();
}, 5000);
```
