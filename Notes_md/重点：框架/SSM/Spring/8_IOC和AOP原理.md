# IOC原理

##  SpringBean的生命周期

> singleton作用域的Bean对象会被Spring 跟踪整个生命周期
>
> prototype作用域的Bean对象被创建后，Spring就不再对其进行跟踪

① 根据配置情况调用Bean类的构造方法或Factory的实例化方法创建Bean对象

② 利用依赖注入，注入配置的需要注入的属性Bean

③ 调用Bean继承的接口的方法或者绑定的BeanPostProcesser的方法（有则调用）

​	Ⅰ Bean实现BeanNameAware接口的setBeanName方法，传入Bean的id值

​	Ⅱ Bean实现BeanFactoryAware接口的setBeanFactory方法，传入当前工厂实例的引用

​	Ⅲ Bean实现ApplicationContextAware接口的setApplicationContext方法，传入Spring容器实例的引用

​	Ⅳ 与Bean绑定的BeanPostProcessor接口的postProcessBeforeInitialization方法，对Bean进行加工

``` java
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
```

​	Ⅴ Bean实现InitializingBean接口的afterPropertiesSet方法

​	Ⅵ Bean配置中init-method配置的初始化方法

```java
class User{
    String userName;
    get();set();
    public void init(){ sout("初始化Bean了")}
    public void destory(){ sout("销毁Bean")}
}
<bean class="" id="" init-method="init" dertory-method="destory"/>
```

​	Ⅶ BeanPostProcesser接口的postProcesserAfterInitialization方法，就Bean进行加工，类似Ⅳ

⑤ Bean进入缓存池或交给调用者

​	Ⅰ 如果Bean的作用域是singleton，则Bean对象进入IOC缓存池，并出发对Bean的生命周期的管理

​	Ⅱ 如果Bean的作用域是prototype，则直接将Bean对象交给调用者，放弃管理

⑥  Singleton作用域的Bean的销毁

​	Ⅰ Bean实现DisposableBean接口的destory方法

​	Ⅱ Bean配置中dertory-method配置的初始化方法

#  AOP的原理——动态代理

## jdk动态代理

> jdk动态代理就是通过调用java.lang.reflect.Proxy的newProxyInstance方法创建代理对象。
>
> 其实就是基本的基本的动态代理
>
> ​	分别将前置，后置，异常，最终切面方法放到InvocationHandler接口实现的对应方法的前、后、catch、finally里

缺点：需要①接口②接口实现类③代理创建类

​			需要接口的一个或多个实现类（例子中的MyTestDaoImpl），对实现类进行动态代理增强

```java
public interface MyTestDao {
    public int myMethod(int x);
}
public class MyTestDaoImpl implements MyTestDao {
    @Override
    public int myMethod(int x) {
        System.out.println(x);
        return x+1;
    }
}
public class MyAspect {
    public void beforeAspect(){
        System.out.println("前置切口");
    }
}
public class Main {

    public static void main(String[] args) {
        MyTestDao user = new MyTestDaoImpl();
        MyTestDao userDao = (MyTestDao) Proxy.newProxyInstance(MyTestDao.class.getClassLoader(), MyTestDaoImpl.class.getInterfaces(), new InvocationHandler() {
            @Override
            public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                MyAspect myAspect = new MyAspect();
                myAspect.beforeAspect();
                Object obj = method.invoke(user,args);
                return obj;
            }
        });
        int i = userDao.myMethod(100);
        System.out.println(i);
    }
}
```

## CGLIB动态代理

> 是一个高性能开源的代码生成包，已集成到Spring的核心包中
>
> 采用底层的字节码技术
>
> 对指定的目标类生成一个子类，对其进行增强

```java
public class MyTestDaoImpl implements MyTestDao {
    @Override
    public int myMethod(int x) {
        System.out.println(x);
        return x+1;
    }
}
public class MyAspect {
    public void beforeAspect(){
        System.out.println("前置切口");
    }
}
public class Main {

    public static void main(String[] args) {
        MyTestDaoImpl myTestDao = new MyTestDaoImpl();
        MyTestDaoImpl myTestDao1 = (MyTestDaoImpl) Enhancer.create(myTestDao.getClass(), new MethodInterceptor() {
            @Override
            public Object intercept(Object o, Method method, Object[] objects, MethodProxy methodProxy) throws Throwable {
                MyAspect myAspect = new MyAspect();
                myAspect.beforeAspect();
                //Object invoke = method.invoke(o, objects);
                Object invoke = methodProxy.invokeSuper(o, objects);
                return invoke;
            }
        });

        int i = myTestDao1.myMethod(100);
        System.out.println(i);
    }
}
```

