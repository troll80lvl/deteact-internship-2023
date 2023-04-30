# Password generator 1
Изучая исходный код видно что, в методе `register` класса `Database` в запросе есть sql-inj.

Выполнив POST запрос к /register с параметрами `username=a&password=pass'),+('admin',+'lul',+'test` можно добавить двух пользователей, первый с ролью `user`, второй с ролью `admin`.

После вошел как `lul:test` и получил флаг.

Флаг - deteact{4804ea3c290391280973c65db196f8d0}