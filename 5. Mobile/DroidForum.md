# DroidForum
Декомпилируем приложение
```shell
apktool d droidforum.apk
```

В каталоге assets видим файл index.bak.html. Открываем. Внутри закомментирован весь код кроме одной строки в base64. Декодируем, получаем флаг.

Флаг - deteact{b3d837ca3e192e9c0a6e77eb0b268730}