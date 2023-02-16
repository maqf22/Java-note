
# 一、测试简介

> 黑盒：不需要写代码，给输入值，看程序是否能够输出期望值。
> 白盒：需要写代码的。关注程序具体的执行流程。


# 二、Junit使用

## 1. 操作步骤

1. 定义一个测试类（测试用的类）
	- 规则
		- 测试类名：被测试的类名Test - 如：Demo01Test
		- 包名：公司域名.test
2. 定义测试方法
	- 规则
		- 方法名：test测试方法名
		- 返回值：void
		- 形参列表：无参
3. 给方法加@Test
4. 导入Junit依赖环境

```java
package com.maqf.test;

import org.junit.Test;

public class Compute {
    public int sum(int a, int b) {
        return a + b;
    }
    @Test
    public void print() {
        System.out.println(sum(2, 3));
    }
}
```

## 2. 判断结果

- 绿为成功
- 红为失败

## 3. 断言

> 断言通常用来处理预期可能出现的结果

```java
Assert.assertEquals(期望的结果，运算的结果);
```

```java
package com.maqf.test;

import org.junit.Assert;
import org.junit.Test;

public class Compute {
    public int sum(int a, int b) {
        return a + b + 1;
    }
    @Test
    public void print() {
        int res = sum(2, 3);
        // 断言应该得到什么结果，真实得到了什么结果，进行对比
        Assert.assertEquals(5, res);
        System.out.println(res);
    }
}
```

> @Before - 修饰的方法会在测试方法之前被自动执行
> @After - 修饰的方法会在测试方法执行之后被自动执行

```java
package com.maqf.test;

import org.junit.After;
import org.junit.Assert;
import org.junit.Before;
import org.junit.Test;

public class Compute {
    @Before
    public void init() {
        System.out.println("info init complete!");
    }
    @After
    public void close() {
        System.out.println("info close!");
    }
    public int sum(int a, int b) {
        return a + b;
    }
    @Test
    public void print() {
        int res = sum(2, 3);
        // 断言应该得到什么结果，真实得到了什么结果，进行对比
        Assert.assertEquals(5, res);
        System.out.println(res);
    }
}
```


# 三、注解

## 1. 简介

> 为程序添加的说明，给计算机看的。JDK1.5之后出现的新特性

- 作用分类：
	1. 编写文档：通过代码里标识的注解生成文档【生成文档doc文档】
	2. 代码分析：通过代码里标识的注解对代码进行分析【使用反射】
	3. 编译检查：通过代码里标识的注解让编译器能够实现基本的编译检查【Override】

## 2. JDK常用注解

> JDK自带的预定义的注解

- `@FunctionalInterface`：检测被标注的结构是函数式接口
- `@Override`：检测被标注的方法是否是继承自父类（接口）
- `@Deprecated`：被标注的内容已过时
- `@SupperssWarnings("all")`：压制警告

```java
package com.maqf.annotation;

public class AnnotationTest01 {
    @Deprecated
    public void sayHello() {
        System.out.println("say hello");
    }
    public void hello() {
        System.out.println("hello");
    }
    @SuppressWarnings("all")
    public void method() {}
}
```

```java
package com.maqf.annotation;

public class AnnotationTest {
    public static void main(String[] args) {
        AnnotationTest01 an = new AnnotationTest01();
        an.sayHello();
        an.hello();
    }
}
```

## 3. 自定义注解

```java
元注解
public @interface 注解名称 {
	属性列表;
}
```

- 属性实际上就是接口中的抽象方法
	- 注意
		1. 属性的返回值只能是以下内容
			- 基本数据类型
			- String
			- 枚举类型
			- 注解类型
			- 以上类型的数组类型
		2. 主要定义了属性，在使用注解的时候就必须给于赋值
			- 如果定义属性时，使用default关键字给属性默认初始化值，则使用注解时，可不进行属性的赋值
			- 如果只有一个属性需要赋值，并且属性的名称是value，则value可以省略，直接定义值即可
			- 数组赋值时，值使用{}包裹，如果数组中只有一个值，则{}可省略

## 4. 元注解

> 用于描述注解的注解

- @Target：描述注解能够作用的位置
	- ElementType取值
		- TYPE：可以作用于类上
		- METHOD：可以作用于方法上
		- FIELD：可以作用于成员变量上
- @Retention：描述注解被保留的阶段
	- @Retention(RetentionPolicy.RUNTIME)：当前被描述的注解，会保留到class字节码文件中，并被JVM读取到
- @Documented：描述注解是否被抽取到api文档中
- Inherite：描述注解是否被子类继承

## 5. 解析注解

> 获取注解中定义的属性值

```java
package com.maqf.annotation;

import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
public @interface AnnoTest {
    String name();
    String psd();
    // String value(); //设置为value可以直接传
    // String[] info();
}
```

```java
package com.maqf.annotation;

@AnnoTest(name = "root", psd = "123")
public class AnnotationTest {
    public static void main(String[] args) {
        Class<AnnotationTest> atc = AnnotationTest.class;
        AnnoTest at = atc.getAnnotation(AnnoTest.class);
        String name = at.name();
        String psd = at.psd();
        System.out.println(name);
        System.out.println(psd);
    }
}
```

















