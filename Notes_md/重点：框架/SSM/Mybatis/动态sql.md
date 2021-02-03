[目录](目录.md)

# 动态sql

* 根据参数动态判断组成sql

## 标签

### 1. <if>

* 注意加where1=1和空格和and

```xml
<select id="findAll" resultType="User" parameterType="User">
    select * from user where 1=1 
    <if test="name!=null">
    	and name=#{name} 
    </if>
</select>
```

### 2. <where>

* 此时不用加where 1=1了

```xml
<select id="findAll" resultType="User" parameterType="User">
    select * from user 
    <where>
        <if test="name!=null">
            and name=#{name} 
        </if>
    </where>
</select>
```

### 3. <foreach>

```xml
<!--QueryVo为自定义类，有一个叫ids的List<Integer>集合-->
<select id="findAll" resultType="User" parameterType="QueryVo"> 
    select * from user 
    <where>
        <if test="ids!=null">
        	<foreach collection="ids" open="and id in(" close=")" item="id" separator=",">
            	#{id}
            </foreach>
        </if>
    </where>
</select>
```

