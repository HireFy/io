下面的一些笔记是在看这个网站[https://www.w3cschool.cn/wkspring/](https://www.w3cschool.cn/wkspring/)看到的，然后就想着看的时候边敲代码，边做笔记学习一下(其实写这个的时候已经是第二次看了)

+ Spring通过依赖注入(DI)来管理组成一个应用程序的组件，这些对象被称为Spring Beans，Spring容器会创建对象，把它们链接在一起，配置它们，并且管理它们的整个生命周期。

+ Spring提供了两个不同类型容器: Spring BeanFactory和Spring ApplicationContext。BeanFactory是最简单的容器，比较轻量，提供了DI基本的支持。ApplicationContext能从属性文件解析文本信息，包含了BeanFactory的所有功能。

+ BeanFactory接口有一个实现就是XmlBeanFactory，它能从属性文件读取配置

+ 被称作 bean 的对象是构成应用程序的支柱也是由 Spring IoC 容器管理的。bean 是一个被实例化，组装，并通过 Spring IoC 容器所管理的对象。

+ 凡是写在了配置文件xml中的bean，即使没有在程序中声明，程序运行的时候也会创建一个它的实例

### Bean的生命周期

+ Bean的作用域，当创建Bean的时候，属性scope设置为prototype，在每次创建Bean对象的时候，都会返回一个新的bean实例，属性scope设置为singleton，每次创建创建Bean对象的时候，返回同一个实例。

+ 在Bean的生命周期中，在Bean开始创建的时候和销毁的时候，可以调用自定义的void无参方法，注意在销毁一个Bean的时候，要使用AbstractApplicationContext类的registerShutdownHook()方法，它会确保正常关闭，并调用相关destroy方法

+ Bean的后置处理器，实现了BeanPostProcessor接口的Bean，会被注册为后置处理器，然后在容器中创建这个Bean(也就是说，在配置文件中要声明这个Bean)，然后会在适当的时候调用它，比如一个bean开始初始化和初始化完成的时候

### Bean的继承

+ Bean定义继承，bean 定义可以包含很多的配置信息，包括构造函数的参数，属性值，容器的具体信息例如初始化方法，静态工厂方法名，等等。
子 bean 的定义继承父定义的配置数据。子定义可以根据需要重写一些值，或者添加其他值。

  在xml文件中，通过设置bean的parent属性指定父类bean的id，完成继承这一操作

  甚至可以定义一个模板的bean，设置abstract值为true，然后他就可以作为一个模板来供继承它的子类使用:

![模板Bean](../images/bean_template.png)

### DI(依赖注入)

DI主要有两种变体，一个是基于构造函数，另外一个是基于setter方法。

+ 基于构造函数，在要注入依赖的bean里添加<constructor-arg>标签，如果要传递一个引用，就使用标签的ref属性，但是如果想要直接传递一个值的话，就使用value属性。

+ 基于setter方法，同样的不过是添加<property>标签，引用的话使用ref属性，传递值的话value属性

### 自动装配

+ byName

+ byType

+ constructor

### 注解

首先开启注解支持，在xml文件中添加下面的语句:
```
xmlns:context="http://www.springframework.org/schema/context"
xsi:schemaLocation="http://www.springframework.org/schema/context
                    http://www.springframework.org/schema/context/spring-context-4.0.xsd"
```
上面语句添加在xml文件开头的beans属性中，下面这句添加在xml内容中的开头
```
<context:annotation-config/>
```
+ @Required
  
  用于bean属性的setter方法，它表明注解的bean属性必须在xml配置文件中配置。否则会抛出一个BeanInitializationException的异常。

+ @Autowired
  + setter方法中的@Autowired
  
     Spring会在方法中尝试***byType***自动连接，之前没有用注解，那时候是要注入的bean中设置`autowire="byType"`

  + 属性中的@Autowired
     当在一个类中，使用@Autowired注解了它的属性，就可以省略掉属性的setter方法，Spring会在xml文件中寻找类型匹配的Bean注入。也是***byType***

  + 构造函数中的@Autowired
     当@Autowired注解这个构造方法时，Spring也是根据类型匹配找到相应的Bean来注入
 
+ @Qualifier

  当使用@Autowired注解时候，可能在Beans.xml中有很多个类型相同的Bean，这时候可以通过使用@Qualifier注解来指定特定的Bean。比如`@Qualifier("student1")`

+ @Resource
  
  注解字段或者setter方法。按照***byName***形式在配置文件中寻找Bean。如果按照名称找不到Bean的话，那么会按照***byType***的形式来找

+ @Configuration

  注解的类表示可以作为Sping Ioc容器的bean的定义的来源。使用这个注解了类以后比如`HelloConfig`之后，在HelloConfig使用@Bean注解:
  ```java
   @Bean 
   public HelloWorld helloWorld(){
      return new HelloWorld();
   } 
  ```
  然后通过下面代码拿到Bean:
  ```java
  ApplicationContext context = new AnnotationConfigApplicationContext(HelloConfig.class);
  ```
  就可以通过`context.getBean("HelloWorld.class")`拿到Bean HelloWorld。

+ @Bean

  带有@Bean注解的方法返回一个对象，这个对象被注册为在Sping应用程序上下文中的bean

---------------------------------------------------

### Spring AOP

Spring框架一个关键的组件是***面向方面编程***(AOP)框架。面向方面编程把程序的逻辑分成了不同的部分，称之为关注点，跨一个应用程序的多个点的功能被称为横切关注点。这些横切关注点在概念上独立于应用程序的业务逻辑。比如日志记录、审计、声明式事务、安全性和性能测试(我自己加的)。

+ 基于XML架构
  
  在xml中要使用AOP的话，下载两个包:aspectjrt和aspectjweaver，maven配置在这里:
  ```
    <!-- https://mvnrepository.com/artifact/org.aspectj/aspectjrt -->
        <dependency>
	    <groupId>org.aspectj</groupId>
	    <artifactId>aspectjrt</artifactId>
	    <version>1.9.1</version>
	</dependency>

    <!-- https://mvnrepository.com/artifact/org.aspectj/aspectjweaver -->
	<dependency>
	    <groupId>org.aspectj</groupId>
	    <artifactId>aspectjweaver</artifactId>
	    <version>1.9.1</version>
	</dependency>
  ```

  然后在xml文件头部添加:
  ```
  xmlns:aop="http://www.springframework.org/schema/aop"
  xsi:schemaLocation="http://www.springframework.org/schema/aop
                      http://www.springframework.org/schema/aop/spring-aop-4.0.xsd"
  ```

  然后就可以使用了

+ 使用@AspectJ

  先在xml文件中添加`<aop:aspectj-autoproxy/>`，使用注解
