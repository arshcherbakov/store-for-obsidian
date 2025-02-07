Если злоумышленник добавит вредоносный код (например, JavaScript) в качестве текста в инпут, он может быть выполнен в браузере других пользователей.

Если пользователь вводит:
```html
<script>alert('Hacked!')</script>
```

И это значение используется в HTML напрямую:
```html
<input value="<script>alert('Hacked!')</script>">
```

Браузер выполнит `<script>` как JavaScript, и появится всплывающее окно с сообщением "Hacked!". 
Чтобы предотвратить это, нужно сделать так, чтобы script при вставке не выполнился. Превратить код в текст.  Используется экранирование:
- `<` → `&lt;`
- `>` → `&gt;`
- `"` → `&quot;`
- `'` → `&#039;`
- `&` → `&amp;`
  
```html
<input value="&lt;script&gt;alert('Hacked!')&lt;/script&gt;">
```

Теперь браузер покажет текст `<script>alert('Hacked!')</script>`, а не выполнит его как код.
Пример реализации функции экранирования 

```js
const escape = (text) => {
	const htmlEscapes = {
	'&': '&amp;',
	'<': '&lt;',
	'>': '&gt;',
	'"': '&quot;',
	"'": '&#039;'
	};
	
	return text.toString().replace(/[&<>"']/g, (char) => htmlEscapes[char]);
}
```

Также это защищает от вставке в html спомощью innerHtml, который не экранирует данные. Желательно вместо него использовать Node.textContent. Этот метод потребляет гораздо меньше ресурсов, так как текст парсится как текст, а не HTML. Кроме того, это защищает от XSS атак.

Существует два типа XSS атак: серверная и клиентская. При этом и серверные и клиентские XSS могут быть как хранимыми, так и отраженными.

![[Pasted image 20241213112202.png]]

**Серверный XSS возникает,** когда данные из ненадежного источника включаются в HTTP-ответ сервера. Источником этих данных может быть пользовательский ввод, переданный с текущим HTTP-запросом, и тогда мы получим отраженный серверный XSS. А если в ответе используются сохраненные на сервере данные, ранее полученные от пользователя, реализуется хранимый серверный XSS. В этом случае уязвимость находится в коде на стороне сервера, а браузер просто отображает ответ и выполняет содержащийся в ответе скрипт.
**Клиентский XSS возникает**, когда ненадежные данные используются для обновления DOM с помощью небезопасного вызова JavaScript (например, с помощью innerHTML). Вызов JavaScript считается небезопасным, если его могут использовать для внедрения кода в DOM. Источником данных также может быть запрос или сохраненная информация. Таким образом, возможен как Reflected Client XSS, так и Stored Client XSS.

### Хранимые

В этом случае внедренный вредоносный скрипт постоянно хранится в базе данных веб-приложения. Сервер выполняет его, когда пользователь посещает сайт. Код JavaScript будет выполняться браузером так же, как он записан в базе данных приложения, поэтому эта атака не требует никаких действий со стороны пользователя.

### Отраженные

Отраженные XSS появляются, когда веб-приложение получает от пользователя данные, а в ответ выводит их небезопасным образом. Это может привести к тому, что злоумышленник передаст в браузер код, который будет немедленно исполнен.

Вот ключевые моменты, которые помогут реализовать комплексную защиту от XSS:

1. **Контекстно-зависимое кодирование**. Кодируйте и экранируйте данные при выводе на страницу в соответствии с контекстом вывода. 
    
2. **Валидация**. Проводите строгую проверку полученных от пользователей данных, ограничивая их типы и форматы.
    
3. **Флаги Cookies**. Используйте флаги HttpOnly и Secure для cookies, чтобы защитить их от доступа через клиентские скрипты и разрешить передачу только через зашифрованные соединения.
    
4. **Content Security Policy (CSP)**. Настройте CSP, чтобы предотвратить загрузку вредоносных скриптов на ваши страницы, ограничивая источники, с которых можно загружать скрипты.

### Как защититься от чтения произвольных файлов

1. **Проверяйте пользовательский ввод**, чтобы предотвратить нежелательные запросы к файлам. Проверяйте допустимость имен файлов, ограничивайте символы, проверяйте пути.
    
2. **Используйте безопасные методы доступа к файлам.** Вместо прямого формирования пути к файлу, используйте безопасные методы для доступа к файлам или используйте специализированные библиотеки.
    
3. **Убедитесь, что ваше приложение или сервис работает с минимально необходимыми правами**. 
    
4. **Разделите приложения и их данные**, чтобы предотвратить утечку информации между различными частями системы.
    
5. **Настройте системы мониторинга**, чтобы отслеживать необычные или подозрительные действия. Это может помочь обнаружить атаку и вовремя на нее среагировать.
    
6. **Обучите разработчиков принципам написания безопасного кода**, чтобы уменьшить вероятность ошибок, приводящих к уязвимостям.

Полезно: https://habr.com/ru/companies/StartX/articles/787176/
