
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

```



