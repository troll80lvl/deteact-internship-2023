# The Developer's site
Цель - `http://45.131.97.54:1337/`

Путем перебора каталогов был найден каталог `.git`.
```shell
ffuf -c -u http://45.131.97.54:1337/ -w SecLists/Discovery/Web-content/common.txt
```

С помощью инструмента GitHacker извлечен репозиторий.
В файле `.git/logs/HEAD` заметил откат к ранней версии проекта. Выполнив `git diff 9ec858c7978308fd69d8161e11a0cc729038bc42 b766a77c19149ea0dc2f126d8a791d7dc3c5bfe2` был обнаружен флаг.

Флаг - deteact{fc9403e6e7f917e5bac324c92cc5d5bb}