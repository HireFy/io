好奇`WikipediaExtractor`这个类里面到底做了什么，以便更好的写出自己的`Extractor`。

首先它定义了一堆常量，这些常量有些是定义在`.pull`文件里的属性，有些是用来解析json数据的。

首先，它先定义了一个内部类--`WikiResponseReader`，继承自`Iterator<JsonElement`，主要重写了`hasNext()`和`next()`方法，在里面调用了一个方法`retrievePageRevisions()`

接着就是一系列的封装了，慢慢缕清:

retrievePageRevisions(Map<String, String>):Queue<JsonElement>

调用performHttpQuery(this.rootUrl, query)来获得请求之后接受到的json数据
performHttpQuery(String, Map<String, String>):JsonElement

调用createHttpRequest(rootUrl, query)来获得http请求
createHttpRequest(String, Map<String, String>):HttpUriRequest

调用createRequestURI(rootUrl, query)来编码请求的字符(我看是这样)
createRequestURI(String, Map<String, String>):URI

之后在performHttpQuery这里收到服务器的响应，并把响应的内容转换成JsonElement类型，发送给retrievePageRevisions。在retrievePageRevisions中进行一系列的检查后，将JsonElement类型的数据返回。

不过我现在觉得最重要的是，在`Extractor`里面要设置lowWatermark和setActualHighWatermark这点很重要。
