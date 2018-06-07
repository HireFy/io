wikipedia这个例子里，它获取的数据主要分成了两个内容，这两个内容是两个不同title的页面的修改信息。然后在源码里面就根据不同的URN来创建WorkUnits，所以这里我就打算把原来的两个作为title的URN，现在转换成两本图书的不同id。它们分别是`1003078`和`1003077`。`GET`方法像这样就能请求到数据:

```
GET https://api.douban.com/v2/book/1003078

GET https://api.douban.com/v2/book/1003077
```


