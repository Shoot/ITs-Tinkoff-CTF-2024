Судя по коду, флаг лежит в таблице `core_parameters` в строке с `name='DEBUG_KEY'`

Уязвимость 1. Обращаем внимание, что используется старая версия Django 4.0.2. Смотрим changelog Django и находим сообщение об исправлении SQL-инъекции в более поздней версии: https://docs.djangoproject.com/en/5.0/releases/4.0.6/. Уязвимый код используется на странице `/users/`, но она требует авторизации.

Уязвимость 2. Страница /users прикрыта кодом, который пропускает запросы с IP 127.0.0.1, при этом не проверяется, что пользователь залогинен.

Уязвимость 3. Код загрузки аватара по ссылке позволяет отправить GET-запрос на любой адрес, в том числе на 127.0.0.1. Там есть проверка, что адрес заканчивается на расширение картинки, но его можно легко обойти, добавив `?.jpg` в конце адреса.

Как воспользоваться уязвимостями:

1. Создаём пользователя обычным образом. Для примера его username будет `HACKER`.

2. Загружаем ему аватарку по этому URL:

http://127.0.0.1:8000/users/?joined=year'%20FROM%20date_joined)FROM%20core_user%3BUPDATE%20core_user%20SET%20last_name%3D(SELECT%20value%20FROM%20core_parameter%20WHERE%20name%3D'DEBUG_KEY')%20WHERE%20username%3D'HACKER'--&.jpg

Обратите внимание что в адресе в конце указано имя нашего пользователя.

3. Идём на страницу `/profile` и видим там флаг.
