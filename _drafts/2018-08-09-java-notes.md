java中的那些集合类型，判断为为空有两个方法，一个是`isEmpty()`，一个是判断等不等于`null`，区别在于，前者判断集合中的内容，元素是不是一个也没有，而后者是判断当前这个集合有没有分配内存空间。

```java
private Set<Person> follows;

public void addFollow(Person person) {
	if (follows == null) {
	    follows = new HashSet<>();
	}
	follows.add(person);
}
```
之前我判断那里用的是`if(follows.isEmpty())`，然后就抛出空指针异常，应该使用`null`判断有没有分配内存空间。
