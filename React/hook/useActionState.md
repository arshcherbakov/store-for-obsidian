Это хук, который позволяет обновлять состояние на основе результата действия формы.

В ранних версиях назывался (useFormState)

```js
const [state, formAction, isPending] = useActionState(fn, initialState, permalink?);
```

При использовании с функцией сервера `useActionState`позволяет отображать ответ сервера на отправку формы даже до завершения гидратации.

- `fn`: Функция, которая будет вызвана при отправке формы или нажатии кнопки. Когда функция вызывается, она получит предыдущее состояние формы (сначала то, `initialState`что вы передаете, затем ее предыдущее возвращаемое значение) в качестве своего начального аргумента, за которым следуют аргументы, которые обычно получает действие формы. - Функция, переданная в, `useActionState`получает дополнительный аргумент, предыдущее или начальное состояние, в качестве своего первого аргумента. Это делает ее сигнатуру отличной от той, если бы она использовалась напрямую как действие формы без использования `useActionState`.
  
- `initialState`: Значение, которое вы хотите, чтобы состояние было изначально. Это может быть любое сериализуемое значение. Этот аргумент игнорируется после первого вызова действия.

- - **необязательно** `permalink` : строка, содержащая уникальный URL-адрес страницы, которую изменяет эта форма. Для использования на страницах с динамическим содержимым (например, лентами) в сочетании с прогрессивным улучшением: если `fn`это [серверная функция](https://react.dev/reference/rsc/server-functions) и форма отправлена ​​до загрузки пакета JavaScript, браузер перейдет к указанному URL-адресу постоянной ссылки, а не к URL-адресу текущей страницы. Убедитесь, что тот же компонент формы отображается на целевой странице (включая то же действие `fn`и `permalink`), чтобы React знал, как передать состояние. После гидратации формы этот параметр не имеет никакого эффекта.
  
#### Возвраты[](https://react.dev/reference/react/useActionState#returns "Ссылка для возврата")

`useActionState`возвращает массив со следующими значениями:

1. Текущее состояние. Во время первого рендеринга оно будет соответствовать `initialState`переданному вами. После вызова действия оно будет соответствовать значению, возвращаемому действием.
2. Новое действие, которое можно передать в качестве `action`свойства вашему `form`компоненту или `formAction`свойству любому `button`компоненту в форме.
3. Флаг `isPending`, сообщающий о наличии ожидаемого перехода.

```js

// file action.js
"use server";

export async function addToCart(prevState, queryData) {
  const itemID = queryData.get('itemID');
  if (itemID === "1") {
    return "Added to cart";
  } else {
    // Add a fake delay to make waiting noticeable.
    await new Promise(resolve => {
      setTimeout(resolve, 2000);
    });
    return "Couldn't add to cart: the item is sold out.";
  }
}

import { useActionState, useState } from "react";
import { addToCart } from "./actions.js";

function AddToCartForm({itemID, itemTitle}) {
  const [message, formAction, isPending] = useActionState(addToCart, null);
  
  return (
    <form action={formAction}>
      <h2>{itemTitle}</h2>
      <input type="hidden" name="itemID" value={itemID} />
      <button type="submit">Add to Cart</button>
      {isPending ? "Loading..." : message}
    </form>
  );
}

export default function App() {
  return (
    <>
      <AddToCartForm itemID="1" itemTitle="JavaScript: The Definitive Guide" />
      <AddToCartForm itemID="2" itemTitle="JavaScript: The Good Parts" />
    </>
  )
}
```


``