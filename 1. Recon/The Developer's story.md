# The Developer's story
Цель - `http://45.131.97.54:1337/`

В каталоге .git был обнаружен файл `.git/logs/HEAD` содержащий email разрабочика - `127932315+sergeythedeveloper@users.noreply.github.com`. 

Изучая его историю коммитов на гитхабе - `github.com/sergeythedeveloper` - нашел репозиторий `https://github.com/Secret-unit-of-The-Internship/super-palm-tree`  за 2013 год. Репозиторий содержал две ветки - main и secret. Переключившить на ветку secret увидел файл secret.js.

Просматривая историю коммитов нашел массив содержащий флаг по-символьно `https://github.com/Secret-unit-of-The-Internship/super-palm-tree/commit/966f29904edb897ca331ec17195fbbe6e8e74c9f`.

С помощью python сорбал флаг:
``` python
f = ['d', 'e', 't', 'e', 'a', 'c', 't', '{', '6', '6', '2', '8', 'e', '6', '3', 'f', 'f', '0', 'e', '7', 'e', 'd', '4', '5', '1', 'f', '7', '6', '0', 'b', '3', '2', '5', '5', 'a', '1', 'c', '0', '8', 'a', '}']

''.join(f)
```

Флаг - deteact{6628e63ff0e7ed451f760b3255a1c08a}
