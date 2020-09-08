# JDBC数据库连接池JDBC工具

[1、三层架构](# 1、三层架构)

[2、JDBC](# 2、JDBC及数据库连接工具)

​	[2.1 JDBC基础](# 2.1 jdbc基本使用及事务管理)

​	[2.2 JDBC连接池](# 2.2 数据库连接池)

​		[2.2.1 C3P0](# 2.2.1 C3P0)

​		[2.2.2 DBCP](# 2.2.2 DBCP)

​		[2.2.3Druid](# 2.2.3 Druid)

​	[2.3 jdbc的封装工具](# 2.3 jdbc的封装工具)

​		[2.3.1 DBUtils](# 2.3.1 DBUtils [Apache])

​		[2.3.2 JdbcTemplate](2.3.2 JdbcTemplate)



## 1、三层架构

* 表现层
  * 用于数据展示
* 业务层
  * 用于处理业务逻辑
* 持久层
  * 用于与数据库交互

## 2、 JDBC及数据库连接工具

### 2.1 jdbc基本使用及事务管理

* 基本使用

  ```java
  public static void main(String[] args){
      //加载驱动
      Class.forName("com.mysql.cj.jdbc.Driver"); //低版本用com.mysql.jdbc.Driver
  
      //获取连接
      Connection conn = DriverManager.getConnection(
          "jdbc:mysql://localhost:3306/lzx?serverTimezone=UTC");
      
      /*
      * 方法1：使用普通Statement处理结果
      */
      //创建Statement对象
      Statement statement= conn.createStatement();
      
      //创建sql 
      String sql = "select * from user where id = 1";
      
      //执行sql
      ResultSet result = statement.executeQuery(sql);
      
      //解析结果
      while(result.next()){
          String username = result.getString("username");
          int age = result.getInt("age");
      }
      
      result.close();
      statement.close();
      
      /*
      * 方法2：使用PreparedStatement处理结果
      */
      //创建带参数占位符的sql
      String preSql = "select * from user where id = ?";
      
      //创建PreparedStatement对象
      PreparedStatement preStatement = conn.preparedStatement(preSql);
      
      //传入参数
      preStatement.setInt(1,2);//将第一个参数的值设为2
      
      //执行sql
      ResultSet result = preStatement.executeQuery(sql);
      
      //解析结果
      while(result.next()){
          String username = result.getString("username");
          int age = result.getInt("age");
      }
      result.close();
      statement.close();
  }
  ```

* 事务管理

  ```java
  //获取链接
  Connection conn = DbUtil.getCOnnection();
  
  //开启事务
  conn.setAutoCommmit(false);
  //提交事务
  conn.commit();
  //回滚事务
  conn.rollback();
  //关闭事务
  conn.setAutoCommit(true);
  ```

### 2.2 数据库连接池

#### 2.2.1 C3P0

maven依赖

><groupId>com.mchange</groupId>
><artifactId>c3p0</artifactId>
><version>0.9.5.2</version>

* 方法1：set方法配置

  ```java
  //连接池对象
  private static ComboPooledDataSource datasource = new ComboPooledDataSource();
  
  //设置连接池连接参数
  try {
      datasource.setDriverClass(driver);
      datasource.setJdbcUrl(url);
      datasource.setUser(user);
      datasource.setPassword(password);
  } catch (Exception e) {
      e.printStackTrace();
  }
  
  //从连接池中获取连接
  Connection conn = datasource.getConnection();
  
  //将连接对象归还至连接池
  conn.close();
  ```

* 方法2：xml文件配置

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <c3p0-config>
      <!-- 默认配置，如果没有指定则使用这个配置 -->
      <default-config>
          <property name="user">zhanghanlun</property>
          <property name="password">123456</property>
          <property name="jdbcUrl">jdbc:mysql://localhost:3306/zhanghanlun</property>
          <property name="driverClass">com.mysql.jdbc.Driver</property>
          <property name="checkoutTimeout">30000</property>
          <property name="idleConnectionTestPeriod">30</property>
          <property name="initialPoolSize">3</property>
          <property name="maxIdleTime">30</property>
          <property name="maxPoolSize">100</property>
          <property name="minPoolSize">2</property>
          <property name="maxStatements">200</property>
      </default-config>
      <!-- 命名的配置,可以通过方法调用实现 -->
      <named-config name="test">
          <property name="user">zhanghanlun</property>
          <property name="password">123456</property>
          <property name="jdbcUrl">jdbc:mysql://localhost:3306/zhanghanlun</property>
          <property name="driverClass">com.mysql.jdbc.Driver</property>
          <!-- 如果池中数据连接不够时一次增长多少个 -->
          <property name="acquireIncrement">5</property>
          <!-- 初始化数据库连接池时连接的数量 -->
          <property name="initialPoolSize">20</property>
          <!-- 数据库连接池中的最大的数据库连接数 -->
          <property name="maxPoolSize">25</property>
          <!-- 数据库连接池中的最小的数据库连接数 -->
          <property name="minPoolSize">5</property>
      </named-config>
  </c3p0-config>
  ```

  ```java
  //连接池对象
  private static ComboPooledDataSource datasource = new ComboPooledDataSource("配置文件名");
  //从连接池中获取连接
  Connection conn = datasource.getConnection();
  //将连接对象归还至连接池
  conn.close();
  ```

* Spring连接池配置

  ```xml
  <!--配置数据库连接-->
  <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
      <property name="driverClass" value="${driver}"/>
      <property name="jdbcUrl" value="${url}"/>
      <property name="user" value="${username}"/>
      <property name="password" value="${password}"/>
  </bean>
  ```

#### 2.2.2 DBCP

maven依赖

><groupId>commons-dbcp</groupId>
><artifactId>commons-dbcp</artifactId>
><version>1.4</version>

* properties文件配置法

  ```properties
  ########DBCP配置文件##########
  #驱动名
  driverClassName=com.mysql.jdbc.Driver
  #url
  url=jdbc:mysql://127.0.0.1:3306/mydb
  #用户名
  username=sa
  #密码
  password=123456
  #初试连接数
  initialSize=30
  #最大活跃数
  maxTotal=30
  #最大idle数
  maxIdle=10
  #最小idle数
  minIdle=5
  #最长等待时间(毫秒)
  maxWaitMillis=1000
  #程序中的连接不使用后是否被连接池回收(该版本要使用removeAbandonedOnMaintenance和removeAbandonedOnBorrow)
  #removeAbandoned=true
  removeAbandonedOnMaintenance=true
  removeAbandonedOnBorrow=true
  #连接在所指定的秒数内未使用才会被删除(秒)(为配合测试程序才配置为1秒)
  removeAbandonedTimeout=1
  ```

  ```java
  public static void main(String[] args){
      Properties properties = new Propertise();
  	properties.load(new FileInputStream("配置文件.propertise"));
  	DataSource datasource = BasicDataSourceFactory.createDataSoure(propertise);
  	Connection conn = datasource.getConnection();
  	conn.close(0);
  }
  ```

* Spring连接池配置

  ```xml
  <!--配置数据库连接-->
  <bean id="dataSource" class="org.apache.commons.dbcp.BasicDataSource">
      <property name="driverClassName" value="${driver}"/>
      <property name="url" value="${url}"/>
      <property name="username" value="${username}"/>
      <property name="password" value="${password}"/>
      <!-- 初始化连接大小 -->
      <property name="initialSize" value="${initialSize}"></property>
      <!-- 连接池最大数量 -->
      <property name="maxActive" value="${maxActive}"></property>
      <!-- 连接池最大空闲 -->
      <property name="maxIdle" value="${maxIdle}"></property>
      <!-- 连接池最小空闲 -->
      <property name="minIdle" value="${minIdle}"></property>
      <!-- 获取连接最大等待时间 -->
      <property name="maxWait" value="${maxWait}"></property>
  </bean>
  ```

#### 2.2.3 Druid

maven依赖

> <groupId>com.alibaba</groupId>
> <artifactId>druid</artifactId>
> <version>1.1.23</version>

* Properties方式配置

  ```properties
  driverClassName=com.mysql.cj.jdbc.Driver
  url=jdbc:mysql://localhost:3306/studentmenegesystem?
  username=root 
  password=123456 
  initialSize=5 
  maxActive=10 
  maxWait=3000
  ```

  ```java
  public static void main(String[] args){
      Properties properties = new Propertise();
  	properties.load(new FileInputStream("配置文件.properties"));
  	DataSource datasource = DruidDataSourceFactory.createDataSoure(propertise);
  	Connection conn = datasource.getConnection();
  	conn.close(0);
  }
  ```

* Spring连接池配置

  ```xml
  <?xml version="1.0" encoding="UTF-8"?> 
  <beans xmlns=" http://www.springframework.org/schema/beans" 
   xmlns:xsi=" http://www.w3.org/2001/XMLSchema-instance" xmlns:batch=" http://www.springframework.org/schema/batch" 
   xsi:schemaLocation=" http://www.springframework.org/schema/beans 
             http://www.springframework.org/schema/beans/spring-beans-4.0.xsd">
  
      <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource"
            init-method="init" destroy-method="close">
          <property name="driverClassName" value="${jdbc.driverClassName}" /> 
    		<property name="url" value="${jdbc.url}" /> 
    		<property name="username" value="${jdbc.username}" /> 
    		<property name="password" value="${jdbc.password}" /> 
      </bean>
  
  </beans>
  ```

### 2.3 jdbc的封装工具

#### 2.3.1 DBUtils [Apache]

maven依赖

>  <groupId>commons-dbutils</groupId>
>  <artifactId>commons-dbutils</artifactId>
>  <version>1.7</version>

* QueryRunner

  * update(Connection conn, String sql, Object... params)

    ```java
    QueryRunner query = new QueryRunner();
    query.query(conn,"update user set name="lizixing" where id = ?, 1);
    ```

  * query(Connection conn, String sql, ResultSetHandler<T> rsh ,Object... params)

    * ArrayHandler，查询结果的第一行封成数组
    * ArrayListHandler ，查询结果每一行封成一个数组，数据存储到List集合
    * BeanHandler，结果集，数据表第一行，封装到JavaBean对象中
    * BeanListHanlder, 结果集，数据表所有符合条件行，封装到JavaBean对象中，再存于List
    * ColumnListHandler, 结果集指定列的数据，存储到List集合
    * ScalarHandler, 查询结果为一个结果，一行一列或者聚合函数的结果
    * MapHandler，查询结果的第一行封装为一个map
    * MapListHandler，查询的结果封装为map集合

    ```java
    QueryRunner qr = new QueryRunner();
    //ArrayHandler,查询结果的第一行封成数组
    String sql = "SELECT * FROM user where id = ?";
    Object[] result = qr.query(conn, sql, new ArrayHandler(),1);
    //ArrayListHandler 
    String sql1 = "SELECT * FROM user";
    List<Object[]> query = qr.query(conn, sql1, new ArrayListHandler());
    //BeanHandler
    Userquery = qr.query(conn, sql, new BeanHandler<User>(User.class),1);
    //BeanListHanlder
    List<User> query = qr.query(conn, sql1, new BeanListHandler<User>(User.class));
    //ColumnListHandler
    List<Object> list = qr.query(con, sql1, new ColumnListHandler<Object>("name"));
    //ScalarHandler
    String sql2 = "SELECT COUNT(*) FROM sort";
    long count = qr.query(con, sql1, new ScalarHandler<Long>());
    //MapHandler
    Map<String,Objert> map = qr.query(conn,sql,new MapHandler,1);
    //MapListHandler
    List<Map<String,Objert>> mapList = qr.query(conn,sql1,new MapHandler);
    ```

#### 2.3.2 JdbcTemplate [Spring]

maven依赖

>  <groupId>org.springframework</groupId>
>  <artifactId>spring-jdbc</artifactId>
>  <version>5.0.8.RELEASE</version>

* JdbcTemplate

  * execute，没有返回值

    ```java
    JdbcTemplate jdbcTemplate = new JdbcTemplate(DataSourceUtil.getDataSource());
    String sql = "create table user( id int primary key, name varchar(25))";
    jdbcTemplate.execute(sql);
    ```

  * update，增删改，返回影响的行数

    ```java
    JdbcTemplate jdbcTemplate = new JdbcTemplate(DataSourceUtil.getDataSource());
    String sql = "update user set name = ? where id = ?";
    int i = jdbcTemplate.update(sql,"lizixing",1);
    ```

  * queryXXX，查询

    * queryForObject(String sql, Class clazz, Object... params)，**查询一个值**，返回int，double等

      ```java
      //queryForObject，返回基本类型
      String sql = "select username from tbl_user where id=? and username = ?";
      String name = jdbcTemplate.queryForObject(sql,String.class,1, "李籽兴");
      ```

    * queryForObject(String sql,

      ​				new BeanPropertyRowMapper<T>(T.class), Object... params), **查询一行值**，domain等

      ```java
      //queryForObject，返回自定义domain类型
      String sql = "select username from tbl_user where id=? and username = ?";
      User name = jdbcTemplate.queryForObject(sql,
            		new BeanPropertyRowMapper<User>(User.class),1, "李籽兴");
      ```

    * queryForList(String sql, Class<T> elementType, Object... params), **查询一列值**，基本和String

      ```java
      //queryForList
      String sql = "select username from tbl_User where id>?";
      List<String> names = jdbcTemplate.queryForList(sql,String.class,3);
      ```

    * query(String sql, RowMapper<T> rowMapper, Object... params), **查询多行数据**

      ```java
      //query
      String sql="select * from tbl_user where id > ?";
      List<User> users = jdbcTemplate.query(sql,
                         new BeanPropertyRowMapper<User>(User.class),3);
      
      List<Map<Integer,String>> users = jdbcTemplate.query(sql,
          new RowMapper<Map<Integer,String>>() {
              @Override
              public Map<Integer,String> mapRow(ResultSet resultSet, int i) throws SQLException {
                  Map<Integer,String> map = new HashMap<>();
                  map.put(resultSet.getInt("id"),resultSet.getString("username"));
               }
           },3);
      ```

