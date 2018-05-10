### 1

我好奇，当在运行wikipedia那个例子的时候，我们只需要在wikipedia.pull里面指定好类的信息就可以了，那么gobblin在哪里找到的类？

通过查看整个gobblin-dist目录我发现，在lib目录下，有很多的jar包，其中一个jar包的名字是`gobblin-example-0.9.0-642-g13a21ad.jar`。使用如下命令查看到这个jar包的信息：
```shell
➜  lib git->master jar vft gobblin-example-0.9.0-642-g13a21ad.jar 
     0 Fri May 12 09:48:54 CST 2017 META-INF/
    25 Mon May 08 14:52:44 CST 2017 META-INF/MANIFEST.MF
     0 Mon May 08 14:52:44 CST 2017 gobblin/
     0 Mon May 08 14:52:44 CST 2017 gobblin/example/
     0 Mon May 08 14:52:44 CST 2017 gobblin/example/simplejson/
   581 Fri May 12 09:48:54 CST 2017 gobblin/example/simplejson/SimpleJsonConverter$1.class
  3934 Fri May 12 09:48:54 CST 2017 gobblin/example/simplejson/SimpleJsonConverter.class
  4991 Fri May 12 09:48:54 CST 2017 gobblin/example/simplejson/SimpleJsonExtractor.class
  3341 Fri May 12 09:48:54 CST 2017 gobblin/example/simplejson/SimpleJsonSource.class
     0 Mon May 08 14:52:44 CST 2017 gobblin/example/wikipedia/
  1593 Fri May 12 09:48:54 CST 2017 gobblin/example/wikipedia/EmbeddedWikipediaExample$CliFactory.class
  3545 Fri May 12 09:48:54 CST 2017 gobblin/example/wikipedia/EmbeddedWikipediaExample.class
   574 Fri May 12 09:48:54 CST 2017 gobblin/example/wikipedia/WikipediaConverter$1.class
  4268 Fri May 12 09:48:54 CST 2017 gobblin/example/wikipedia/WikipediaConverter.class
  4294 Fri May 12 09:48:54 CST 2017 gobblin/example/wikipedia/WikipediaExtractor$WikiResponseReader.class
 17832 Fri May 12 09:48:54 CST 2017 gobblin/example/wikipedia/WikipediaExtractor.class
  2878 Fri May 12 09:48:54 CST 2017 gobblin/example/wikipedia/WikipediaPartitioner.class
  1367 Fri May 12 09:48:54 CST 2017 gobblin/example/wikipedia/WikipediaSource$1.class
  6159 Fri May 12 09:48:54 CST 2017 gobblin/example/wikipedia/WikipediaSource.class
  1043 Fri May 12 09:46:42 CST 2017 avro-eventhub.job
  1232 Fri May 12 09:46:42 CST 2017 avro-to-mysql.pull
  2305 Fri May 12 09:46:42 CST 2017 distcp-hive.pull
  1289 Fri May 12 09:46:42 CST 2017 distcp.conf
  4081 Fri May 12 09:46:42 CST 2017 google-analytics-unsampledreport-to-avro.pull
  3781 Fri May 12 09:46:42 CST 2017 google-web-master.pull
  2241 Fri May 12 09:46:42 CST 2017 hdfs-monthly-to-hdfs-daily.pull
  1798 Fri May 12 09:46:42 CST 2017 hive-avro-to-orc.pull
  2283 Fri May 12 09:46:42 CST 2017 kafka-console.pull
   202 Fri May 12 09:46:42 CST 2017 simplejson.json.0
   202 Fri May 12 09:46:42 CST 2017 simplejson.json.1
  2422 Fri May 12 09:46:42 CST 2017 simplejson.pull
  1790 Fri May 12 09:46:42 CST 2017 streaming-kafka-console.pull
  2155 Fri May 12 09:46:42 CST 2017 streaming-kafka-kafka.pull
  2028 Fri May 12 09:46:42 CST 2017 streaming-test-kafka.pull
  2019 Fri May 12 09:46:42 CST 2017 wikipedia-console.pull
  3297 Fri May 12 09:46:42 CST 2017 wikipedia-kafka.pull
  2838 Fri May 12 09:46:42 CST 2017 wikipedia-orc.pull
  2058 Fri May 12 09:46:42 CST 2017 wikipedia.pull
  2121 Fri May 12 09:46:42 CST 2017 wikipedia.template
```
### 2

现在要测试`SimpleJson`这个例子，将`simplejson.pull`拷到`gobblin_config_dir`目录下。直接使用命令`bin/gobblin-standalong.sh start`运行之后，在工作目录没有任何输出。

在`simplejson.pull`里面看到这样一句:
```
# comma-separated list of file URIs (supporting different schemes, e.g., file://, ftp://, sftp://, http://, etc)
source.filebased.files.to.pull=
```
可能是因为这里没有指明要操作的文件的原因。于是根据这里提示的逗号分隔，URI文件列表。先填上一个试一试:
```
# comma-separated list of file URIs (supporting different schemes, e.g., file://, ftp://, sftp://, http://, etc)
source.filebased.files.to.pull=file:///home/xxxx/Documents/gobblin-dist/simplejson.json
```

运行命令

`bin/gobblin-standalone.sh start`

之后查看日志，再查看工作输出目录，发现程序运行有输出了

像之前的一样，在工作目录下，能够看到生成的`.avro`文件，在这个目录下运行命令生成json文件:

`java -jar avro-tools-1.8.1.jar tojson --pretty [job_output].avro > output.json`

如果提示找不到jar包就下载一个，然后可以选择放到`.avro`同级目录下。

查看生成的json文件，发现和作为输入的simplejson.json文件内容一样，不知道这个程序要做的是什么。

不过在之前，在gobblin-dist目录下的lib目录下，查看jar包example的内容里可以看到有两个文件:

```
   202 Fri May 12 09:46:42 CST 2017 simplejson.json.0
   202 Fri May 12 09:46:42 CST 2017 simplejson.json.1
```

这两个文件的内容是一样的，我认为是为了区分两个文件，所以才在最后加上`*.json.0`这样的数字来进行区分。
