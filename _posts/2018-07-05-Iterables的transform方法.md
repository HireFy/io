```java
Iterable<LongWatermark> watermarks =
          Iterables.transform(entry.getValue(), new Function<WorkUnitState, LongWatermark>() {
        @Override
        public LongWatermark apply(WorkUnitState wus) {
          return wus.getActualHighWatermark(LongWatermark.class);
        }
      });
```
这是WikipediaSource.java中getWorkunits方法中的一句，这里学习下Iterables.transform的用法

Iterables是Guava中的类，Guava是google推出的一套java核心库。

transform方法接收两个参数，一个是fromIterable，一个是
