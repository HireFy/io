### SimpleJsonExtractor.java

这个extractor使用commons-vfs库来读取指定的存放着json的文件。
包含以下几个方法:
```java
public SimpleJsonExtractor(WorkUnitState workUnitState) throws FileSystemException

public String getSchema()

public String readRecord()

public long getExpectedRecordCount()

public long getHighWatermark()

public void close() throws IOException
```
----

#### SimpleJsonExtractor(WorkUnitState workUnitState)

接收`WorkUnitState`类型的构造函数，首先在代码里先判断有没有`SOURCE_CONN_USE_AUTHENTICATION`验证，有的话就用用户名和密码去获取`SOURCE_FILE_KEY`，因为我之前在运行这个例子的时候，`*.pull`文件里，有就是job的配置文件里，没有填写`SOURCE_CONN_USE_AUTHENTICATION`，默认的话值为`false`，所以就不考虑有验证的情况，没有验证的情况，就接着执行下面这一句:
```java
this.fileObject = VFS.getManager().resolveFile(workUnitState.getProp(SOURCE_FILE_KEY));
```
这里这一句就是得到这个`workUnitState`的source file

下面一句是:
```java
this.bufferedReader =
        this.closer.register(new BufferedReader(new InputStreamReader(this.fileObject.getContent().getInputStream(),
            ConfigurationKeys.DEFAULT_CHARSET_ENCODING)));
```
这一段就是拿到一个上面fileObject的bufferedReader，在后面读取文件的内容

#### getSchema()

```java
public String getSchema() {
return this.workUnitState.getProp(ConfigurationKeys.SOURCE_SCHEMA);
}
```
这段拿到当前`workUnit`的schema，schema定义在了job配置文件的属性source.schema里:
```
# source data schema
source.schema={"namespace":"example.avro", "type":"record", "name":"User", "    fields":[{"name":"name", "type":"string"}, {"name":"favorite_number",  "type    ":"int"}, {"name":"favorite_color", "type":"string"}]}
```

#### readRecord()

这个方法读取下一行
```java
public String readRecord(@Deprecated String reuse) throws DataRecordException, IOException {
    // Read the next line
    return this.bufferedReader.readLine();
  }
```

后面的方法里都没有代码，其实这个simpleJson的例子，在job配置文件里指定了两个source file，一个是simplejson.json.0，另外一个是simplejson.json.1，两个的内容都一样。运行之后在工作目录下生成了两个`.avro`文件，两个文件都转化成json来看，和刚开始的source file一模一样。所以我也不知道它这个例子是想告诉我什么，之所以看这个例子的源码是因为，即使在运行了gobblin之后，看不出什么意义，但是这个例子至少能够告诉我一些运行gobblin的信息。所以才写的。
