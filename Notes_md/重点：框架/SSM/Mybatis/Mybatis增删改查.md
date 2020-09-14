[目录](目录.md)

# MyBatis增删改查

## 1、增

```xml
<mapper namespace="lzx.com.mapper.UserMapper">
    <insert id="saveUser" parameterType="com.lzx.domain.User">
        insert into user (id, name) values (#{id},#{name});
    </insert>
</mapper>
```

```java
User user = new User();
...
userMapper.saveUser(user);
sqlSession.commit();
```

### 1.2 获取自增的id

select  last_insert_id(); -- sql语句，在insert之后执行时能获取当刚insert的数据的id

```xml
<insert id="saveUser" parameterType="User">
	<selectKey keyProperty="id" keyColumn="id" resultType="int" order="AFTER">
    	select last_insert_id();
    </selectKey>
    insert into user (id, name) values (#{id},#{name});
</insert>
```

* 调用此方法前后，会将user的id属性更新为刚插入的id

```java
userMapper.saveUser(user);
```





## 2、删

```xml
<mapper namespace="lzx.com.mapper.UserMapper">
    <delete id="deleteUser" parameterType="int/java.lang.Integer">
        delete from user where id = #{id}
    </delete>
</mapper>
```

```java
User user = new User();
...
userMapper.deleteUser(1000);
sqlSession.commit();
```

## 3、改

```xml
<mapper namespace="lzx.com.mapper.UserMapper">
    <update id="updateUser" parameterType="com.lzx.domain.User">
        update user set name=#{name} where id=#{id}
    </update>
</mapper>
```

```java
User user = new User();
...
userMapper.updateUser(user);
sqlSession.commit();
```

## 4、查

### 4.1 查找一行

```xml
<select id="findById" resultType="lzx.com.domain.User" parameterType="int">
    select * from user where id=#{id}
</select>
```

```java
User user = userMapper.findById(100);
```

### 4.2 查找多行

```xml
<select id="findByName" resultType="....User" parameter="String">
	select * from user where name like #{name}
</select>
```

```java
List<User> users = userMapper.findByName("%李");
```

### 4.3 查找一行一列

```xml
<select id="findCountByName" resultType="int" paramter="String">
	select count(*) from user where name like #{name}
</select>
```

```java
int count = userMapper.findCountByName("%李%");
```



