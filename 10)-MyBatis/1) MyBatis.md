
# 一、简介

主流的持久层框架
官网：https://mybatis.org/mybatis-3/zh/index.html


# 二、快速入门

1. 添加坐标`pom.xml`

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>org.example</groupId>
    <artifactId>MyBatisDemoTest</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>jar</packaging>

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    </properties>

    <dependencies>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.32</version>
        </dependency>
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.5.6</version>
        </dependency>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.13.2</version>
            <scope>test</scope>
        </dependency>
    </dependencies>
</project>
```

2. 创建User类

```java
package com.example.domain;

public class User {
    private int id;
    private String name;
    private String password;
    private String status;

    public User() {
    }

    public User(String name, String password, String status) {
        this.name = name;
        this.password = password;
        this.status = status;
    }

    public User(int id, String name, String password, String status) {
        this.id = id;
        this.name = name;
        this.password = password;
        this.status = status;
    }

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getPassword() {
        return password;
    }

    public void setPassword(String password) {
        this.password = password;
    }

    public String getStatus() {
        return status;
    }

    public void setStatus(String status) {
        this.status = status;
    }

    @Override
    public String toString() {
        return "User{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", password='" + password + '\'' +
                ", status='" + status + '\'' +
                '}';
    }
}
```

3. 数据库创建表并添加数据

```sql
create table `user` (
	`id` int unsigned primary key auto_increment,
	`name` varchar(30) not null unique key,
	`password` varchar(32) not null,
	`status` enum('0', '1', '2') default '2'
) engine=MyISAM default charset=utf8;

insert into `user` (`name`, `password`, `status`) values
('root', 'root', '0'),
('admin', 'admin', '1'),
('andy', 'andy', '2'),
('ice', 'ice', '2'),
('jack', 'jack', '2'),
('rose', 'rose', '2');
```

4. 创建UserMapper.xml

```xml
<?xml version="1.0" encoding="UTF8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "https://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="userMapper">
    <select id="selAll" resultType="com.example.domain.User">
        select * from user
    </select>
</mapper>
```

5. 创建SqlMapConfig.xml

```xml
<?xml version="1.0" encoding="UTF8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "https://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <environments default="test01">
        <environment id="test01">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://127.0.0.1:3306/mybatis_data?useSSL=true"/>
                <property name="username" value="root"/>
                <property name="password" value="root"/>
            </dataSource>
        </environment>
    </environments>
    <mappers>
        <mapper resource="UserMapper.xml"/>
    </mappers>
</configuration>
```

6. 测试类MybatisTest

```java
package com.example.test;

import com.example.domain.User;
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import org.junit.Test;

import java.io.IOException;
import java.io.InputStream;
import java.util.List;

public class MyBatisTest {
    @Test
    public void test01() throws IOException {
        // 获取核心配置文件
        InputStream resourceAsStream = Resources.getResourceAsStream("SqlMapConfig.xml");
        // 获取session工厂对象
        SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(resourceAsStream);
        // 获取session会话对象
        SqlSession sqlSession = sqlSessionFactory.openSession();

        // 通过会话对象执行xml配置文件中的namespace.id
        List<User> userList = sqlSession.selectList("userMapper.selAll");

        // 输出结果
        for (User user : userList) {
            System.out.println(user);
        }

        // 释放资源
        sqlSession.close();
    }
}
```


# 三、CRUD

1. 导入坐标

```xml
<dependency>
	<groupId>log4j</groupId>
	<artifactId>log4j</artifactId>
	<version>1.2.17</version>
</dependency>
```

2. 添加log4j.properties配置文件

```properties
### direct log messages to stdout ###
log4j.appender.stdout=org.apache.log4j.ConsoleAppender
log4j.appender.stdout.Target=system.out
log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.ConversionPattern=%d{ABSOLUTE} %5p %c{1}:%L - %m%n

### direct messages to file mylog.log ###
log4j.appender.file=org.apache.log4j.FileAppender
log4j.appender.file.File=c:/mylog.log
log4j.appender.file.layout=org.apache.log4j.PatternLayout
log4j.appender.file.layout.ConversionPattern=%d{ABSOLUTE} %5p %c{1}:%L - %m%n
### set log levels - for more verbose logging change 'info' to 'debug' ###
log4j.rootLogger=debug, stdout
```

3. 编辑UserMapper.xml

```xml
<?xml version="1.0" encoding="UTF8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "https://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="userMapper">
    <!-- 查询标签，可自定义其中的sql语句，resultType表示查询后封装的实体类 -->
    <select id="selAll" resultType="com.example.domain.User">
        select * from user
    </select>
    <!-- insert 数据表中插入数据 -->
    <insert id="add" parameterType="com.example.domain.User">
        insert into user (name, password, status) value (#{name}, #{password}, #{status})
    </insert>
    <!-- update 数据表中修改数据 -->
    <update id="update" parameterType="com.example.domain.User">
        update user set password=#{password}, status=#{status} where name=#{name}
    </update>
    <!-- delete 跟据对象删除数据表中的数据 -->
    <delete id="deleteByUserObj" parameterType="com.example.domain.User">
        delete from user where name=#{name}
    </delete>
    <!-- delete 跟据用户名删除数据表中的数据 -->
    <delete id="deleteByName" parameterType="String">
        delete from user where name=#{name}
    </delete>
    <!-- delete 跟据ID删除数据表中的数据 -->
    <delete id="deleteById" parameterType="Integer">
        delete from user where id=#{id}
    </delete>
</mapper>
```

4. MyBatisTest添加测试

```java
package com.example.test;

import com.example.domain.User;
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import org.junit.Before;
import org.junit.Test;

import java.io.IOException;
import java.io.InputStream;
import java.util.List;

public class MyBatisTest {
    private SqlSession sqlSession;

    @Before
    public void before() throws IOException {
        // 获取核心配置文件
        InputStream resourceAsStream = Resources.getResourceAsStream("SqlMapConfig.xml");
        // 获取session工厂对象
        SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(resourceAsStream);
        // 获取session会话对象
        sqlSession = sqlSessionFactory.openSession();
    }

    @Test
    public void test01() {
        // 通过会话对象执行xml配置文件中的namespace.id
        List<User> userList = sqlSession.selectList("userMapper.selAll");

        // 输出结果
        for (User user : userList) {
            System.out.println(user);
        }

        // 释放资源
        sqlSession.close();
    }

    @Test
    public void test02() {
        User mike = new User("mike", "123", "2");
        int row = sqlSession.insert("userMapper.add", mike);
        System.out.println(row);
        sqlSession.close();
    }

    @Test
    public void test03() {
        User mike = new User("mike", "456", "2");
        int row = sqlSession.update("userMapper.update", mike);
        System.out.println(row);
        sqlSession.close();
    }

    @Test
    public void test04() {
        User mike = new User("mike", "456", "2");
        int row = sqlSession.delete("userMapper.deleteByUserObj", mike);
        System.out.println(row);
        sqlSession.close();
    }

    @Test
    public void test05() {
        int row = sqlSession.delete("userMapper.deleteByName", "rose");
        System.out.println(row);
        sqlSession.close();
    }

    @Test
    public void test06() {
        int row = sqlSession.delete("userMapper.deleteById", 5);
        System.out.println(row);
        sqlSession.close();
    }
}
```


# 四、MyBatis配置文件

## 1. 配置文件的顶层结构

- configuration（配置）
	- properties（属性）
	- settings（设置）
	- typeAliases（类型别名）
	- typeHandles（类型处理器）
	- objectFactory（对象工厂）
	- plugins（插件）
	- environments（环境配置）
		- environment（环境变量）
			- transactionManager（事务管理器）
			- dataSource（数据源）
	- databaseIdProvider（数据库厂商标识）
	- mappers（映射器）

## 2. 常用配置

### 1) environments

> 详见手册：https://mybatis.org/mybatis-3/zh/configuration.html#environments

- 事务管理器
	- **JDBC**（常用）
	- MANAGED
- 连接池
	- **POOLED**（常用）
	- UNPOOLED
	- JNDI

### 2) mappers

- mapper标签的四种引用形式：https://mybatis.org/mybatis-3/zh/configuration.html#mappers

### 3) properties

> 用于加载配置文件，修改`SqlMapConfig.xml`

```xml
<?xml version="1.0" encoding="UTF8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "https://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <!-- 引入外部的properties配置文件 -->
    <properties resource="jdbc.properties"/>
    <!-- 配置数据源 -->
    <environments default="test01">
        <environment id="test01">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="${JDBC_DRIVER}"/>
                <property name="url" value="${JDBC_URL}"/>
                <property name="username" value="${JDBC_USERNAME}"/>
                <property name="password" value="${JDBC_PASSWORD}"/>
            </dataSource>
        </environment>
    </environments>
    <mappers>
        <mapper resource="UserMapper.xml"/>
    </mappers>
</configuration>
```

jdbc.properties

```properties
JDBC_DRIVER=com.mysql.cj.jdbc.Driver  
JDBC_URL=jdbc:mysql://127.0.0.1:3306/mybatis_data?useSSL=true  
JDBC_USERNAME=root  
JDBC_PASSWORD=root
```

### 4) typeAliases

> 用于给类型起别名，修改`SqlMapConfig.xml`

```xml
<!-- 配置类型别名 -->
<typeAliases>
	<typeAlias type="com.example.domain.User" alias="User" />
</typeAliases>
```

UserMapper.xml

```xml
<select id="selAll" resultType="User">
	select * from user
</select>
```


## 3. MyBatis实现Dao

> 通过动态代理模式操作MyBatis

- Mapper接口开发规范
	- Mapper.xml文件中的namespace与mapper接口的权限定名必须相同
	- Mapper接口方名和Mapper.xml中定义的每个statement的id相同
	- Mapper接口方法的输入参数类型和Mapper.xml中定义的每个sql的parameterType的类型相同
	- Mapper接口方法的输出参数类型和Mapper.xml中定义的每个sql的resultType的类型相同

1. UserMapperInterface.xml

```xml
<?xml version="1.0" encoding="UTF8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "https://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.example.dao.UserMapper">
    <select id="selAllUser" resultType="User">  
	    select * from user  
	</select>  
	<select id="selUser" resultType="User" parameterType="String">  
	    select * from user where name=#{name}  
	</select>  
	<insert id="add" parameterType="User">  
	    insert into user (name, password, status) value (#{name}, #{password}, #{status})  
	</insert>  
	<update id="update" parameterType="User">  
	    update user set password=#{password}, status=#{status} where name=#{name}  
	</update>  
	<delete id="delete" parameterType="String">  
	    delete from user where name=#{name}  
	</delete>
</mapper>
```

2. UserMapper.java

```java
package com.example.dao;

import com.example.domain.User;

import java.util.List;

public interface UserMapper {
    List<User> selAllUser();  
	User selUser(String name);  
	void add(User user);  
	void update(User user);  
	void delete(String name);
}
```

3. MyBatisInterfaceTest.java

```java
package com.example.test;

import com.example.dao.UserMapper;
import com.example.domain.User;
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import org.junit.Before;
import org.junit.Test;

import java.io.IOException;
import java.io.InputStream;
import java.util.List;

public class MyBatisInterfaceTest {
    private SqlSession sqlSession;
    private UserMapper mapper;
    @Before
    public void before() throws IOException {
        // 获取核心配置文件
        InputStream resourceAsStream = Resources.getResourceAsStream("SqlMapConfig.xml");
        // 获取session工厂对象
        SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(resourceAsStream);
        // 获取session会话对象
        sqlSession = sqlSessionFactory.openSession();
        // 获取mapper实例
        mapper = sqlSession.getMapper(UserMapper.class);
    }

    @Test
    public void test01() {
        List<User> users = mapper.selAllUser();
        for (User user : users) {
            System.out.println(user);
        }
    }

    @Test
    public void test02() {
        User root = mapper.selUser("root");
        System.out.println(root);
    }
    
	@Test  
	public void test03() {  
	    mapper.add(new User("rally", "rally", "2"));  
	}  
	  
	@Test  
	public void test04() {  
	    mapper.update(new User("rally", "000", "2"));  
	}  
	  
	@Test  
	public void test05() {  
	    mapper.delete("rally");  
	}
}
```


## 4. 动态SQL

- **if**（重点）
- choose(when, otherwrise)
- trim(where, set)
- **foreach**

> UserMaperInterface.xml

```xml
<!-- 动态SQL -->
<select id="selBySomething" resultType="User" parameterType="User">
	select * from user
	<where>
		<if test="id!=null">
			and id=#{id}
		</if>
		<if test="name!=null">
			and name=#{name}
		</if>
		<if test="password!=null">
			and password=#{password}
		</if>
		<if test="status!=null">
			and status=#{status}
		</if>
	</where>
</select>
<select id="selByNames" resultType="User" parameterType="list">
	select * from user
	<where>
		<foreach item="name" collection="list" open="name in (" close=")" separator=",">
			#{name}
		</foreach>
	</where>
</select>
```

> UserMapper.java

```java
package com.example.dao;  
  
import com.example.domain.User;  
  
import java.util.List;  
  
public interface UserMapper {  
    List<User> selAllUser();  
    User selUser(String name);  
    void add(User user);  
    void update(User user);  
    void delete(String name);  
    List<User> selBySomething(User user);  
    List<User> selByNames(List<String> names);  
}
```

> MyBatisInterfaceTest.java

```java
@Test  
public void test06() {  
    List<User> users = mapper.selBySomething(new User(1,null, null, "0"));  
    for (User user : users) {  
        System.out.println(user);  
    }  
}  
  
@Test  
public void test07() {  
    ArrayList<String> names = new ArrayList<String>();  
    names.add("root");  
    names.add("ice");  
    names.add("jack");  
    List<User> users = mapper.selByNames(names);  
    for (User user : users) {  
        System.out.println(user);  
    }  
}
```

## 5. SQL抽取

> UserMapperInterface.xml

```xml
<select id="selAllField">
	select * from user
</select>
<select id="selAllUser" resultType="User">
	<include refid="selAllField"/>
</select>
<select id="selUser" resultType="User" parameterType="String">
	<include refid="selAllField"/>
	where name=#{name}
</select>
```

## 6. typeHandlers

> 配置类型转换器：https://mybatis.org/mybatis-3/zh/configuration.html#typeHandlers

```java
// ExampleTypeHandler.java
@MappedJdbcTypes(JdbcType.VARCHAR)
public class ExampleTypeHandler extends BaseTypeHandler<String> {

  @Override
  public void setNonNullParameter(PreparedStatement ps, int i, String parameter, JdbcType jdbcType) throws SQLException {
    ps.setString(i, parameter);
  }

  @Override
  public String getNullableResult(ResultSet rs, String columnName) throws SQLException {
    return rs.getString(columnName);
  }

  @Override
  public String getNullableResult(ResultSet rs, int columnIndex) throws SQLException {
    return rs.getString(columnIndex);
  }

  @Override
  public String getNullableResult(CallableStatement cs, int columnIndex) throws SQLException {
    return cs.getString(columnIndex);
  }
}
```

```xml
<!-- mybatis-config.xml -->
<typeHandlers>
  <typeHandler handler="org.mybatis.example.ExampleTypeHandler"/>
</typeHandlers>
```

## 7. plugins

> mybatis插件：https://mybatis.org/mybatis-3/zh/configuration.html#plugins
> 例如，pageHelper：https://pagehelper.github.io/


# 8. 多表操作

## 1) 1对1关系

1. 数据库创建student和lession表并填充数据

```sql
create table `student` (
	`stu_id` int unsigned not null auto_increment,
	`stu_name` varchar(30) not null,
	`stu_gender` enum('-1', '0', '1') default '-1',
	`stu_age` int unsigned default '0',
	`lession_id` int unsigned default '0',
	 primary key(`stu_id`)
);

insert into `student` (`stu_name`, `stu_gender`, `stu_age`, `lession_id`) values
('rose', '0', '17', '1'),
('jack', '1', '18', '0'),
('lily', '0', '16', '1');

create table `lession` (
	`lession_id` int unsigned primary key auto_increment,
	`lession_name` varchar(60) not null,
	`lession_techer` varchar(30) not null
);

insert into `lession` (`lession_name`, `lession_techer`) values
('itjava', 'aa'),
('itgolang', 'bb');

-- select * from `student`, `lession` where `student`.`lession_id` = `lession`.`lession_id`;
```

3. 创建Student和Lession实体类

```java
package com.example.domain;

public class Lession {
    private Integer lession_id;
    private String lession_name;
    private String lession_teacher;

    public Lession() {
    }

    public Lession(String lession_name, String lession_teacher) {
        this.lession_name = lession_name;
        this.lession_teacher = lession_teacher;
    }

    public Lession(Integer lession_id, String lession_name, String lession_teacher) {
        this.lession_id = lession_id;
        this.lession_name = lession_name;
        this.lession_teacher = lession_teacher;
    }

    public Integer getLession_id() {
        return lession_id;
    }

    public void setLession_id(Integer lession_id) {
        this.lession_id = lession_id;
    }

    public String getLession_name() {
        return lession_name;
    }

    public void setLession_name(String lession_name) {
        this.lession_name = lession_name;
    }

    public String getLession_teacher() {
        return lession_teacher;
    }

    public void setLession_teacher(String lession_teacher) {
        this.lession_teacher = lession_teacher;
    }

    @Override
    public String toString() {
        return "Lession{" +
                "lession_id=" + lession_id +
                ", lession_name='" + lession_name + '\'' +
                ", lession_teacher='" + lession_teacher + '\'' +
                '}';
    }
}
```

```java
package com.example.domain;

public class Student {
    private Integer stu_id;
    private String stu_name;
    private String stu_gender;
    private Integer stu_age;
    private Integer lession_id;
    private Lession lession;

    public Student() {
    }

    public Student(String stu_name, String stu_gender, Integer stu_age, Integer lession_id, Lession lession) {
        this.stu_name = stu_name;
        this.stu_gender = stu_gender;
        this.stu_age = stu_age;
        this.lession_id = lession_id;
        this.lession = lession;
    }

    public Student(Integer stu_id, String stu_name, String stu_gender, Integer stu_age, Integer lession_id, Lession lession) {
        this.stu_id = stu_id;
        this.stu_name = stu_name;
        this.stu_gender = stu_gender;
        this.stu_age = stu_age;
        this.lession_id = lession_id;
        this.lession = lession;
    }

    public Integer getStu_id() {
        return stu_id;
    }

    public void setStu_id(Integer stu_id) {
        this.stu_id = stu_id;
    }

    public String getStu_name() {
        return stu_name;
    }

    public void setStu_name(String stu_name) {
        this.stu_name = stu_name;
    }

    public String getStu_gender() {
        return stu_gender;
    }

    public void setStu_gender(String stu_gender) {
        this.stu_gender = stu_gender;
    }

    public Integer getStu_age() {
        return stu_age;
    }

    public void setStu_age(Integer stu_age) {
        this.stu_age = stu_age;
    }

    public Integer getLession_id() {
        return lession_id;
    }

    public void setLession_id(Integer lession_id) {
        this.lession_id = lession_id;
    }

    public Lession getLession() {
        return lession;
    }

    public void setLession(Lession lession) {
        this.lession = lession;
    }

    @Override
    public String toString() {
        return "Student{" +
                "stu_id=" + stu_id +
                ", stu_name='" + stu_name + '\'' +
                ", stu_gender='" + stu_gender + '\'' +
                ", stu_age=" + stu_age +
                ", lession_id=" + lession_id +
                ", lession=" + lession +
                '}';
    }
}
```

3. 创建StudentMapper接口

```java
package com.example.dao;

import com.example.domain.Student;

import java.util.List;

public interface StudentMapper {
    List<Student> selStudentLession();
}
```

4. 创建StudentMapper.xml

```xml
<?xml version="1.0" encoding="UTF8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "https://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.example.dao.StudentMapper">
    <resultMap id="studentLession" type="Student">
        <id column="stu_id" property="stu_id"/>
        <result column="stu_name" property="stu_name"/>
        <result column="stu_gender" property="stu_gender"/>
        <result column="stu_age" property="stu_age"/>
        <result column="lession_id" property="lession_id"/>
        <association property="lession" javaType="Lession">
            <id column="lession_id" property="lession_id"/>
            <result column="lession_name" property="lession_name"/>
            <result column="lession_teacher" property="lession_teacher"/>
        </association>
    </resultMap>
    <select id="selStudentLession" resultMap="studentLession">
        select * from `student`, `lession` where `student`.`lession_id` = `lession`.`lession_id`
    </select>
</mapper>
```

5. 配置SqlMapConfig.xml

```xml
<?xml version="1.0" encoding="UTF8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "https://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <!-- 引入外部的properties配置文件 -->
    <properties resource="jdbc.properties"/>
    <!-- 配置类型别名 -->
    <typeAliases>
        <typeAlias type="com.example.domain.User" alias="User" />
        <typeAlias type="com.example.domain.Student" alias="Student" />
        <typeAlias type="com.example.domain.Lession" alias="Lession" />
    </typeAliases>
    <!-- 配置数据源 -->
    <environments default="test01">
        <environment id="test01">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="${JDBC_DRIVER}"/>
                <property name="url" value="${JDBC_URL}"/>
                <property name="username" value="${JDBC_USERNAME}"/>
                <property name="password" value="${JDBC_PASSWORD}"/>
            </dataSource>
        </environment>
    </environments>
    <mappers>
        <!-- <mapper resource="UserMapper.xml"/> -->
        <!-- <mapper resource="UserMapperInterface.xml"/> -->
        <mapper resource="StudentMapper.xml"/>
    </mappers>
</configuration>
```

6. 添加测试类MyBatisStudentTest.java

```java
package com.example.test;

import com.example.dao.StudentMapper;
import com.example.domain.Student;
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import org.junit.Before;
import org.junit.Test;

import java.io.IOException;
import java.io.InputStream;
import java.util.List;

public class MyBatisStudentTest {
    private SqlSession sqlSession;
    private StudentMapper mapper;

    @Before
    public void before() throws IOException {
        // 获取核心配置文件
        InputStream resourceAsStream = Resources.getResourceAsStream("SqlMapConfig.xml");
        // 获取session工厂对象
        SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(resourceAsStream);
        // 获取session会话对象
        sqlSession = sqlSessionFactory.openSession();
        mapper = sqlSession.getMapper(StudentMapper.class);
    }

    @Test
    public void test01() {
        List<Student> students = mapper.selStudentLession();
        for (Student stu : students) {
            System.out.println(stu);
        }
    }
}
```

## 2) 1对多关系

1. Lession实体类添加`private List<Student> studentList`属性

```java
package com.example.domain;

import java.util.List;

public class Lession {
    private Integer lession_id;
    private String lession_name;
    private String lession_teacher;
    private List<Student> studentList;

    public Lession() {
    }

    public Lession(String lession_name, String lession_teacher) {
        this.lession_name = lession_name;
        this.lession_teacher = lession_teacher;
    }

    public Lession(Integer lession_id, String lession_name, String lession_teacher) {
        this.lession_id = lession_id;
        this.lession_name = lession_name;
        this.lession_teacher = lession_teacher;
    }

    public Integer getLession_id() {
        return lession_id;
    }

    public void setLession_id(Integer lession_id) {
        this.lession_id = lession_id;
    }

    public String getLession_name() {
        return lession_name;
    }

    public void setLession_name(String lession_name) {
        this.lession_name = lession_name;
    }

    public String getLession_teacher() {
        return lession_teacher;
    }

    public void setLession_teacher(String lession_teacher) {
        this.lession_teacher = lession_teacher;
    }

    public List<Student> getStudentList() {
        return studentList;
    }

    public void setStudentList(List<Student> studentList) {
        this.studentList = studentList;
    }

    @Override
    public String toString() {
        return "Lession{" +
                "lession_id=" + lession_id +
                ", lession_name='" + lession_name + '\'' +
                ", lession_teacher='" + lession_teacher + '\'' +
                ", studentList=" + studentList +
                '}';
    }
}
```

2. 创建LessionMapper接口

```java
package com.example.dao;

import com.example.domain.Lession;

import java.util.List;

public interface LessionMapper {
    List<Lession> selLession();
}
```

3. 创建LessionMapper.xml

```xml
<?xml version="1.0" encoding="UTF8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "https://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.example.dao.LessionMapper">
    <resultMap id="lessionStudentMap" type="Lession">
        <id column="lession_id" property="lession_id"/>
        <result column="lession_name" property="lession_name"/>
        <result column="lession_teacher" property="lession_teacher"/>
        <collection property="studentList" ofType="Student">
            <id column="stu_id" property="stu_id"/>
            <result column="stu_name" property="stu_name"/>
            <result column="stu_gender" property="stu_gender"/>
            <result column="stu_age" property="stu_age"/>
        </collection>
    </resultMap>
    <select id="selLession" resultMap="lessionStudentMap">
        select * from `lession`, `student` where `lession`.`lession_id` = `student`.`lession_id`
    </select>
</mapper>
```

4. 修改SqlMapConfig.xml配置

```xml
<?xml version="1.0" encoding="UTF8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "https://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <!-- 引入外部的properties配置文件 -->
    <properties resource="jdbc.properties"/>
    <!-- 配置类型别名 -->
    <typeAliases>
        <typeAlias type="com.example.domain.User" alias="User" />
        <typeAlias type="com.example.domain.Student" alias="Student" />
        <typeAlias type="com.example.domain.Lession" alias="Lession" />
    </typeAliases>
    <!-- 配置数据源 -->
    <environments default="test01">
        <environment id="test01">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="${JDBC_DRIVER}"/>
                <property name="url" value="${JDBC_URL}"/>
                <property name="username" value="${JDBC_USERNAME}"/>
                <property name="password" value="${JDBC_PASSWORD}"/>
            </dataSource>
        </environment>
    </environments>
    <mappers>
        <!-- <mapper resource="UserMapper.xml"/> -->
        <!-- <mapper resource="UserMapperInterface.xml"/> -->
        <!-- <mapper resource="StudentMapper.xml"/> -->
        <mapper resource="LessionMapper.xml"/>
    </mappers>
</configuration>
```

5. 添加测试

```java
@Test
public void test02() {
	LessionMapper mapper = sqlSession.getMapper(LessionMapper.class);
	List<Lession> lessions = mapper.selLession();
	for (Lession lession : lessions) {
		System.out.println(lession);
	}
}
// Lession{lession_id=1, lession_name='itjava', lession_teacher='aa', studentList=[Student{stu_id=1, stu_name='rose', stu_gender='0', stu_age=17, lession_id=null, lession=null}, Student{stu_id=3, stu_name='lily', stu_gender='0', stu_age=16, lession_id=null, lession=null}]}
// Lession{lession_id=2, lession_name='itgolang', lession_teacher='bb', studentList=[Student{stu_id=2, stu_name='jack', stu_gender='1', stu_age=18, lession_id=null, lession=null}]}
```

## 3) 多对多关系

1. 修改Student实体类

```java
package com.example.domain;

import java.util.List;

public class Student {
    private Integer stu_id;
    private String stu_name;
    private String stu_gender;
    private Integer stu_age;
    // private Integer lession_id;
    // private Lession lession;
    private List<Lession> lessions;

    public Student() {
    }

    public Student(String stu_name, String stu_gender, Integer stu_age, List<Lession> lessions) {
        this.stu_name = stu_name;
        this.stu_gender = stu_gender;
        this.stu_age = stu_age;
        this.lessions = lessions;
    }

    public Student(Integer stu_id, String stu_name, String stu_gender, Integer stu_age, List<Lession> lessions) {
        this.stu_id = stu_id;
        this.stu_name = stu_name;
        this.stu_gender = stu_gender;
        this.stu_age = stu_age;
        this.lessions = lessions;
    }

    public List<Lession> getLessions() {
        return lessions;
    }

    public void setLessions(List<Lession> lessions) {
        this.lessions = lessions;
    }

    public Integer getStu_id() {
        return stu_id;
    }

    public void setStu_id(Integer stu_id) {
        this.stu_id = stu_id;
    }

    public String getStu_name() {
        return stu_name;
    }

    public void setStu_name(String stu_name) {
        this.stu_name = stu_name;
    }

    public String getStu_gender() {
        return stu_gender;
    }

    public void setStu_gender(String stu_gender) {
        this.stu_gender = stu_gender;
    }

    public Integer getStu_age() {
        return stu_age;
    }

    public void setStu_age(Integer stu_age) {
        this.stu_age = stu_age;
    }

    @Override
    public String toString() {
        return "Student{" +
                "stu_id=" + stu_id +
                ", stu_name='" + stu_name + '\'' +
                ", stu_gender='" + stu_gender + '\'' +
                ", stu_age=" + stu_age +
                ", lessions=" + lessions +
                '}';
    }
}
```

2. 创建StudentLessonsMapper接口

```java
package com.example.dao;

import com.example.domain.Student;

import java.util.List;

public interface StudentLessonsMapper {
    List<Student> selStudentLessons();
}
```

3. 创建 StudentLessonsMapper.xml

```xml
<?xml version="1.0" encoding="UTF8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "https://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.example.dao.StudentLessonsMapper">
    <resultMap id="stuLess" type="Student">
        <id column="stu_id" property="stu_id"/>
        <result column="stu_name" property="stu_name"/>
        <collection property="lessions" ofType="Lession">
            <id column="lession_id" property="lession_id"/>
            <result column="lession_name" property="lession_name"/>
        </collection>
    </resultMap>
    <select id="selStudentLessons" resultMap="stuLess">
        select `student`.`stu_id`, `student`.`stu_name`, `lession`.`lession_id`, `lession`.`lession_name` from `student`, `student_sel_lesson` as `sl`, `lession` where `student`.`stu_id` = `sl`.`stu_id` and `lession`.`lession_id` = `sl`.`lesson_id`;
    </select>
</mapper>
```

4. 修改SqlMapConfig配置

```xml
<mappers>
	<!-- <mapper resource="UserMapper.xml"/> -->
	<!-- <mapper resource="UserMapperInterface.xml"/> -->
	<!-- <mapper resource="StudentMapper.xml"/> -->
	<!-- <mapper resource="LessionMapper.xml"/> -->
	<mapper resource="StudentLessonsMapper.xml"/>
</mappers>
```

5. 添加测试

```java
@Test
public void test03() {
	StudentLessonsMapper mapper = sqlSession.getMapper(StudentLessonsMapper.class);
	List<Student> students = mapper.selStudentLessons();
	for (Student stu : students) {
		System.out.println(stu);
	}
}
```


# 9. MyBatis注解

## 1) 常用注解

- @Select 查询
- @Insert 插入
- @Update 修改
- @Delete 删除
- @Result 结果集封装
- @Results 结果集封装（结合@Result使用）
- @One 一对一结果集封装
- @Many 一对多结果集封装

## 2) 操作方法

### a. 基本CRUD

1. 配置SqlMapConfig.xml

```xml
<?xml version="1.0" encoding="UTF8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "https://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <!-- 引入外部的properties配置文件 -->
    <properties resource="jdbc.properties"/>
    <!-- 配置类型别名 -->
    <typeAliases>
        <typeAlias type="com.example.domain.User" alias="User" />
        <typeAlias type="com.example.domain.Student" alias="Student" />
        <typeAlias type="com.example.domain.Lession" alias="Lession" />
    </typeAliases>
    <!-- 配置数据源 -->
    <environments default="test01">
        <environment id="test01">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="${JDBC_DRIVER}"/>
                <property name="url" value="${JDBC_URL}"/>
                <property name="username" value="${JDBC_USERNAME}"/>
                <property name="password" value="${JDBC_PASSWORD}"/>
            </dataSource>
        </environment>
    </environments>
    <!-- <mappers> -->
    <!--     &lt;!&ndash; <mapper resource="UserMapper.xml"/> &ndash;&gt; -->
    <!--     &lt;!&ndash; <mapper resource="UserMapperInterface.xml"/> &ndash;&gt; -->
    <!--     &lt;!&ndash; <mapper resource="StudentMapper.xml"/> &ndash;&gt; -->
    <!--     &lt;!&ndash; <mapper resource="LessionMapper.xml"/> &ndash;&gt; -->
    <!--     &lt;!&ndash; <mapper resource="StudentLessonsMapper.xml"/> &ndash;&gt; -->
    <!-- </mappers> -->

    <!-- 配置扫描包 -->
    <mappers>
        <package name="com.example.dao"/>
    </mappers>
</configuration>
```

2. 在UserMapper接口添加注解

```java
package com.example.dao;

import com.example.domain.User;
import org.apache.ibatis.annotations.Delete;
import org.apache.ibatis.annotations.Insert;
import org.apache.ibatis.annotations.Select;
import org.apache.ibatis.annotations.Update;

import java.util.List;

public interface UserMapper {
    @Select("select * from `user`")
    List<User> selAllUser();
    @Select("select * from `user` where name=#{name}")
    User selUser(String name);
    @Insert("insert into user (name, password, status) value (#{name}, #{password}, #{status})")
    void add(User user);
    @Update("update user set password=#{password}, status=#{status} where name=#{name}")
    void update(User user);
    @Delete("delete from user where name=#{name}")
    void delete(String name);
}
```

3. 运行测试

```java
package com.example.test;

import com.example.dao.UserMapper;
import com.example.domain.User;
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import org.junit.Before;
import org.junit.Test;

import java.io.IOException;
import java.io.InputStream;
import java.util.ArrayList;
import java.util.List;

public class MyBatisInterfaceTest {
    private SqlSession sqlSession;
    private UserMapper mapper;
    @Before
    public void before() throws IOException {
        // 获取核心配置文件
        InputStream resourceAsStream = Resources.getResourceAsStream("SqlMapConfig.xml");
        // 获取session工厂对象
        SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(resourceAsStream);
        // 获取session会话对象
        sqlSession = sqlSessionFactory.openSession();
        // 获取mapper实例
        mapper = sqlSession.getMapper(UserMapper.class);
    }

    @Test
    public void test01() {
        List<User> users = mapper.selAllUser();
        for (User user : users) {
            System.out.println(user);
        }
    }

    @Test
    public void test02() {
        User root = mapper.selUser("root");
        System.out.println(root);
    }

    @Test
    public void test03() {
        mapper.add(new User("rally", "rally", "2"));
    }

    @Test
    public void test04() {
        mapper.update(new User("rally", "000", "2"));
    }

    @Test
    public void test05() {
        mapper.delete("rally");
    }
}
```

### b. 注解过script实现动态SQL

```java
@Select({
		"<script>",
			"select * from `user`",
			"<where>",
				"<if test='id!=null'>id=#{id}</if>",
				"<if test='name!=null'>name=#{name}</if>",
			"</where>",
		"</script>"
})
List<User> selBySomething(User user);
```

```java
@Test
public void test06() {
	List<User> users = mapper.selBySomething(new User(1,null, null, "0"));
	for (User user : users) {
		System.out.println(user);
	}
}
```

### c. 多表操作

#### 1对1

1. LessionMapper.java

```java
package com.example.dao;

import com.example.domain.Lession;
import org.apache.ibatis.annotations.Select;

public interface LessionMapper {
    @Select("select * from `lession` where `lession_id` = #{lession_id}")
    Lession selLessionById(int lession_id);
}
```

2. StudentMapper.java

```java
package com.example.dao;

import com.example.domain.Lession;
import com.example.domain.Student;
import org.apache.ibatis.annotations.One;
import org.apache.ibatis.annotations.Result;
import org.apache.ibatis.annotations.Results;
import org.apache.ibatis.annotations.Select;

import java.util.List;

public interface StudentMapper {
	// @Select("select * from `student`, `lession` where `student`.`lession_id` = `lession`.`lession_id`")  
	// @Results({  
	//         @Result(column = "stu_id", property = "stu_id"),  
	//         @Result(column = "stu_name", property = "stu_name"),  
	//         @Result(column = "lession_name", property = "lession.lession_name")  
	// })
    @Select("select * from `student`")
    @Results({
            @Result(column = "stu_id", property = "stu_id"),
            @Result(column = "stu_name", property = "stu_name"),
            @Result(
                    column = "lession_id", // 通过外键进行查询
                    property = "lession", // 成员名
                    javaType = Lession.class, // 成员类型
                    one = @One(select = "com.example.dao.LessionMapper.selLessionById") // 一对一调用的方法
            )
    })
    List<Student> selStudentLession();
}
```

3. 测试

```java
@Test  
public void test01() {  
    StudentMapper mapper = sqlSession.getMapper(StudentMapper.class);  
    List<Student> students = mapper.selStudentLession();  
    for (Student stu : students) {  
        System.out.println(stu);  
    }  
}
```

#### 1对多

1. StudentMapper.java

```java
@Select("select * from `student` where `lession_id` = #{lession_id}")  
List<Student> selStudentByLessionId(int lession_id);
```

2. LessionMapper.java

```java
@Select("select * from `lession`")  
@Results({  
        @Result(column = "lession_id", property = "lession_id"),  
        @Result(column = "lession_name", property = "lession_name"),  
        @Result(  
                column = "lession_id",  
                property = "studentList",  
                javaType = List.class,  
                many = @Many(select = "com.example.dao.StudentMapper.selStudentByLessionId")  
        )  
})  
List<Lession> selLessionStudent();
```

3. 测试

```java
@Test  
public void test04() {  
    LessionMapper mapper = sqlSession.getMapper(LessionMapper.class);  
    List<Lession> lessions = mapper.selLessionStudent();  
    for (Lession lession : lessions) {  
        System.out.println(lession);  
    }  
}
```

#### 多对多

1. LessionMapper.java

```java
@Select("select `lession`.`lession_id`, `lession_name` from `student_sel_lesson` `sl`, `lession` where #{stu_id} = `sl`.`stu_id` and `lession`.`lession_id` = `sl`.`lesson_id`")  
List<Lession> selLessionByStuId(int stu_id);
```

2. StudentMapper.java

```java
@Select("select `stu_id`, `stu_name` from `student`")  
@Results({  
        @Result(column = "stu_id", property = "stu_id"),  
        @Result(column = "stu_name", property = "stu_name"),  
        @Result(  
                column = "stu_id",  
                property = "lessions",  
                javaType = List.class,  
                many = @Many(select = "com.example.dao.LessionMapper.selLessionByStuId")  
        )  
})  
List<Student> selStudentLessionMany();
```

3. 测试

```java
@Test  
public void test05() {  
    StudentMapper mapper = sqlSession.getMapper(StudentMapper.class);  
    List<Student> students = mapper.selStudentLessionMany();  
    for (Student stu : students) {  
        System.out.println(stu);  
    }  
}
```


# 10. MyBatis plus

## 1. 简介

https://www.baomidou.com/pages/24112f/

## 2. 快速上手






