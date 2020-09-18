# 请求方法编写

## 1、 Controller

> 配置类为controller类

###  1.1 配置扫描注解

```xml
<contex:component-scan base-package="controller"/>
```

### 1.2 创建Controller类

```java
@Controller
	public class TestController{
}
```

## 2、 RequestMapping

### 1.1 为方法配置请求连接

```java
//请求login连接 indedx/login
@Controller
public class TestController{
    @RequestMapping("/index/login")
        public String login(){
        return "login";
    }
}
```

### 1.2 为类配置请求连接

```java
@RequestMapping("/index")
public class TestController{
    @RequestMapping("/login")
        public String login(){
        return "login";
    }
}
```

## 3、编写请求方法

### 3.1 controller请求方法中获取request和resoponse和session

```java
@RequestMapping("/index")
public class TestController{
    @RequestMapping("/login")
    public String login(HttpServletRequest request,
                        HttpServletResponse response,
                        HttpSession session
                           ){
        session.addAttribute("user",user);
        return "login";
    }
}
```

### 3.2 Model对象

> controller方法可以接收

```java
@RequestMapping("/index")
public class TestController{
    @RequestMapping("/login")
    public String login(Model model){
        model.addAttribute("username",username)
        return "login";
    }
}
```

## 4、获取参数

### 4.1 通过Bean获取参数

> 可以获取Get和Set方法的请求参数
>
> 要求：Bean对象有参数名的对应属性

```jsp
<form method="post" action="<%=request.getContextPath()%>/index/login">
	<input type="text" name="userName"/>
    <input type="password" name="password"/>
    <input type="submit"/>
</form>
```

```java
@RequestMapping("/index")
public class TestController{
    @RequestMapping("/login")
    public String login(User user){
		String username = user.getUseName();
        String password = user.getPassword();
        return "login";
    }
}
```

### 4.2 通过方法形参获取参数

> 请求参数名跟形参一一对应
>
> 或者给形参加上@RequestParam注解，使形参与参数一一对应

```java
@RequestMapping("/index")
public class TestController{
    @RequestMapping("/login")
    public String login(String username,String password){
        return "login";
    }
}
```

```java
@RequestMapping("/index")
public class TestController{
    @RequestMapping("/login")
    public String login(@RequestParam("userName")String accountName,,String password){
        return "login";
    }
}
```

### 4.3 使用request获取参数

```java
@RequestMapping("/index")
public class TestController{
    @RequestMapping("/login")
    public String login(HttpServletRequest request){
        String userName = request.getAttribute("userName");
        String password = request.getAttribute("password");
        return "login";
    }
}
```

### 4.4 通过 @PathVariable 接收 URL 中的请求参数

```java
@RequestMapping("/index")
public class TestController{
    @RequestMapping("/login")
    public String login(@PathVariable String userName, @PathVariable String password){
        return "login";
    }
}
```