### SimpleJsonConverter

`Converter`的一个实现，它会把输入的schema字符串转换成`Avro`文件。

包含有两个方法:
```java
public Schema convertSchema(String inputSchema, WorkUnitState workUnit)

public Iterable<GenericRecord> convertRecord(Schema schema, String inputRecord, WorkUnitState workUnit)
```
----

#### convertSchema(String inputSchema, WorkUnitState workUnit)

这个方法接收两个参数，一个是字符串类型的`inputSchema`，这个的值是在job配置文件里这一句:
```
source.schema={"namespace":"example.avro", "type":"record", "name":"User", "fields":[{"name":"name", "type":"string"}, {"name":"favorite_number",  "type":"int"}, {"name":"favorite_color", "type":"string"}]}
```
`workUnit`携带了当前这个`workUnit`所有的配置属性。

这个方法的意义在我现在看来，它是把我们传入的`inputSchema`，转换成`Avro`的schema，这样就可以按照`Avro`的schema来写进`Avro`的文件。

#### convertRecord(Schema schema, String inputRecord, WorkUnitState workUnit)

这个方法，我就上面的方法的作用联系起来看的话，那么这个`convertRecord`方法的作用就是根据上面生成的`Avro`的shcema，来把读取到的record转换成对应`Avro`的record。
