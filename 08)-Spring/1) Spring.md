
# 一、简介

> SSM = Spring + SpringMVC + MyuBatis
> IOC（控制反转）& AOP（面向切面编程）


# 二、快速上手

1. 创建JavaEE项目模块
2. 添加spring-context依赖，粘贴到pom.xml中并查看依赖下载是否成功
3. 拱建测试结构

	DAO全称 - Data Access Object 数据访问对象 - 对应的就是 Model模型层

![[Pasted image 20230217172918.png]]

	1. 创建dao包 - 用于模型层操作源码
	2. 在dao包中创建StuDao接口，定义模型层对象成员模板，定义抽象方法show()

```java
package com.maqf.dao;

/**
 * @ClassName StuDao
 * @Description: TODO
 * @Author: maqf22@qq.com
 */
public interface StuDao {
    public abstract void show();
}
```

	3. 创建Impl包 - 用于存储不同接口的实现类
	4. 在Impl包内创建StuDaoImpl实现类，并在实现类中实现接口中的成员方法

```java
package com.maqf.dao.impl;

import com.maqf.dao.StuDao;

/**
 * @ClassName StuDaoImpl
 * @Description: TODO
 * @Author: maqf22@qq.com
 */
public class StuDaoImpl implements StuDao {
    @Override
    public void show() {
        System.out.println("StuDaoImpl Show!");
    }
}
```

	5. 创建 `ApplicationContext.xml`文件并添加内容

> 在main/java/resources目录中创建xml配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

	<!-- 添加一个对象配置，相当于是在定义一个实例化对象的过程，通过调用getBean()方法来获取对象 -->
	<!-- id：beans容器中的唯一标识 -->
	<!-- class：类的全路径 -->
    <bean id="StuDaoImpl" class="com.maqf.dao.impl.StuDaoImpl"></bean>

</beans>
```

	6. 编写测试代码

> 在test/java目录中创建com.maqf.runTest包，在包内创建StuTest.java测试代码文件

```java
package com.maqf.runTest;

import com.maqf.dao.StuDao;
import org.junit.jupiter.api.Test;
import org.springframework.context.support.ClassPathXmlApplicationContext;

/**
 * @ClassName StuDaoTest
 * @Description: TODO
 * @Author: maqf22@qq.com
 */
public class StuTest {
    @Test
    public void test01() {
		// 通过Spring配置文件获取容器对
        ClassPathXmlApplicationContext app = new ClassPathXmlApplicationContext("ApplicationContext.xml");
		// 在容器中通过id来获取对象
        StuDao stuDao = (StuDao) app.getBean("StuDaoImpl");
        stuDao.show();
    }
}
```


# 三、Spring配置文件

## 1. \<bean\>的基本配置

> 每一个`<bean>`标签都相当于在实例化一个对象，这个动作由Spring内部实现

- 属性
	- id：beans容器中的唯一标识
	- class：类的全路径名
	- scope：对象的作用范围
		- **singleton** - 单例（默认）
		- **prototype** - 多例
		- request - Web项目中，Spring创建一个bean对象的同时将对象存入到request中
		- session - Web项目中，Spring创建一个bean对象的同时将对象存入到session中
		- global session - Web项目中，Spring创建一个bean对象的同时将对象存入到全局的session中

> 测试：singleton和prototype的效果

- 修改ApplicationContext配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

<!--    <bean id="StuDaoImpl" class="com.maqf.dao.impl.StuDaoImpl" scope="singleton"></bean>-->
    <bean id="StuDaoImpl" class="com.maqf.dao.impl.StuDaoImpl" scope="prototype"></bean>

</beans>
```

- 在StuDaoTest.java中添加无参构造方法并输出对象的toString值

```java
package com.maqf.runTest;

import com.maqf.dao.StuDao;
import org.junit.jupiter.api.Test;
import org.springframework.context.support.ClassPathXmlApplicationContext;

/**
 * @ClassName StuDaoTest
 * @Description: TODO
 * @Author: maqf22@qq.com
 */
public class StuTest {
    @Test
    public void test01() {
        ClassPathXmlApplicationContext app = new ClassPathXmlApplicationContext("ApplicationContext.xml");
        // 对象在获取时创建
        StuDao stuDao = (StuDao) app.getBean("StuDaoImpl");
        StuDao stuDao2 = (StuDao) app.getBean("StuDaoImpl");
//        stuDao.show();
        System.out.println(stuDao);
        System.out.println(stuDao2);
    }
}
```

- singleton与prototype创建对象的时机

> 修改StuDaoImpl类，手动重写无参构造方法，输出内容

```java
package com.maqf.dao.impl;

import com.maqf.dao.StuDao;

/**
 * @ClassName StuDaoImpl
 * @Description: TODO
 * @Author: maqf22@qq.com
 */
public class StuDaoImpl implements StuDao {
    public StuDaoImpl() {
        System.out.println("StuDaoImpl has been created");
    }

    @Override
    public void show() {
        System.out.println("StuDaoImpl Show!");
    }
}
```


## 2. \<bean\>作用范围配置

### a. singleton

- bean的实例化个数：1个
- bean的实例化时机：当Spring核心配置文件被加载的时候，实例化配置的bean实例
- bean的生命周期：
	- 对象的创建：当应用加载，创建容器时，对象就被创建了
	- 对象的运行，只要容器在，对象就一直都存在
	- 对象的销毁：当应用卸载，销毁容器的时候

### b. prototype

- bean的实例化个数：多个
- bean的实例化时机：当调用`getBean()`实例化的时候
- bean的生命周期：
	- 对象的创建：调用`getBean()`
	- 对象的运行：只要容器在，对象就一直存在
	- 对象的销毁：对象长时间不使用，JVM会自动回收


## 3. \<bean\>生命周期配置

- `init-method`：指定类中的初始化方法名
- `destory-method`：指定类中的销毁方法名

> 测试：在StuDaoImpl类中添加两个方法，分别用于测试初化和销毁

```java
package com.maqf.dao.impl;

import com.maqf.dao.StuDao;

/**
 * @ClassName StuDaoImpl
 * @Description: TODO
 * @Author: maqf22@qq.com
 */
public class StuDaoImpl implements StuDao {
    public StuDaoImpl() {
        System.out.println("StuDaoImpl has been created");
    }

    public void initMethod() {
        System.out.println("StuDaoImpl init");
    }

    public void destroyMethod() {
        System.out.println("StuDaoImpl destroy");
    }

    @Override
    public void show() {
        System.out.println("StuDaoImpl Show!");
    }
}
```

> ApplicationContext.xml配置init-method和destroy-method

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="StuDaoImpl" class="com.maqf.dao.impl.StuDaoImpl" scope="singleton" init-method="initMethod" destroy-method="destroyMethod"></bean>
<!--    <bean id="StuDaoImpl" class="com.maqf.dao.impl.StuDaoImpl" scope="prototype"></bean>-->

</beans>
```

> 修改StuDaoImpl类中的scopeTest方法为以下内容

```java
package com.maqf.runTest;

import com.maqf.dao.StuDao;
import org.junit.jupiter.api.Test;
import org.springframework.context.support.ClassPathXmlApplicationContext;

/**
 * @ClassName StuDaoTest
 * @Description: TODO
 * @Author: maqf22@qq.com
 */
public class StuTest {
    @Test
    public void test01() {
        ClassPathXmlApplicationContext app = new ClassPathXmlApplicationContext("ApplicationContext.xml");
        StuDao stuDao = (StuDao) app.getBean("StuDaoImpl");
//        StuDao stuDao2 = (StuDao) app.getBean("StuDaoImpl");
        System.out.println(stuDao);
//        System.out.println(stuDao2);
        stuDao.show();
        app.close();
    }
}
```


## 4. \<bean\>实例化的方式

### a. 无参构造方法实例化

	略

### b. 工厂静态方法实例化

1. 创建一个工厂静态方法

```java
package com.maqf.factory;

import com.maqf.dao.StuDao;
import com.maqf.dao.impl.StuDaoImpl;

/**
 * @ClassName MyFactory
 * @Description: TODO
 * @Author: maqf22@qq.com
 */
public class MyFactory {
    public static StuDao getStuDao() {
        return new StuDao() {
            @Override
            public void show() {
                System.out.println("StuDao匿名内部类");
            }
        };
    }
}
```

2. 调整配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="StuDao" class="com.maqf.factory.MyFactory" factory-method="getStuDao"></bean>

</beans>
```

### c. 工厂实例方法实例化

1. 在`MyFactory`类中添加普通的成员方法

```java
package com.maqf.factory;

import com.maqf.dao.StuDao;
import com.maqf.dao.impl.StuDaoImpl;

/**
 * @ClassName MyFactory
 * @Description: TODO
 * @Author: maqf22@qq.com
 */
public class MyFactory {
    public StuDao getStuDaoObj() {
        return new StuDaoImpl();
    }
}
```

2. 调整配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="MyFactory" class="com.maqf.factory.MyFactory"></bean>
    <bean id="StuDao" factory-bean="MyFactory" factory-method="getStuDaoObj"></bean>

</beans>
```


## 5. \<bean\>依赖注入

### a. 通过set方法

1. 创建service包，并在其中创建StuService接口

```java
package com.maqf.dao;  
  
/**  
 * @ClassName StuService  
 * @Description: TODO  
 * @Author: maqf22@qq.com  
 */public interface StuService {  
    void show();  
}
```

2. 在service包中创建Impl包，并在其中创建StuServiceImpl实类

```java
package com.maqf.dao.impl;

import com.maqf.dao.StuDao;
import com.maqf.dao.StuService;

/**
 * @ClassName StuServiceImpl
 * @Description: TODO
 * @Author: maqf22@qq.com
 */
public class StuServiceImpl implements StuService {
    private StuDao stuDao;

    public void setStuDao(StuDao stuDao) {
        this.stuDao = stuDao;
    }

    @Override
    public void show() {
        stuDao.show();
        System.out.println("StuService show running~");
    }
}
```

3. 调整核心配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="StuDao" class="com.maqf.dao.impl.StuDaoImpl"></bean>
    <bean id="StuDaoService" class="com.maqf.dao.impl.StuServiceImpl">
        <property name="stuDao" ref="StuDao"/>
    </bean>

</beans>
```

4. 测试类中添加测试方法并测试

```java
@Test
public void test03() {
        ClassPathXmlApplicationContext app = new ClassPathXmlApplicationContext("ApplicationContext.xml");
        StuServiceImpl bean = app.getBean(StuServiceImpl.class);
        bean.show();
}
```

### b. 通过带参构造方法

1. 修改StuServiceImpl类，添加带参构造方法，同时手动添加无参构造方法

```java
package com.maqf.dao.impl;

import com.maqf.dao.StuDao;
import com.maqf.dao.StuService;

/**
 * @ClassName StuServiceImpl
 * @Description: TODO
 * @Author: maqf22@qq.com
 */
public class StuServiceImpl implements StuService {
    private StuDao stuDao;

    public StuServiceImpl() {
    }

    public StuServiceImpl(StuDao stuDao) {
        this.stuDao = stuDao;
    }

    @Override
    public void show() {
        stuDao.show();
        System.out.println("StuService show running~");
    }
}
```

2. 调整核心配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="stuDao" class="com.maqf.dao.impl.StuDaoImpl"></bean>
    <bean id="suDaoService" class="com.maqf.dao.impl.StuServiceImpl">
        <constructor-arg name="stuDao" ref="stuDao"/>
    </bean>

</beans>
```

3. 测试


## 6. \<bean\>依赖注入类型

### a. 引用数据类型

略。

### b. 普通数据类型

1. 修改StuDaoImpl源码文件

```java
package com.maqf.dao.impl;

import com.maqf.dao.StuDao;

/**
 * @ClassName StuDaoImpl
 * @Description: TODO
 * @Author: maqf22@qq.com
 */
public class StuDaoImpl implements StuDao {
    private String stuName;
    private int age;
    private float score;

    public StuDaoImpl() {
    }

    public String getStuName() {
        return stuName;
    }

    public void setStuName(String stuName) {
        this.stuName = stuName;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public float getScore() {
        return score;
    }

    public void setScore(float score) {
        this.score = score;
    }

    @Override
    public void show() {
        System.out.printf("name: %s, age: %d, score: %.2f\n", this.stuName, this.age, this.score);
    }
}
```

2. 调整核心配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="stuDao" class="com.maqf.dao.impl.StuDaoImpl">
        <property name="stuName" value="Jack"></property>
        <property name="age" value="29"></property>
        <property name="score" value="99.5"></property>
    </bean>
    <bean id="suDaoService" class="com.maqf.dao.impl.StuServiceImpl">
        <constructor-arg name="stuDao" ref="stuDao"/>
    </bean>

</beans>
```

### c. 集合数据类型

1.  修改StuDaoImpl源码文件

```java
package com.maqf.dao.impl;

import com.maqf.dao.StuDao;
import com.maqf.global.Student;

import java.util.List;
import java.util.Map;
import java.util.Properties;

/**
 * @ClassName StuDaoImpl
 * @Description: TODO
 * @Author: maqf22@qq.com
 */
public class StuDaoImpl implements StuDao {
    private String stuName;
    private int age;
    private float score;

    private List<String> strList;
    private Map<String, Student> stuMap;
    private Properties prop;

    public List<String> getStrList() {
        return strList;
    }

    public void setStrList(List<String> strList) {
        this.strList = strList;
    }

    public Map<String, Student> getStuMap() {
        return stuMap;
    }

    public void setStuMap(Map<String, Student> stuMap) {
        this.stuMap = stuMap;
    }

    public Properties getProp() {
        return prop;
    }

    public void setProp(Properties prop) {
        this.prop = prop;
    }

    public StuDaoImpl() {
    }

    public String getStuName() {
        return stuName;
    }

    public void setStuName(String stuName) {
        this.stuName = stuName;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public float getScore() {
        return score;
    }

    public void setScore(float score) {
        this.score = score;
    }

    @Override
    public void show() {
        System.out.printf("name: %s, age: %d, score: %.2f\n", this.stuName, this.age, this.score);
        System.out.println("list:" + this.strList);
        System.out.println("map:" + this.stuMap);
        System.out.println("properties:" + this.prop);
    }
}

```

2. 修改核心配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="stu01" class="com.maqf.global.Student">
        <property name="name" value="Lily"/>
        <property name="age" value="19"/>
        <property name="score" value="86.5"/>
    </bean>
    <bean id="stu02" class="com.maqf.global.Student">
        <property name="name" value="Ben"/>
        <property name="age" value="21"/>
        <property name="score" value="89"/>
    </bean>
    <bean id="stuDao" class="com.maqf.dao.impl.StuDaoImpl">
        <property name="stuName" value="Jack"></property>
        <property name="age" value="29"></property>
        <property name="score" value="99.5"></property>
        <property name="strList">
            <list>
                <value>Jack</value>
                <value>Rose</value>
            </list>
        </property>
        <property name="stuMap">
            <map>
                <entry key="one" value-ref="stu01"/>
                <entry key="two" value-ref="stu02"/>
            </map>
        </property>
        <property name="prop">
            <props>
                <prop key="01">first</prop>
                <prop key="02">second</prop>
            </props>
        </property>
    </bean>
    <bean id="suDaoService" class="com.maqf.dao.impl.StuServiceImpl">
        <constructor-arg name="stuDao" ref="stuDao"/>
    </bean>

</beans>
```


## 7. 导入其他配置

```xml
<import resource="Student.xml"/>
```


# 四、配置数据源

## 1. 数据源简介

> 数据源（连接池）的作用是提高程序的性能
> 常见的数据源：C3P0 / Druid / DBCP / BoneCP


## 2. 数据源的使用

### a. 手动创建数据源测试

1. 导入数据源的坐标和数据库坐标

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.maqf</groupId>
    <artifactId>SpringDemo-02-ANNO</artifactId>
    <version>1.0-SNAPSHOT</version>
    <name>SpringDemo-02-ANNO</name>
    <packaging>war</packaging>

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <maven.compiler.target>1.8</maven.compiler.target>
        <maven.compiler.source>1.8</maven.compiler.source>
        <junit.version>5.9.1</junit.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>javax.servlet-api</artifactId>
            <version>4.0.1</version>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>org.junit.jupiter</groupId>
            <artifactId>junit-jupiter-api</artifactId>
            <version>${junit.version}</version>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>org.junit.jupiter</groupId>
            <artifactId>junit-jupiter-engine</artifactId>
            <version>${junit.version}</version>
            <scope>test</scope>
        </dependency>
        <!-- https://mvnrepository.com/artifact/org.springframework/spring-context -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>5.3.20</version>
        </dependency>
        <!-- https://mvnrepository.com/artifact/mysql/mysql-connector-java -->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.49</version>
        </dependency>
        <!-- https://mvnrepository.com/artifact/com.mchange/c3p0 -->
        <dependency>
            <groupId>com.mchange</groupId>
            <artifactId>c3p0</artifactId>
            <version>0.9.5.2</version>
        </dependency>
        <!-- https://mvnrepository.com/artifact/com.alibaba/druid -->
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid</artifactId>
            <version>1.2.8</version>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-war-plugin</artifactId>
                <version>3.3.2</version>
            </plugin>
        </plugins>
    </build>
</project>
```

2. 创建数据源（连接池）对象，设置连接信息、输出、关闭！

```java
package com.maqf.runTest;

import com.alibaba.druid.pool.DruidDataSource;
import com.alibaba.druid.pool.DruidPooledConnection;
import com.mchange.v2.c3p0.ComboPooledDataSource;
import org.junit.jupiter.api.Test;

import java.beans.PropertyVetoException;
import java.sql.Connection;
import java.sql.SQLException;

/**
 * @ClassName Test
 * @Description: TODO
 * @Author: maqf22@qq.com
 */
public class AnnoTest {
    private static final String MYSQL_DRIVER = "com.mysql.jdbc.Driver";
    private static final String MYSQL_URL = "jdbc:mysql://127.0.0.1:3306/lx_java";
    private static final String MYSQL_USER = "root";
    private static final String MYSQL_PASSWORD = "root";

    @Test
    public void c3p0Test() throws SQLException, PropertyVetoException {
        ComboPooledDataSource dataSource = new ComboPooledDataSource();
        dataSource.setDriverClass(MYSQL_DRIVER);
        dataSource.setJdbcUrl(MYSQL_URL);
        dataSource.setUser(MYSQL_USER);
        dataSource.setPassword(MYSQL_PASSWORD);
        Connection conn = dataSource.getConnection();
        System.out.println(conn);
        conn.close();
    }

    @Test
    public void druidTest() throws SQLException {
        DruidDataSource dataSource = new DruidDataSource();
        dataSource.setDriverClassName(MYSQL_DRIVER);
        dataSource.setUrl(MYSQL_URL);
        dataSource.setUsername(MYSQL_USER);
        dataSource.setPassword(MYSQL_PASSWORD);
        DruidPooledConnection conn = dataSource.getConnection();
        System.out.println(conn);
        conn.close();
    }
}
```

### b. 通过Spring创建数据源测试

1. `pom.xml`中导入Spring坐标

```xml
<!-- https://mvnrepository.com/artifact/org.springframework/spring-context -->
<dependency>
	<groupId>org.springframework</groupId>
	<artifactId>spring-context</artifactId>
	<version>5.3.20</version>
</dependency>
```

2. `test/resources`中创建`ApplicationContext.xml`核心配置文件并添加内容

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
        <property name="driverClassName" value="com.mysql.jdbc.Driver"/>
        <property name="url" value="jdbc:mysql://127.0.0.1:3306/lx_java"/>
        <property name="username" value="root"/>
        <property name="password" value="root"/>
    </bean>

</beans>
```

3. `test/java/DataSource.java`文件中添加

```java
@Test
public void druidTest02() throws SQLException {
	ClassPathXmlApplicationContext app = new ClassPathXmlApplicationContext("ApplicationContext.xml");
	DataSource dataSource = (DataSource) app.getBean("dataSource");
	Connection conn = dataSource.getConnection();
	System.out.println(conn);
	conn.close();
}
```

### c. 通过Properties创建数据源测试

1. 在`test/resources`目录中创建 `jdbc.properties`文件并添加配置信息

```properties
MYSQL_DRIVER=com.mysql.jdbc.Driver
MYSQL_URL=jdbc:mysql://127.0.0.1:3306/lx_java
MYSQL_USER=root
MYSQL_PASSWORD=root
```

2. 修改`ApplicationContext.xml`核心配置文件如下，添加`:context`命名空间

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="
       http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       http://www.springframework.org/schema/context/spring-context.xsd">

    <context:property-placeholder location="classpath:jdbc.properties"/>
    <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
        <property name="driverClassName" value="${MYSQL_DRIVER}"/>
        <property name="url" value="${MYSQL_URL}"/>
        <property name="username" value="${MYSQL_USER}"/>
        <property name="password" value="${MYSQL_PASSWORD}"/>
    </bean>

</beans>
```

3. 运行测试


# 五、Spring注解开发

## 1. Spring原始注解

> Spring原始注解主要用于替代`<bean>`标签的配置

注解名 | 说明
:- | :-
@Component | 使用在类上，用来实例化`<bean>`
**@Controller** | 同上 - Web层控制器
**@Service** | 同上 - Service层
**@Repository** | 同上- Dao层
@Autowired | 使用在字段上用于根据类型依赖注入（引用数据类型）
@Qualifier | 集合@Autowried一起使用，用于根据名称进行依赖注入
**@Resource** | 等价于@Autowried + @Qualifier，按照名称进行注入
**@Value** | 注入普通属性
**@Scope** | 标注作用范围
@PostConstruct | 使用在方法上，标注初始化方法
@PreDestory | 使用在方法上，标注销毁方法

1. 准备工作（同上）

![[Pasted image 20230219174202.png]]

2. 使用注解替换配置文件

	1. 替换`<bean id="stuDao" class="com.maqf.dao.impl.StuDaoImpl"/>`：在StuDaoImpl类上面添加注解@Component("stuDao")
	2. 替换`<bean id="stuService" class="com.maqf.service.impl.StuServiceImpl">    <property name="stuDao" ref="stuDao"/></bean>`：在StuServiceImpl类上添加@Component("stuService")，`private StuDao stuDao;`上添加@AutoWired和@Qualifier("stuDao")或直接添加@Resource(name="stuDao")
	3. 在`ApplicationContext.xml`核心配置文件中添加注解组件扫描：`<context:component-scan base-package="com.maqf"/>`
	4. 注入基本数据类型的属性

```java
@Value("Jack")  
private String stuName;
```

		5. 设置Scope

```java
// 在类前添加
@Scope("prototype")
```

		6. 设置初始化和销毁方式

```java
// 在类前添加
@PostConstruct // 初始化方法
// 或者
@PreDestory // 销毁方法
```


## 2. Spring新注解

用来替代XML中其他配置内容如下：
1. 非自定义的`<bean>`
2. 加载`properties`文件的配置`<context:property-placeholder>`
3. 组件扫描配置`<context:component-scan>`
4. 配置文件的引入`<import>`

注解名 | 说明
:- | :-
@Configuration | 用于指定当前类是一个Spring配置类，当创建容器是从该类上加载注解
@ComponentScan | 用于指定Spring在初始化容器是要扫描包
@Bean | 使用在方法上，标注该方法的返回值存储到Spring容器中
@PropertySource | 加载Properties配置文件
@Import | 导入其他配置文件

1. 创建一个“数据源配置类”`com.maqf.config.DataSourceConfiguration.java`

```java
package com.maqf.config;

import com.alibaba.druid.pool.DruidDataSource;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.PropertySource;

import javax.sql.DataSource;

/**
 * @ClassName DataSourceConfiguration
 * @Description: TODO
 * @Author: maqf22@qq.com
 */
@PropertySource("classpath:jdbc.properties")
public class DataSourceConfiguration {
    @Value("${MYSQL_DRIVER}")
    private String driver;
    @Value("${MYSQL_URL}")
    private String url;
    @Value("${MYSQL_USER}")
    private String user;
    @Value("${MYSQL_PASSWORD}")
    private String password;

    @Bean("dataSource")
    public DataSource getDataSource() {
        DruidDataSource dataSource = new DruidDataSource();
        dataSource.setDriverClassName(this.driver);
        dataSource.setUrl(this.url);
        dataSource.setUsername(this.user);
        dataSource.setPassword(this.password);
        return dataSource;
    }
}
```

2. 创建一个“核心配置类“`com.maqf.config.SpringConfiguration.java`

```java
package com.maqf.config;

import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Import;

/**
 * @ClassName SpringConfiguration
 * @Description: TODO
 * @Author: maqf22@qq.com
 */
@Configuration // 标注当前的类为核心配置文件类
@ComponentScan("com.maqf") // 配置注解的扫描路径
@Import({DataSourceConfiguration.class}) // 导入其他的配置文件
public class SpringConfiguration {
}
```

3. 测试

```java
@Test
public void AnnoTest02() throws SQLException {
	// 使用核心类之类的class对象创建容器对象
	ApplicationContext app = new AnnotationConfigApplicationContext(SpringConfiguration.class);
	DataSource dataSource = (DataSource) app.getBean("dataSource");
	Connection conn = dataSource.getConnection();
	System.out.println(conn);
	conn.close();
}
```


# 六、Spring整合Junit

使用原始的Junit在测试过程中，都需要去创建容器对象和通过容器对象获取bean，如下

```java
ClassPathXmlApplicationContext app = new ClassPathXmlApplicationContext("ApplicationContext.xml");  
MyDruidDataSource bean = app.getBean(MyDruidDataSource.class);
```

- 可以通过SpringJunit负责创建Spring容器
- 将需要进行测试的bean直接在测试类中进行注入

1. `pom.xml`中添加SpringJunit坐标

```xml
<!-- https://mvnrepository.com/artifact/org.springframework/spring-test -->
<dependency>
	<groupId>org.springframework</groupId>
	<artifactId>spring-test</artifactId>
	<version>5.3.20</version>
	<scope>test</scope>
</dependency>
```

2. 创建`com.mqf.test.JunitTest.java`并编辑源码

```java
package com.maqf.runTest;

import com.maqf.config.SpringConfiguration;
import com.maqf.dao.StuDao;
import com.maqf.service.StuService;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;

import javax.annotation.Resource;
import javax.sql.DataSource;
import java.sql.SQLException;

/**
 * @ClassName JunitTest
 * @Description: TODO
 * @Author: maqf22@qq.com
 */
// 使用@RunWith注解替换原来的运行
@RunWith(SpringJUnit4ClassRunner.class)
// 使用@ContextConfiguration指定配置文件或配置类
// @ContextConfiguration("classpath:ApplicationContext.xml")
@ContextConfiguration(classes = SpringConfiguration.class)
public class JunitTest {
    @Resource(name = "stuService")
    private StuService stuService;
    @Resource(name = "stuDao")
    private StuDao stuDao;
    @Resource(name = "dataSource")
    private DataSource dataSource;

    @Test
    public void Test01() throws SQLException {
        System.out.println(dataSource.getConnection());
        stuDao.show();
        stuService.show();
    }
}
```


