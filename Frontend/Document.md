Node.textContent - Позволяет задавать или получать текстовое содержимое элемента и его потомков.

Node.innerHTML - возвращает HTML. Довольно часто `innerHTML` используется для получения или записи текста в элемент. Тем не менее, вместо него желательно использовать `textContent`: этот метод потребляет гораздо меньше ресурсов, так как текст парсится как текст, а не HTML. Кроме того, это защищает от XSS атак.

Node.innerText -  был введён Internet Explorer-ом. Работает по тому же принципу за небольшими исключениями:

- `textContent` получает содержимое _всех_ элементов, включая [`<script>`](https://developer.mozilla.org/ru/docs/Web/HTML/Element/script) и [`<style>`](https://developer.mozilla.org/ru/docs/Web/HTML/Element/style), тогда как `innerText` этого не делает.
- `innerText` умеет считывать стили и не возвращает содержимое скрытых элементов, тогда как `textContent` этого не делает.
- Метод `innerText` позволяет получить CSS, а `textContent` — нет.

