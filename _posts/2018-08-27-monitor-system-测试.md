### 创建节点

__POST__    ___localhost:9000/api/v1/node/create___

```json
{
    "name": "今日网络游戏排行榜",
    "createTime": "1535350228",
    "modifyTime": "1535350228",
    "author": "baidu",
    "size": "2",
    "path": "/data/baidu/game"
}
```
### 创建tag

可不可以，tag的属性继承自item，都有`name`, `createTime`, `modifyTime`,那么这些属性是不是可以在创建tag的时候，根据它即将要链接的node来自动填充，然后我们创建tag的时候就可以不用自己来写这些属性，而只用写要添加的特别属性。或者也可以直接不要`name`, `createTime`, `modifyTime`这些属性，因为这个tag到时候会连接它关联的node，我们需要的信息只有它的额外属性。

__POST__    ___localhost:9000/api/v1/tag/create___

```json
{
    "name": "今日网络游戏排行榜",
    "createTime": "1535351209",
    "modifyTime": "1535351209"
}
```
现在创建tag是先创建个空的，之后再添加属性

__POST__    ___localhost:9000/api/v1/tag/properties/25___

```json
{
    "first":"梦幻西游",
    "second":"data2",
    "third":"lol"
}
```
属性在neo4j数据库里面是添加成功了，可是这里有个bug，在postman中返回的信息:
```json
{
    "name": null,
    "id": 25,
    "createTime": null,
    "modifyTime": null,
    "properties": {
        "modifyTime": 1535351209,
        "createTime": 1535351209,
        "name": "今日网络游戏排行榜",
        "id": 25
    }
}
```
properties中的值应该是后面添加的游戏排行信息，这里的properties中的是原来的节点的基础信息。

后面知道为什么了，因为在添加属性的时候先根据id查找到tag，这段代码是这样:
```java
public Tag addProperties(@PathVariable long id, @RequestBody Map<String, Object> fullTag){
        Tag tag = tagService.getById(id);
        return tagService.updateProperties(tag, fullTag);
    }
```
调用了`tagService.getById(id)`，在这个函数里面，会把tag的属性读出来，存在tag的properties里面，然后返回这个tag。在`tagService.updateProperties()`里面，没有这一步操作，所以就是在`getById()`的时候，那时候添加进来的。还有一点就是前面创建tag节点的时候就应该创建一个空的，什么属性也不添加。

这里是不是应该分开来，有一个是单纯的找到这个节点-----`getById()`，返回所有属性的-----`getByIdwithPr()`

或者只根据id来添加属性。`getById()`还是原来的样子，返回带有属性的。(我选择这个)

新创建个tag:

__POST__    ___localhost:9000/api/v1/tag/create___

```json
{
	"properties":null
}
```
创建一个属性为空的tag，然后添加它的属性:

__POST__    ___localhost:9000/api/v1/tag/properties/25___

```json
{                                                                                                         
    "first":"梦幻西游",                                                                                   
    "second":"data2",                                                                                     
    "third":"lol"                                                                                         
}
```

这下postman中能正确返回了:
```json
{
    "name": null,
    "id": 26,
    "createTime": null,
    "modifyTime": null,
    "properties": {
        "third": "lol",
        "first": "梦幻西游",
        "second": "data2",
        "id": 26
    }
}
```
然后在properties中还会看到一个重复的值`id`,把它删了。

### 创建关系

连接刚才创建的Node和tag

创建关系的时候是可以只给定两个id，`fromNodeId`和`toNodeId`这两个是必要的，创建一个属性为空的边:

__POST__    ___localhost:9000/api/v1/edge/create___

```json
{
    "fromNodeId":26,
    "toNodeId":22,
    "properties":null
}
```
也可以在创建关系的时候在加上`name`,`properties`:
```json
{
    "fromNodeId":96,
    "toNodeId":95,
    "name":"Tag",
    "properties":{
	"sdfsdf":123,
	"hsdf":"sdfsdf",
	"sdf":false
     }
}
```
postman返回:
```json
{
    "name": "Tag",
    "id": null,
    "createTime": null,
    "modifyTime": null,
    "fromNodeId": 96,
    "toNodeId": 95,
    "properties": {
        "sdfsdf": 123,
        "hsdf": "sdfsdf",
        "sdf": false
    }
}
```
注意看，返回创建的edge，id是空的，这里要解决。

这里我测试一下通过id查询刚才创建的edge,看下什么状况:

__GET__    ___localhost:9000/api/v1/test/find/edge/22___

```json
{
    "name": null,
    "id": 22,
    "createTime": null,
    "modifyTime": null,
    "fromNodeId": null,
    "toNodeId": null,
    "properties": {
        "sdf": false,
        "hsdf": "sdfsdf",
        "sdfsdf": 123
    }
}
```
通过id来查找edge的时候，发现返回的json中，除了`id`和`properties`其他的都不知道，`name`, `fromNodeId`和`toNodeId`应该是必须知道的，在上面创建edge的时候postman可是返回得有`name`, `fromNodeId`和`toNodeId`这些属性的，这里是有问题的。

根据id查找edge的时候，用到的`joinQueryEdgeProById(Long id)`的函数，函数里有这样的语句`return "MATCH (a)-[r]->(b) WHERE id(r)=" + id + " RETURN properties(r)";`可以看到这里的语句只返回了properties(r)，所以这样的查询语句我们只能拿到edge的的属性，只能看到properties中的属性，看不到它的自身的一些属性，我们能看到的就像下面图中的样子:

![edge_properties](/images/edge_properties.png)

把这个语句改成`return "MATCH (a)-[r]->(b) WHERE id(r)=" + id + " RETURN properties(r), id(a), id(b), TYPE(r)";`就能拿到刚才所想要的属性。相应的其他地方把相应的属性装好就行了。
