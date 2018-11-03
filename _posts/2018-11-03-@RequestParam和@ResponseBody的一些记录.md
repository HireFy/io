当传输的数据为键值对的时候，且后端接收注解为@RequestParam的时候
```js
function isMailExist() {
    mail_addr = mail.val()
    $.ajax({
        type: "post",
        url: "/mail/exist",
        data: {
            "mail":mail_addr
        },
        dataType: "json",
        error: function (error) {
            console.log(error)
        },
        success: function (data) {
            console.log("success " + data)
            return data
        }
    })
}
```
后端接收参数:`@RequestParam("mail") String mail`

服务器显示接收到的mail的值为:994831363@qq.com

------

当js中把data改成mail\_addr后,data不再是键值对，而注解依旧是@RequestParam。
```js
...
url: "/mail/exist",
data: mail_addr,
dataType: "json",
...
```

后端接收参数:`@RequestParam("mail") String mail`

服务器报错`MissingServletRequestParameterException: Required String parameter 'mail' is not present`

----

当data不再是键值对，且设置contentType为"application/json", 注解依然是@RequestParam


```js
...
contentType:"application/json",
url: "/mail/exist",
data: mail_addr,
dataType: "json",
...
```

后端接收参数:`@RequestParam("mail") String mail`

服务器报错`MissingServletRequestParameterException: Required String parameter 'mail' is not present`

----

当data不再是键值对，且contentType为"application/json",注解改为@RequestBody


```js
...
contentType:"application/json",
url: "/mail/exist",
data: mail_addr,
dataType: "json",
...
```

后端接收参数:`@RequestBody String mail`

后端接收的mail的值为994831363@qq.com

----

当data是键值对，且contentType为"application/json",注解改为@RequestBody

```js
...
url: "/mail/exist",
contentType:"application/json",
data:{
    "mail":mail_addr
},
dataType: "json",
...
```

后端接收参数:`@RequestBody String mail`

后端接收的mail的值为"mail=994831363%40qq.com"

----

当data是键值对，且转换成json字符串，且contentType为"application/json",注解改为@RequestBody

```js
...
url: "/mail/exist",
contentType:"application/json",
data:JSON.stringify(mail_addr),
dataType: "json",
...
```

后端接收参数:`@RequestBody String mail`

后端接收到的mail的值:""994831363@qq.com""

