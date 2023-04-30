# BugBounty platform 3
При логине устанавливается Cookie session, который содержит jwt. С помощью hashcat можно сбрутить secret для подписи
``` shell
hashcat -a 0 -m 16500 jwt.txt ./rockyou.txt
```
secret - `1122334455`.

Теперь можно изменить в jwt `"username": "admin"` и подписать. Подменив cookie заходим на страницу /hacker и видим данные пользователя admin и кнопку "Secret area" нажав на которую получаем флаг

Флаг - deteact{7fb5ef652f60edca8dbdbb698d0c7291}