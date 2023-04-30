# DroidForum Admin
В `AndroidManifest.xml` находим что приложение обрабатывает Deeplink. Можно вызвать обработку ссылки следующем образом:
```shell
adb shell am start-activity -W -a android.intent.action.VIEW -d "droidforum://forum/admin  
?token=//host/path" com.example.droidforum
```

Это позволит попасть на экран Admin Panel, но там пусто. Изучая декомпилированный код классов DeeplinkActivity и AdminForumActivity узнаем что после того как DeeplinkActivity проверяет intent создаётся новый intent, в свойство Extra помещается значение token. Но свойство data в таком случае пустое. А в классе AdminForumActivity происходит вызов `getData()`, которое возвращает `null`.

Пишем скрипт hook.js который добавит нужный uri в свойство data:
```js
Java.perform(() => {
  console.log("\n[ * ] Starting hack...");
  const adminClass = Java.use('com.example.droidforum.AdminForumActivity');
  const intentClass = Java.use('android.content.Intent');
  const Uri = Java.use('android.net.Uri');

  adminClass.onCreate.implementation = function(input) {
    console.log('\n[ * ]: ' + this.getClass());

    let intent = this.getIntent();
    console.log('[ Intent input ]: ' + intent);

    let data = Uri.parse('/admin?token=c429592708a3f9a57866373e36b52e71');
    intent.setData(data);
    console.log('[ Intent mod ]: ' + intent);

    return this.onCreate(input);
  }
})
```

Теперь запускаем приложение на эмуляторе. 
В одном окне терминала:
```shell
adb shell am start -W -n com.example.droidforum/.MainActivity && frida -U -F -l ./hook.js
```
Во втором окне терминала:
```shell
adb shell am start-activity -W -a android.intent.action.VIEW -d "droidforum://forum/admin?token=//host/admin" com.example.droidforum
```

В эмуляторе видим экран приложения Admni Panel с флагом.

Флаг - deteact{8d125dc38213ea6237cdec4b3824a9b2}

В исходном коде так же можно найти ссылку `http://5.188.117.170:40080/encrypted_flag5748317582` запрос по которой возвращает 404. Перехватив запрос из приложения в burp видно `User-Agent: okhttp/4.9.1`. В декомпилированном коде есть пакет okhttp3. 
Можно было догадаться подставить в user-agent okhttp и получить защифрованный флаг напрямую от сервера. И в cyberchef расшифровать ключом, который передается в параметре token.