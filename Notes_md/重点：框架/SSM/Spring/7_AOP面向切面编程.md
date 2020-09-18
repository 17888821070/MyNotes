[目录](目录.md)  [返回](IOC AOP.md)

# AOP面向切面编程

## 1、基本配置

* maven依赖

  ><dependency>
  >  <groupId>org.aspectj</groupId>
  >  <artifactId>aspectjweaver</artifactId>
  >  <version>1.9.6</version>
  ></dependency>

### 1.1 写正常的代码逻辑

```java
//用于保存和修改Account的业务类
public class AcountService{
    public int saveAccount(Account account){
        System.out.println("saveAccount");
        return 1;
    }
    public int updateAccount(Account account){
        System.out.println("saveAccount");
        return 2;
    }
}
```

### 1.2 写具有公共代码的类

```java
//用于保存日志的类
public class Logger{
    //前置通知方法
    public void beforeLogger(){
        System.out.println("即将进行XXX操作");
    }
    //后置通用方法
    public void afterLogger(int value){
        System.out.println("执行XXX操作成功");
        System.out.println(value);
    }
    //异常通知方法
    public void catchLogger(Exception ex){
        System.out.println("执行XXX操作失败");
        System.out.println("李籽兴主动打印"+ex);
    }
    //最终通知方法
    public void finallyLogger(){
        System.out.println("执行XXX操作失败");
    }
}
```

### 1.3 配置切面

```properties
aopxml约束 = xmlns:aop="http://www.springframework.org/schema/aop"
aop通知Bean = <bean id="logger" class="Logger"></bean>
aop配置 = <aop:config>
aop切面 = <aop:aspect id="切面名称" ref="通知类">
aop前置通知 = <aop:before method="通知类的方法" pointcut="execution表达式">
	-- 在执行切入点方法前执行
aop后置通知 = <aop:after method="通知类的方法" pointcut="execution表达式">
	-- 在执行切入点方法后执行（未发生异常的情况下）
aop异常通知 = <aop:after-throwing method="通知类的方法" pointcut="execution表达式">
	-- 在执行切入点方法后执行（发生异常的情况下）
aop最终通知 = <aop:after-returning method="通知类的方法" pointcut="execution表达式">
	-- 在最后执行，无论异常是否发生
aop环绕通知 = <aop:around method="通知类的方法" pointcut="execution表达式">
	-- 需要自己明确调用切入点方法，其他切入点通知配置都失效
切入点表达式 = <aop:pointcut 
execution表达式  = execution([访问修饰符] 返回值类型 全限定类名.方法名(参数类型))
		   		= execution(* *..*.*(..)) 所有方法都拦截
		   		= execution(* 全限定类名.方法名(参数类型)) 不限定返回值和访问修饰符
		   		= execution(* *.*.*.方法(参数类型)) 指定层级包下的固定方法的固定参数类型
				= execution(* com.lzx.service.*.*.*(..)) 常用，只针对业务层的方法
		   ...
```

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/aop
                           http://www.springframework.org/schema/aop/spring-aop.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">

    <bean id="accountService" class="AccountService"></bean>
    
    <!--配置AOP-->
    <bean id="logger" class="Logger"></bean>
    <aop:config>
    	<aop:aspect id="loggerAspect" ref="logger">
            <aop:pointcut id="pt1" 
              expression="execution(public void com.lzx.service.AccountService.delete())"
                          
            <aop:before method="beforeLogger" pointcut-ref="pt1"/>
            <aop:after-returning method="afterLogger" pointcut="" returning="value" />
            <aop:after-throwing method="catchLogger" pointcut-ref="pt1" throwing="ex"/>
            <aop:after method="finallyLogger" pointcut-ref="pt1" />
        </aop:aspect>
    </aop:config>
</beans>
```

## 2、 注解方式配置切面

```xml
<!--开启AOP注解配置-->
<aop:aspectj-autoproxy></aop:aspectj-autoproxy>
```

```java
@Aspect
public class LoggerAspect{
    @Pointcut("execution("* com.lzx.service.impl.*.*(..)")")
    public void pt1(){}
    
    @Before("pt1()")
    public void beforeLogger(){
        System.out.println("即将进行XXX操作");
    }
    
    @AfterReturning("pt1()")
    public void afterLogger(int value){
        System.out.println("执行XXX操作成功");
        System.out.println(value);
    }
    
    @AfterThrowing("pt1()")
    public void catchLogger(Exception ex){
        System.out.println("执行XXX操作失败");
        System.out.println("李籽兴主动打印"+ex);
    }
    
    @After("pt1")
    public void finallyLogger(){
        System.out.println("执行XXX操作失败");
    }
}
```



* @Service 注解在Service类上
* @Aspect 注解在通知类上
* @Component 注解在通知类上
* @Before("pt1()") 注解在前置通知方法上

* @After("pt1()") 注解在后置通知方法上
* @AfterThrowing("pt1()") 注解在异常通知方法上
* @AfterReturning("pt1()") 注解在最终通知方法上

* @Around("pt1()") 注解在环绕通知方法上

* @PointCut("execution(XXXXXXXXXXXXXXXXXXXXXXXXXXXXX)")

  public pt1(){}