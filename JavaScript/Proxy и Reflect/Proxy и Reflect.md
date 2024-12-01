[`Proxy`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy) - это объект, позволяющий сделать обертку над объектом, в котором можно переопределить стандартное поведение, например, обращение к свойству объекта. 
Это очень похоже на [eventListner'ы](https://developer.mozilla.org/en-US/docs/Web/API/EventTarget/addEventListener) из [Browser'ного API](https://developer.mozilla.org/en-US/docs/Web/API), поскольку с помощью [Proxy](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy) мы можем привязаться и, при необходимости перехватить нужное нам событие у объектов, классов, функций, и пр. 
Полный список методов, которые можно перехватывать, перечислен в [спецификации Proxy](https://tc39.es/ecma262/#sec-proxy-object-internal-methods-and-internal-slots)

[`Reflect`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Reflect) - это **объект, который предоставляет методы**, для работы с объектом, в нем дублируются некоторые методы из `Object`

