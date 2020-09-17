[目录](目录.md)

# IOC AOP

[IOC](# 1、IOC)

[AOP](# 2、AOP)

* IOC和AOP是Spring的内核
* IOC解决的是对象管理和对象依赖的问题
* AOP解决的是非业务代码抽取的问题

# 1、IOC

> 控制反转：由Spring的IOC容器负责对象的创建和依赖
>
> 依赖注入：依赖的对象由IOC容器创建好，进行注入赋值（原操作为主动new创建）
>
> IOC就是由Spring IOC容器来负责对象的生命周期和对象之间的关系

### 1.1 本质问题

* 谁控制谁？
  * 原来：程序员new对象，程序员控制对象
  * Spring：IOC容器控制对象
* 控制什么？
  * 对象
* 为何是反转?
  * 原来：程序员主动创建类A中依赖的其他类B对象
  * Spring：IOC容器创建了被依赖的B对象后，将对象注入到A的对象中， 依赖由主动创建变成了被动接受，所以是反转
* 什么反转了？
  * 依赖的对象的获取方式，由主动new变成了被动接收

### 1.2 程序员写控制的三种注入方式

* 构造器注入

  ```java
  class YounfMan{
      BeautifulGirl beautifulGirl;
      public YoungMan(BeautifulGirl beautifulGirl){
          this.beautifulGirl = beautifulGirl;
  	}
  }
  ```

* setter注入

  ```java
  class YounfMan{
      private BeautifulGirl beautifulGirl;
      public void setBeautifulGirl(BeautifulGirl beautifulGirl) {
          this.beautifulGirl = beautifulGirl;
      }
  }
  ```

* 接口注入

  需要被依赖的对象实现不必要的接口，带有侵入性。一般都不推荐这种方式。

### 1.3 猿主动创建和Spring IOC创建对象对比

#### 猿注入

![img](imgs\261421378318292.jpg)

#### Spring IOC注入

![img](imgs\img.png)



### [1.4  IOC的基本使用](3_IOC容器.md)



# 2、AOP

> **面向切面编程**：将分散到各个方法中的重复代码进行抽取，例如日志记录、事务提交回滚等，使用动态代理技术将aop抽取的代码再加入到程序过程中，在编译或运行阶段将抽取出的代码应用到需要执行的地方
>
> 白话：service层当前处理时都要在开始和结束后对事务进行开启和关闭，冗余很多， AOP就是将事务的开启关闭等操作分离出来，在编译或运行时，在service方法运行前后先调用aop接口方法

### 2.1 应用场景：

* 日志记录
* 事务控制
* 权限判断
* 异常处理

### 2.2 基本术语

* 切面Aspect：将切入点、通知配置出来后就是切面

<img src="imgs\1600238343273.png" style="zoom: 33%;" />

* 连接点JoinPoint：指业务中所有可以被指定用切面增强的方法

* 切入点cutPoint：指配置中定义的需要进行拦截的连接点

* 通知Advice：拦截到连接点的调用后，要做的增强的内容，Invoke内部的增强

  * 前置通知：在拦截到的方法调用前，先调用的方法

  * 后置通知：在拦截到的方法调用后，后调用的方法

  * 异常通知：在拦截到方法发生异常时，调用的方法

  * 最终通知：在拦截到的方法的finally内部调用的方法

  * 环绕通知：整个通知就是环绕通知

    <img src="imgs\1600240066448.png" alt="1600240066448" style="zoom:67%;" />

* 代理对象TargetObject：被代理的对象

* 代理Proxy：将通知加入到目标对象，生成代理对象的方法

* 织入Weaving：将切片插入到目标对象上，从而生成代理对象的过程

### 2.3 [AOP的基本使用]()

