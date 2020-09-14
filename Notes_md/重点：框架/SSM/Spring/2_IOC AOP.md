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



### [1.4  IOC的基本使用](IOC的基本使用.md)



# 2、AOP

