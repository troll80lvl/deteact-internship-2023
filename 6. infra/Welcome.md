# Welcome
Получаем файл dump.pcapng содержащий дамп сетевого трафика.
Открываем wireshark, применяем фильтр "http.request.method == GET" и видим множество запросов на IP адрес 5.188.117.170.
В фрейме №17123 выполняется GET запрос к этому IP с ответом 401 Unauthorized в фрейме 17126.
Следущий запрос в фрейме №17489 содержит заголовок `Authorization: Basic dXNlcjpkZXRlYWN0e2ZkNmZiN2M2ZGIzOWViZDIxNDJlNmQxMDM4NDY0ZWQ0fQ==`. В ответ получает 302 Found. 
Wireshark умеет декодировать base64, из чего видим пару логин:пароль - `user:deteact{fd6fb7c6db39ebd2142e6d1038464ed4}`.

Флаг - deteact{fd6fb7c6db39ebd2142e6d1038464ed4}