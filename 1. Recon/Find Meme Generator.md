# Find Meme Generator
У адреса `https://45.131.97.54/` есть ssl сертификат выданный на имя `intern.deteact.local`.
Поместив `intern.deteact.local` в заголовок Host сервер показал "Not here" вместо 404.

Пробуем перебрать виртуальные хосты:
```shell
ffuf -c -u https://45.131.97.54/ -H "Host: FUZZ.deteact.local" -w shubs-subdomains.txt
```

Находим поддомен `memes.deteact.local`. Делаем запрос:
```shell
curl https://45.131.97.54/ -H "Host: memes.deteact.local"
```
Видим комментарий с флагом.

Флаг - deteact{76f856b12f993eabb18c88d56fafa270}