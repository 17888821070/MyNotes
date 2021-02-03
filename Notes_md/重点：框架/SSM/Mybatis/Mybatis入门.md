[目录](目录.md)

# Mybatis入门

## 1、基本环境搭建

### 1.1 创建实体类

* cpm.lzx.domain

```java
public class User implements Serializable{
    int id;
    String name;
    get();set();toString()...
}
```

### 1.2 创建Mapper接口

*  com.lzx.mapper

```java
public interface UserMapper{
    List<User> findAll();
    User findById(String id);
}
```

### 1.3 配置总配置文件

* resources/sqlMapConfig.xml

* 导入约束
* 配置数据库连接信息
* 配置mapper映射文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
  PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <!--配置环境-->
    <environments default="MyDataSource">
        <!--配置MyDataSource环境-->
        <environment id="MyDataSource">
            <!--配置事务类型-->
        	<transactionManager type="JDBC"></transactionManager>
            <!--配置数据源（连接池）-->
            <dataSource type="POOLED">
            	<property neme="driver" value=""></property>
                <property name="url" value = ""></property>
                <property name="username" value></property>
                <property name="password" value></property>
            </dataSource>
        </environment>
         <!--配置SchoolDataSource环境-->
        <environment id="SchoolDataSource">
            <!--配置事务类型-->
        	<transactionManager type="JDBC"></transactionManager>
            <!--配置数据源（连接池）-->
            <dataSource type="POOLED">
            	<property neme="driver" value=""></property>
                <property name="url" value = ""></property>
                <property name="username" value></property>
                <property name="password" value></property>
            </dataSource>
        </environment>
    </environments>
    
    <!--指定映射的配置文件Mapper的位置-->
    <mappers>
    	<mapper resourse="com/lzx/mapper/UserMapper.xml"/>
    </mappers>
</configuration>
```

### 1.4 配置Mapper配置文件

* 导入约束
* 映射到具体的mapper类
* 将方法与sql语句对应

```xml
<?xml version="1.0" encoding="UTF-8" ?>
        <!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.lzx.mapper.UserMapper">
	<select id="findAll" resultType="com.lzx.domain.user">
        select * from User;
    </select>
</mapper>
```

### 1.5 执行mapper方法

* 读取配置文件
* 创建SqlSessionFactory工厂
* 创建SqlSession对象
* 创建Dao接口的代理对象
* 使用代理对象执行方法
* 释放资源

```java
public class Main {

    public static void main(String[] args) throws Exception {
        InputStream is = Resources.getResourceAsStream("mybatis.xml");
		//创建工厂
        SqlSessionFactoryBuilder builder = new SqlSessionFactoryBuilder();
        SqlSessionFactory factory = builder.build(is);
		
        //创建Session
        SqlSession sqlSession = factory.openSession();
		
        //创建代理mapper对象
        UserMapper userMapper = sqlSession.getMapper(UserMapper.class);

        List<User> all = userMapper.findAll();

        System.out.println(all);

    }
}
```

## 2、环境改进

### 2.1 连接信息放到properties

* jdbc.properties文件

```properties
driver=com.mysql.cj.jdbc.Driver
url=jdbc:mysql://localhost:3306/mylibrary_ssm?serverTimezone=UTC
username=root
password=123456
```

* 将文件引入到主配置文件

```xml
<properties sources="jdbc.properties"></properties>
...
	<property name="driver" value={$driver}/>
    <property name="url" value={$url}/>
	<property name="username" value={$username}/>	
	<property name="password" value={$password}/>
...
```

### 2.2 给domain中类配置别名

```xml
<typeAliases>
    <!--单个类配置别名-->
	<!--<typeAliase type="com.lzx.domain.User" alias="user"/>-->
    
    <!--整个domain中的类都配置别名为类名-->
    <package name="com.lzx.domain"/>
</typeAliases>
```

### 2.3 给Mapper映射全部一次映射

```xml
<mappers>
    <!--<mapper resourse="com/lzx/mapper/UserMapper.xml"/>-->
    <package name="com.lzx.mapper"></package>
</mappers>
```



## 3、注解方式配置mapper

### 3.1 去掉mapper.xml

### 3.2 修改主xml的mapper映射

```xml
<mappers>
	<!--<mapper resources="com/lzx/mapper/*.xml"></mapper>-->
    <mapper class="com.lzx.mapper.*"></mapper>
</mappers>
```

### 3.3 mapper类方法加注解

```java
@Select("select * from User")
List<User> findAll();
```

## 4、mybatis的设计模式

* 构建者模式

  SqlSessionFactory factory = new SqlSessionFactoryBuilder().build();

* 工厂模式

  SqlSession sqlSession = factory.openSession();

* 代理模式

  UserMapper userMapper = sqlSession.getMapper(UserMapper.class);









