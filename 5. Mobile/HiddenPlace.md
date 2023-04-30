# HiddenPlace
Запускаем приложение и frida-server на эмуляторе.
С помощью frida подключаемся к приложению:
``` shell
frida-trace -U -F -j 'com.deteact.hiddenplace.*!*'
```

Видим что шифрование происходит в классе `com.deteact.hiddenplace.util.CryptoKt`.
Так же видим зашифрованный флаг и ключ в base64.

Декомпилируем приложение:
``` shell
jadx-gui hiddenplace.apk
```

Изучая исходный код пишем скрипт hook.js:
```js
Java.perform(() => {
  console.log("\n[ * ] Starting hack...");

  const b64 = Java.use('android.util.Base64');
  const Cipher = Java.use('javax.crypto.Cipher');
  const crypto = Java.use('com.deteact.hiddenplace.util.CryptoKt');

  crypto.encrypt.overload('java.lang.String', 'javax.crypto.SecretKey', 'java.lang.String')
  .implementation = function(inputText, key, alg) {
    console.log('\n[ * ]: util.CryptoKt.encrypt');
    let flag = 'A+szE4mZZKF9YbZ/YUMl2vhzD8LzMsISgevWKiFQvnQ9KyE2NcFwzuSCrTLOr7ND';
    console.log('[encoded flag]: ' + flag);
    
    let cipher = Cipher.getInstance(alg);
    cipher.init(2, key);
    console.log('[decrypt flag]: ' + b64.encodeToString(cipher.doFinal(b64.decode(flag, 2)), 2));

    return this.encrypt(inputText, key, alg);
  }
})
```

Подключаем скрипт с помощью фрида к приложению:
`frida -U -F -l ./hook.js`

Теперь при вводе в эмуляторе произвольного пароля в терминал пишется расшифрованный флаг в base64.
frida output:
```
[ * ]: util.CryptoKt.encrypt  
[encoded flag]: A+szE4mZZKF9YbZ/YUMl2vhzD8LzMsISgevWKiFQvnQ9KyE2NcFwzuSCrTLOr7ND  
[decrypt flag]: ZGV0ZWFjdHtkM2VmMzRlMjk2YjEzZjdmMjU5NTQ5NjJlZTZiNjhiNn0=
```
Декодируем base64 и получаем флаг.

Флаг - deteact{d3ef34e296b13f7f25954962ee6b68b6}