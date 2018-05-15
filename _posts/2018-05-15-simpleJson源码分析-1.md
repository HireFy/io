### SimpleJsonSource.java
这个类实现了`Source<String, String>`接口，`Source`为每一个要提取的文件创建`WorkUnit`，然后用`SimpleJsonExtractor`去执行提取的操作。

该类重写了三个方法:
```java
public List<WorkUnit> getWorkUnits(SourceState state)

public Extractor<String, String> getExtractor(WorkUnitState state)

public void shutdown(SourceState state)
```

------

#### getWorkUnits方法
在`getWorkUnits`这个方法里面，这个方法返回`WorkUnit`集合。

方法里的第一句是创建了空的`WorkUnit`的list，紧接着，下一句这样写的:
```java
if (!state.contains(ConfigurationKeys.SOURCE_FILEBASED_FILES_TO_PULL)) {
      return workUnits;
    }
```

这一句是对应`.pull`文件里这一句，检查有没有填写要pull的文件:
```
...
# source configuration properties
# comma-separated list of file URIs (supporting different schemes, e.g., file://, ftp://, sftp://, http://, etc)
source.filebased.files.to.pull=
...
```

如果不存在的话就直接返回刚才创建的空的`workUnit`list。

state是`SourceState`类型的，`SourceState`继承自`State`。`SourceState`容纳了一个source所有的元数据，也就是说包含了所有定义在job配置文件中的属性，还有目前运行的task的所有属性。

`state.contains()`方法返回一个布尔值，判断一个属性是否被设置。

```java
Extract extract = new Extract(Extract.TableType.SNAPSHOT_ONLY,
        state.getProp(ConfigurationKeys.EXTRACT_NAMESPACE_NAME_KEY, "ExampleNamespace"), "ExampleTable");
```

一个`Extract`的构造方法，这里还不清楚`TableType.SNAPSHOT_ONLY`的作用和意义，后面的一个参数是返回state的名称空间，如果在job配置文件中没有配置的话，就使用`ExampleNamespace`。最后一个参数是table name。

```java
String filesToPull = state.getProp(ConfigurationKeys.SOURCE_FILEBASED_FILES_TO_PULL);
for (String file : Splitter.on(',').omitEmptyStrings().split(filesToPull)) {
      // Create one work unit for each file to pull
      WorkUnit workUnit = WorkUnit.create(extract);
      workUnit.setProp(SOURCE_FILE_KEY, file);
      workUnits.add(workUnit);
    }
```

这里根据在job配置文件中定义的要pull的文件，为每个文件创建一个`workUnit`，同时再设置一个`SOURCE_FILE_KEY`的属性，这样就设置了一个Source file。(`WorkUnit`也是继承自`State`，所以可以调用`setProp()`方法)，之后返回`workUnits`。

上面的`workUnit.setProp(SOURCE_FILE_KEY, file);`设置了一个属性，`SOURCE_FILE_KEY`的值在代码开始的时候声明过
```java
public static final String SOURCE_FILE_KEY = "source.file";
```
然后，如果我的配置文件像下面这样填写的话，那么上面设置的一个`workUnit`的属性类似于`source.file=file:///home/Documents/xxxxx.json.0`

```
...
# source configuration properties
# comma-separated list of file URIs (supporting different schemes, e.g., file://, ftp://, sftp://, http://, etc)
source.filebased.files.to.pull=file:///home/Documents/xxxxx.json.0,file:///home/Documents/xxxxx.json.1
...
```

#### getExtractor方法

返回一个new SimpleJsonExtractor(state)

#### shutdown方法

这个方法没有写代码，在这里什么也不做
