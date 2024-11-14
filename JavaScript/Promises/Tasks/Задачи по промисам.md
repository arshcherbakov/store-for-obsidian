### 1. **Промисы и цепочки**

У вас есть функция `asyncOperation`, которая возвращает промис и завершает выполнение за случайное время от 1 до 3 секунд. Напишите код, который выполняет три таких операции последовательно и выводит в консоль результат каждой из них.

```js
function asyncOperation() { 
	return new Promise((resolve) => { 
		const delay = Math.floor(Math.random() * 2000) + 1000; 
		setTimeout(() => { resolve(`Завершено за ${delay} мс`); }, delay); 
	}); 
}

const getResult = async () => { 
	console.log(await asyncOperation()); 
	console.log(await asyncOperation()); 
	console.log(await asyncOperation()); 
} 

getResult();
```

Напишите функцию `runParallel`, которая принимает массив функций, возвращающих промисы, и запускает их параллельно, возвращая массив результатов. Убедитесь, что функция ждет завершения всех промисов перед возвратом результата.

### 2. **Параллельное выполнение промисов**

Напишите функцию `runParallel`, которая принимает массив функций, возвращающих промисы, и запускает их параллельно, возвращая массив результатов. Убедитесь, что функция ждет завершения всех промисов перед возвратом результата.

```js
const runParallel = (promises) => Promise.all(promises.map(promis => promis()));

const tasks = [
	() => new Promise(resolve => setTimeout(() => resolve('Task 1'), 1000)),
	() => new Promise(resolve => setTimeout(() => resolve('Task 2'), 2000)),
	() => new Promise(resolve => setTimeout(() => resolve('Task 3'), 1500))
];

runParallel(tasks).then(res => console.log(res));
```

### 3. **Обработка ошибок в цепочках промисов**

Дан массив функций, возвращающих промисы, некоторые из которых могут завершиться с ошибкой. Напишите функцию, которая будет запускать эти промисы ==последовательно== и обрабатывать ошибки так, чтобы выполнение продолжалось даже в случае сбоя одного из промисов, но ошибки должны логироваться в консоль.

```js
const getValueFromPromises = async (tasks) => {
  const promResult = [];

  for (const task of tasks) {
    try {
      const result = await task; 
      promResult.push(result);
    } catch (error) {
      console.log(error);
    }
  }

  return promResult;
};

const tasks = [
  Promise.resolve('Task 1'),
  Promise.resolve('Task 2'),
  Promise.reject('Error in Task 3'),
  Promise.resolve('Task 4'),
  Promise.reject('Error in Task 5'),
];

getValueFromPromises(tasks).then(res => console.log(res));

```

### 4. **Лимитирование параллельных задач**

Напишите функцию `runWithLimit`, которая принимает массив функций, возвращающих промисы, и максимальное количество ==параллельно== выполняющихся задач. Выполнение должно происходить с ограничением на количество одновременно запущенных промисов.

```js
const runWithLimit = async (tasks, countTask) => {
  let resolveTask = [];

  for (let i = 0; i < tasks.length; i += countTask) {
    const taskBatch = tasks.slice(i, i + countTask).map(task => task());
    const res = await Promise.all(taskBatch);
    resolveTask = resolveTask.concat(res);
  }

  return resolveTask;
}

const tasks = [
  () => new Promise(resolve => setTimeout(() => resolve('Task 1'), 1000)),
  () => new Promise(resolve => setTimeout(() => resolve('Task 2'), 2000)),
  () => new Promise(resolve => setTimeout(() => resolve('Task 3'), 1500))
];

runWithLimit(tasks, 2).then(res => console.log(res));
```

### 6. **Имитация `Promise.allSettled`**

Напишите свою реализацию `allSettled`, которая принимает массив промисов и возвращает массив объектов с результатами выполнения каждого промиса, включая статус и значения или ошибки.`**

```js
const allSettled = (promosesList) => {
	return new Promise((resolve) => {
	    const arrayPromis = [];
		let countPromises = 0;

	  	promosesList.forEach((promis, index) => {
		  promis
		  .then(response => {
		    arrayPromis[index] = { status:"fulfilled", value: response };
		  })
		  .catch(error => {
			arrayPromis[index] = { status:"rejected", reason: error };
		   })
		   .finally(() => {
				countPromises++;
				
				if(countPromises === promosesList.length)
					resolve(arrayPromis);

			});
		});
	});
}

const tasks = [
	new Promise(resolve => setTimeout(() => resolve('Task 1'), 1000)),
	new Promise(resolve => setTimeout(() => resolve('Task 2'), 2000)),
	new Promise((resolve, reject) => setTimeout(() => reject('Task 3'), 1500)),
	new Promise(resolve => setTimeout(() => resolve('Task 4'), 1500)),
	new Promise((resolve, reject) => setTimeout(() => reject('Task 5'), 2500)),
];

allSettled(tasks).then(res => console.log(res));
```

Переменная `countPromises` используется для отслеживания, сколько промисов уже завершено. Только когда все промисы завершатся, будет вызван `resolve(arrayPromis)`. Это гарантирует, что массив результатов будет полностью заполнен к моменту выполнения `resolve`.

###### Пример объяснения потока без `countPromises`:
- Представим, что функция `allSettled` запускается, и цикл `forEach` перебирает все промисы.
- Асинхронные блоки `then`, `catch` и `finally` ставятся в очередь, но не выполняются немедленно.
- Если вызвать `resolve(arrayPromis)` вне этой очереди, он выполнится **раньше**, чем асинхронные колбэки заполнят массив `arrayPromis`.
- Все `then`, `catch` и `finally` являются асинхронными операциями. Они будут выполнены только после того, как текущий стек вызовов будет завершен и управление передано в очередь событий (event loop).
- Если вызвать `resolve(arrayPromis)` вне `finally` или без проверки `countPromises`, то промис-обертка завершится **сразу**, и `arrayPromis` еще не будет заполнен результатами.