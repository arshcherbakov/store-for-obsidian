Это замена чего-то реального (класс или его отдельный метод, функция или целый модуль) на дублера. Такой дублер воспринимается юнит-тестом как оригинал, ведь он имеет точно такой же интерфейс, отправляет такие же ответы, но разница в том, что устанавливаются зарание ответы и прописываем поведение такому дублеру. Изолированное тестирование отдельных фрагментов кода и избегание тяжеловесных запросов в БД, потока HTTP-запросов или взаимодействие с файловой системой, за счет грамотного использования test doubles, позволяет упростить запуск юнит-тестов и значительно ускорить их проверку любым раннером тестов.

Тестовые двойники бывают нескольких основных разновидностей, как показано на следующей диаграмме.

![[Pasted image 20250110131407.png]]

## Dummy Object

Чаще всего методы класса или функции нуждаются в каких-то параметрах, но не всегда эти параметры могут быть важны для теста. В таких ситуациях следует использовать Dummy object. По сути — это объект, который передается в метод, но на самом деле не используется, не производит никаких изменений, не вызывает другие методы и не имеет никакого поведения.

Такой объект нужен просто для того, чтобы тест прошел. Очень часто это просто NULL или пустой объект. Dummy object не является как таковым test double, поэтому на картинке выше он и имеет пунктирную границу.

```js
class TestClass {
 public logger(message: string): void {
   console.log(message);
 }
 
 public callAnotherFN(value: string): string {
   this.logger(value);
   return value;
 }
}
 
const instance = new TestClass();
 
it('should call function with Dummy Object', () => {
 jest.spyOn(instance, 'logger');
 const result = instance.callAnotherFN(null);
 expect(instance.logger).toHaveBeenCalled();
 expect(result).toBe(null);
});
```

В данном юнит-тесте нам важно проверить работу функции и то, что она вызывает внутри себя другую. Нам не важен результат ее выполнения, поэтому в качестве Dummy object здесь используется null.

## Test Stub

Stub означает «заглушка». Это объект, содержащий предопределенные данные, которые он использует для ответа на вызовы во время тестов. Он используется, когда мы не можем или не хотим задействовать объекты, которые будут отвечать реальными данными или иметь нежелательные побочные эффекты.

```js
class TestClass {
 public summ(arg1: number, arg2: number): number {
   return arg1 + arg2;
 }
 
 public callAnotherFN(arg1: number, arg2: number): number {
   const result = this.summ(arg1, arg2);
   return result;
 }
}
 
const instance = new TestClass();
 
it('should return stubbed value', () => {
 instance.summ = jest.fn().mockReturnValue(100);
 const result = instance.callAnotherFN(1, 2); // 3
 expect(result).toBe(100);
});
```

В данном примере **Test Stub** — это замена метода `summ` с использованием `jest.fn().mockReturnValue(100)`.

## Test Spy

Test spy — это более функциональная версия Test stub, а его главной задачей является наблюдение и запись данных и/или вызовов во время исполнения теста. Test spy используется для дальнейшей проверки корректности вызова зависимого объекта. Позволяет проверить логику именно тестируемого объекта без проверки зависимых объектов. Так что во многих отношениях Test spy — это просто Test stub с возможностью записи.

```js
class TestClass {
 public summ(arg1: number, arg2: number): number {
   return arg1 + arg2;
 }
 
 public callAnotherFN(arg1: number, arg2: number): number {
   const result = this.summ(arg1, arg2);
   
   return result;
 }
}
 
const instance = new TestClass();
 
it('should call spied function', () => {
 jest.spyOn(instance, 'summ');
 instance.callAnotherFN(1, 2);
 
 expect(instance.summ).toHaveBeenCalled();
});
```

В данном примере test spy является `jest.spyOn(instance, 'summ');`. Так как нам не важно как отработает `summ`,  а интересует, что вызовется функция `summ`.

## Mock Object

Mock Object используется, когда вызывать настоящий метод являеться накладным или когда подобная проверка является слишком затруднительной. Например, если у функции отсутствует возвращаемое значения или нет простого способа проверить изменение состояния системы, то Mock object будет весьма удобен и полезен в таком случае.  

Примером может служить функция, вызывающая службу отправки электронной почты. Нам не нужна фактическая отправка писем на каждый запуск теста, поэтому мы используем Mock Object. Он позволяет нам убедиться, что сервис по отправке писем вызвался необходимое количество раз, а также позволяет проверить параметры, которые использовались во время вызова функции.

## Fake Object

Мы используем Fake Object, чтобы заменить функциональность реального компонента в тесте. Как правило, он реализует те же функции, что и настоящий компонент, но гораздо проще. Наиболее частая причина использования Fake Object заключается в том, что реальный зависимый компонент еще недоступен, слишком медленный или не может использоваться в тестовой среде из-за вредных побочных эффектов. Ярким примером использования Fake Objects являются подмены HTTP-запросов или запросов в БД.

```js
it('returns an object containing all payments', done => {
 // В данном примере мы будем использовать Sinon.js
 const server = sinon.createFakeServer();
 server.respondWith('GET', '/payments', [
   200,
   { 'Content-Type': 'application/json' },
   `[
     { "id": 1, "amount": 1100, "recipient": "Vladyslav" }, 
     { "id": 2, "amount": 230, "name": "Maria" }
   ]`
 ]);
 
 Payments.all().done(collection => {
   const expectedCollection = [
     { id: 1, amount: 1100, recipient: 'Vladyslav' },
     { id: 2, amount: 230, name: 'Maria' }
   ];
 
   expect(collection.toJSON()).to.eql(expectedCollection);
 
   done();
 });
 
 server.respond();
 server.restore();
});
```

В примере создается Fake Object, который ведет себя как сервер, что позволяет протестировать необходимый флоу. Еще частым применением Fake Object являются поддельные платежные системы, которые всегда возвращают успешные платежи.

https://dou.ua/forums/topic/33944/

http://xunitpatterns.com/Test%20Double.html