[目录](目录.md)  [返回](IOC AOP.md)

# IOC容器

[BeanFactory容器](# 1、Spring BeanFactory容器)

[ApplicationContext容器](# 2、Spring ApplicationContext容器)

[SpringBean](# 3、SpringBean)

​	[属性](# 3.1 属性)

​	[创建及使用流程](# 3.2 创建及使用流程)

​	[作用域](# 3.3 作用域)

​	[生命周期](# 3.4 生命周期)

​	[后置处理器](# 3.5 后置处理器)

## 1、Spring BeanFactory容器

* 常用实现
  * XmlBeanFactory

```java
XmlBeanFactory factory = new XmlBeanFactory(new ClassPathResource("Beans.xml"));
HelloWorld obj = (HelloWorld) factory.getBean("helloWorld");
obj.getMessage();
```

## 2、Spring ApplicationContext容器

* 常用实现

  * FileSystemXmlApplicationContext

    该容器从 XML 文件中加载已被定义的 bean。在这里，你需要提供给构造器 XML 文件的完整路径。

  * ClassPathXmlApplicationContext

    该容器从 XML 文件中加载已被定义的 bean。在这里，你不需要提供 XML 文件的完整路径，只需正确配置 CLASSPATH 环境变量即可，因为，容器会从 CLASSPATH 中搜索 bean 配置文件。

  * WebXmlApplicationContext

    该容器会在一个 web 应用程序的范围内加载在 XML 文件中已被定义的 bean。

```java
ApplicationContext context = new FileSystemXmlApplicationContext
            ("C:/Users/ZARA/workspace/HelloSpring/src/Beans.xml");
HelloWorld obj = (HelloWorld) context.getBean("helloWorld");
obj.getMessage();
```

```java
ApplicationContext context = new ClassPathXmlApplicationContext("Beans.xml");
HelloWorld obj = (HelloWorld) context.getBean("helloWorld");
obj.getMessage();
```

## 3、SpringBean

* SpringBean是由IOC管理的对象

### 3.1 属性

| 属性                     | 描述                                                         |
| ------------------------ | ------------------------------------------------------------ |
| class                    | 这个属性是强制性的，并且指定用来创建 bean 的 bean 类。       |
| id/name                  | 这个属性指定唯一的 bean 标识符。在基于 XML 的配置元数据中，你可以使用 ID 和/或 name 属性来指定 bean 标识符。 |
| scope                    | 这个属性指定由特定的 bean 定义创建的对象的作用域，它将会在 bean 作用域的章节中进行讨论。 |
| constructor-arg          | 它是用来注入依赖关系的，并会在接下来的章节中进行讨论。       |
| properties               | 它是用来注入依赖关系的，并会在接下来的章节中进行讨论。       |
| autowiring mode          | 它是用来注入依赖关系的，并会在接下来的章节中进行讨论。       |
| lazy-initialization mode | 延迟初始化的 bean 告诉 IoC 容器在它第一次被请求时，而不是在启动时去创建一个 bean 实例。 |
| init-method              | 在 bean 的所有必需的属性被容器设置之后，调用回调方法。它将会在 bean 的生命周期章节中进行讨论。 |
| destroy-method           | 当包含该 bean 的容器被销毁时，使用回调方法。它将会在 bean 的生命周期章节中进行讨论。 |
| parent                   | 指定继承的Bean                                               |
| abstract                 | 指定此Bean是不能实例化的，需要被其他Bean继承                 |

### 3.2 创建及使用流程

1、IOC读取“xml<bean>、java@Configuration、注解@Autowrite”的配置信息

2、配置信息形成Spring容器的Bean 定义注册表

3、根据Bean定义注册表，结合Bean实体类，创建对象，放到Bean缓冲池

4、用户使用Bean对象，直接从SpringIOC容器中获取对象

### 3.3 作用域

> <bean class="" id="" scope="作用域类型"/>

* singleton：每次都返回同一个Bean对象，单例模式，默认的作用域
* prototype：每次需要都创建一个新的Bean对象
* request：每次请求需要时创建一个新的Bean对象，只应用于WebApplicationContext环境

* session：同一个Session共享一个Bean对象，不同Sessio使用不同Bean对象，只应用于WebApplicationContext环境
* global-session：一般应用于portlet应用环境，仅适用于WebApplicationContext环境

### 3.4 生命周期

>  Bean对象在IOC容器中从创建到销毁的过程
>
>  生命周期：Bean的定义——Bean的初始化——Bean的使用——Bean的销毁


* 初始化回调

  > <bean class="" id="" init-method="回调函数"/>

* 销毁回调

  > <bean class="" id="" destroy-method="回调函数"/>

* 示例：

```java
class User{
    String userName;
    get();set();
    public void init(){ sout("初始化Bean了")}
    public void destory(){ sout("销毁Bean")}
}
```
  ```xml
<bean class="" id="" init-method="init" dertory-method="destory"/>
  ```

### 3.5 后置处理器

> 用于Bean初始化回调函数前后对Bean对象进行增强

```java
//后置处理器类InitHelloWorld
public class InitHelloWorld implements BeanPostProcessor {
    public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
        System.out.println("InitHelloWorld,init前被调用了一下");
        return bean;
    }

    public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
        System.out.println("InitHelloWorld,init后被调用了一下");
        return bean;
    }
}
//后置处理器类DestroyHelloWorld
public class DestroyHelloWorld implements BeanPostProcessor {
    public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
        System.out.println(beanName+"DestroyHelloWorld,Init前被调用了一下");
        return bean;
    }

    public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
        System.out.println(beanName+"DestroyHelloWorld,init后被调用了一下");
        return bean;
    }
}
```

```xml
<bean id="userB" class="com.lzx.domain.User" init-method="init" destroy-method="destroy">
    <property name="userName" value="userB!"/>
</bean>
<bean class="com.lzx.processor.DestroyHelloWorld"></bean>
<bean class="com.lzx.processor.InitHelloWorld"></bean>
```

```java
public static void main(String[] args) {
    AbstractApplicationContext context = new 
        						ClassPathXmlApplicationContext("spring.xml");
    User userA = (User)context.getBean("userA");
    User userB = (User)context.getBean("userB");
    context.registerShutdownHook();
}
```

>输出：
>
>userADestroyHelloWorld,Init前被调用了一下
>userAInitHelloWorld,init前被调用了一下
>初始化：userA!
>userADestroyHelloWorld,init后被调用了一下
>userAInitHelloWorld,init后被调用了一下
>userBDestroyHelloWorld,Init前被调用了一下
>userBInitHelloWorld,init前被调用了一下
>初始化：userB!
>userBDestroyHelloWorld,init后被调用了一下
>userBInitHelloWorld,init后被调用了一下
>销毁：userB!
>销毁：userA!

### 3.6 定义继承

> 用于Bean属性等配置信息的传递

* 直接继承普通类

```xml
<bean id="helloWorld" class="com.tutorialspoint.HelloWorld">
    <property name="message1" value="Hello World!"/>
    <property name="message2" value="Hello Second World!"/>
</bean>

<bean id="helloIndia" class="com.tutorialspoint.HelloIndia" parent="helloWorld">
    <property name="message1" value="Hello India!"/> <!--会覆盖父类的属性的值-->
    <!--message2会继承父类的属性值-->
    <property name="message3" value="Namaste India!"/> 
</bean>
```

* 定义不可继承的类

```xml
<bean id="beanTeamplate" abstract="true">
    <property name="message1" value="Hello World!"/>
    <property name="message2" value="Hello Second World!"/>
    <property name="message3" value="Namaste India!"/>
</bean>

<bean id="helloIndia" class="com.tutorialspoint.HelloIndia" parent="beanTeamplate">
    <property name="message1" value="Hello India!"/>
    <property name="message3" value="Namaste India!"/>
</bean>
```

