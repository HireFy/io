### Job Configurations Basics

Job配置文件是一个以`.pull`或`.job`为后缀的文本文件，它定义的job属性可以被Java [Properties](http://docs.oracle.com/javase/7/docs/api/java/util/Properties.html) 对象加载。在[这里](https://github.com/linkedin/gobblin/tree/master/gobblin-core/src/main/resources)会看到几个Gobblin的job配置文件。

通常job配置文件有下面几个属性。在[Configuration Properties Glossary](http://gobblin.readthedocs.io/en/latest/user-guide/Configuration-Properties-Glossary)能看到更多关于Gobblin配置属性的信息。

+ `job.name`: job的名称
+ `job.group`: job所属的组
+ `source.class`: job要用到的`Source`类
+ `converter.classes`:job中要用的`Converter`类，以逗号分隔,可选。
+ Quality checker related configuration properties: a Gobblin job typically has both row-level and task-level quality checkers specified. Please refer to Quality Checker Properties for configuration properties related to quality checkers.

### Hierarchical Structure of Job Configuration Files

It is often the case that a Gobblin instance runs many jobs and manages the job configuration files corresponding to those jobs. The jobs may belong to different job groups and are for different data sources. It is also highly likely that jobs for the same data source shares a lot of common properties. So it is very useful to support the following features:

+ Job configuration files can be grouped by the job groups they belong to and put into different subdirectories under the root job configuration file directory.

+ Common job properties shared among multiple jobs can be extracted out to a common properties file that will be applied into the job configurations of all these jobs.

Gobblin supports the above features using a hierarchical structure to organize job configuration files under the root job configuration file directory. The basic idea is that there can be arbitrarily deep nesting of subdirectories under the root job configuration file directory. Each directory regardless how deep it is can have a single .properties file storing common properties that will be included when loading the job configuration files under the same directory or in any subdirectories. Below is an example directory structure.

```
root_job_config_dir/
  common.properties
  foo/
    foo1.job
    foo2.job
    foo.properties
  bar/
    bar1.job
    bar2.job
    bar.properties
    baz/
      baz1.pull
      baz2.pull
      baz.properties
```

在这个例子中，`common.properties`会在加载`foo1.job`,`foo2.job`,`bar1.job`,`bar2.job`,`baz1.pull`,`baz2.pull`的时候被included。`foo.properties`会在加载`foo1.job`和`foo2.job`的时候被`included`,并且在这里设置的属性会覆盖在`common.properties`中定义的属性。同样的，`bar`文件夹下也是一样的情况，`baz`文件夹下，`baz.properties`的属性设置会覆盖`bar.properties`和`common.properties`中相同的属性。
