# Spring入门

## 1、 入门程序

* maven依赖

> <!--Spring 依赖-->
> <dependency>
>   <groupId>org.springframework</groupId>
>   <artifactId>spring-context</artifactId>
>   <version>5.0.8.RELEASE</version>
> </dependency>
> <dependency>
>   <groupId>org.springframework</groupId>
>   <artifactId>spring-core</artifactId>
>   <version>5.0.8.RELEASE</version>
> </dependency>
> <dependency>
>   <groupId>org.springframework</groupId>
>   <artifactId>spring-beans</artifactId>
>   <version>5.0.8.RELEASE</version>
> </dependency>

## 1.1 主配置文件

* resources/spring.xml

``` xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans-3.0.xsd">

   <bean id="helloWorld" class="com.tutorialspoint.HelloWorld">
       <property name="message" value="Hello World!"/>
   </bean>
</beans>
```

### 1.2 使用

* 使用ApplicationContext获取IOC容器

```java
public static void main(String[] args) {
    ApplicationContext context = 
        new ClassPathXmlApplicationContext("Beans.xml");
    HelloWorld obj = (HelloWorld) context.getBean("helloWorld");
    obj.getMessage();
}
```

