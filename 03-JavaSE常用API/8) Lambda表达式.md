
# 一、概述

## 1. 函数式编程思想

- 面向对象思想在乎的是，一切的动作通过对象的形式去完成
- 而函数式编程思想，更在乎做什么，而不是通过什么形式去做

## 2. 案例

```java
package com.maqf.demo01;

public class Demo01 {
    public static void main(String[] args) {
        new Thread(new Runnable() {
            @Override
            public void run() {
                System.out.println("匿名内部类实现的线程");
            }
        }).start();

        new Thread(() -> {
            System.out.println("Lambda实现的线程");
        }).start();
    }
}
```

## 3. Lambda基本格式

- ()：形参列表
- ->：要做的事情
- {}：方法体

## 4. 案例

- Lambda表达式使用的前提
	- 有一个接口
	- 并且接口中有且仅有一个抽象方法

```java
package com.maqf.demo02;

public interface Eatable {
    public void eat(String food);
}
```

```java
package com.maqf.demo02;

public class EatableImpl implements Eatable {
    @Override
    public void eat(String food) {
        System.out.println("one eat " + food + '.');
    }
}
```

```java
package com.maqf.demo02;

public class EatableTest {
    public static void main(String[] args) {
        // 实现方式一（实现类）
        runEat(new EatableImpl());

        // 实现方式二（匿名内部类）
        runEat(new Eatable() {
            @Override
            public void eat(String food) {
                System.out.println("two eat " + food + '.');
            }
        });

        // 实现方式三（Lambda）
        runEat((String food) -> {
            System.out.println("three eat " + food + '.');
        });

        // 实现方式四
        Eatable e = (String food) -> {
            System.out.println("four eat " + food + '.');
        };
        runEat(e);

        // 必须得有一个接口
        // 接口中有且仅有一个抽象方法
        // 使用Lambda的时候必须要有上下文
    }

    public static void runEat(Eatable e) {
        e.eat("duck");
    }
}
```

## 5. Lambda省略模式

```java
package com.maqf.demo03;

public interface Eatable {
    public void eat();
}
```

```java
package com.maqf.demo03;

public interface Computable {
    public Integer compute(Integer a, Integer b);
}
```

```java
package com.maqf.demo03;

import com.maqf.demo02.Eatable;

public class Main {
    public static void main(String[] args) {
        // 匿名内部类
        runEat(new Eatable() {
            @Override
            public void eat(String food) {
                System.out.println("Jack eat " + food);
            }
        });
        runCompute(new Computable() {
            @Override
            public Integer compute(Integer a, Integer b) {
                return a + b;
            }
        });

        // Lambda
        runEat((String food) -> {
            System.out.println("Rose eat " + food);
        });
        runCompute((Integer a, Integer b) -> {
            return a - b;
        });

        // Lambda省略
        runEat(food -> System.out.println("Lily eat " + food));
        runCompute((a, b) -> a * b);
    }

    public static void runEat(Eatable e) {
        e.eat("rice");
    }

    public static void runCompute(Computable c) {
        Integer res = c.compute(5, 6);
        System.out.println(res);
    }
}
```

## 6. Lambda使用注意事项

- 要实现的接口中保证有且仅有一个抽象方法（叫做函数式接口）
- 必须有类型指向，才能确定Lambda对应的接口（传参 或 多态）

## 7. Lambda与匿名内部类

- 内部类-万能，接口、抽象类、普通类均可
- Lambda-接口，只能针对接口，其他类则不可以，而且接口中要求有且仅有一个抽象方法
- 匿名内部类在编译运行的时候会生成可见的字节码文件.class，Lambda则不会

# 二、Lambda支持的方法引用

## 1. 常见的引用方式

- 引用类方法：引用类的静态方法；`类名::静态方法名`
- 引用对象的实例方法：引用对象中的成员方法：`对象::成员方法名`
- 引用类的实例方法：引用类中的成员方法：`类名::方法名`
- 引用构造方法：引用构造方法：`类名::new`

## 2. 案例

### 引用类中的静态方法

```java
package com.maqf.demo04;

public class ConvertMain {
    public static void main(String[] args) {
        // 匿名内类部方式
        runConvert(new Converter() {
            @Override
            public Integer convert(String str) {
                return Integer.parseInt(str);
            }
        });

        // Lambda方式
        runConvert((String str) -> {
            return Integer.parseInt(str);
        });

        // Lambda省略方式
        runConvert(str -> Integer.parseInt(str));

        // 引用类中静态方法
        runConvert(Integer::parseInt);
    }

    public static void runConvert(Converter c) {
        Integer res = c.convert("29");
        System.out.println(res + 1);
    }
}
```

### 引用对象的实现方法

```java
package com.maqf.demo05;

public class PrintUpper {
    public void toUpper(String str) {
        System.out.println("对象方法: " + str.toUpperCase());
    }
}
```

```java
package com.maqf.demo05;

public interface Printer {
    public void toUpper(String str);
}
```

```java
package com.maqf.demo05;

public class PrinterMain {
    public static void main(String[] args) {
        // 匿名内部类
        runPrint(new Printer() {
            @Override
            public void toUpper(String str) {
                System.out.println(str.toUpperCase());
            }
        });

        // 完整Lambda
        runPrint((String str) -> {
            System.out.println(str.toUpperCase());
        });

        // 省略Lambda
        runPrint(str -> System.out.println(str.toUpperCase()));

        // 引用对象的实例方法
        runPrint(new PrintUpper()::toUpper);
    }

    public static void runPrint(Printer p) {
        p.toUpper("Hello JavaSE");
    }
}
```

### 引用类的实例方法

```java
package com.maqf.demo06;

public interface MyString {
	// 引用类的实例方法，首个参数必须是类的实例
    public String mySubstring(String str, int start, int end);
}
```

```java
package com.maqf.demo06;

public class MyStringMain {
    public static void main(String[] args) {
        // 匿名内部类
        runMyString(new MyString() {
            @Override
            public String mySubstring(String str, int start, int end) {
                return str.substring(start, end);
            }
        });

        // 完整Lambda
        runMyString((String str, int start, int end) -> {
            return str.substring(start, end);
        });

        // 省略Lambda
        runMyString((str, start, end) -> str.substring(start, end));

        // 引用类的实例方法（引用类的实例方法时，接口第一个参数要是类的实例）
        runMyString(String::substring);

    }

    private static void runMyString(MyString m) {
        String subStr = m.mySubstring("Hello JavaSE Lambda", 6, 12);
        System.out.println(subStr);
    }
}
```

### 引用构造器

```java
package com.maqf.demo07;

public class Student {
    private String name;
    private int age;

    public Student() {
    }

    public Student(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }
}
```

```java
package com.maqf.demo07;

public interface StudentBuilder {
    public Student build(String name, int age);
}
```

```java
package com.maqf.demo07;

public class Main {
    public static void main(String[] args) {
        // 匿名内部类
        runBuild(new StudentBuilder() {
            @Override
            public Student build(String name, int age) {
                return new Student(name, age);
            }
        });

        // 完整Lambda
        runBuild((String name, int age) -> {
            return new Student(name, age);
        });

        // 省略Lambda
        runBuild((name, age) -> new Student(name, age));

        // 引用构造方法作为Lambda表达式（它的所有形参将作为实参进行传递【隐式的】）
        runBuild(Student::new);
    }

    private static void runBuild(StudentBuilder sb) {
        Student stu = sb.build("Rose", 19);
        System.out.printf("%s, %d\n", stu.getName(), stu.getAge());
    }
}
```


# 三、函数式接口

## 1. 函数式接口概述

有且只有一个抽象方法的接口，专为Lambda而生

> @FunctionalInterface

## 2. 函数式接口作为方法的参数

> 创建线程就是典型的函数式接口

```java
package com.maqf.demo08;

public class Demo01 {
    public static void main(String[] args) {
        startThread(new Runnable() {
            @Override
            public void run() {
                System.out.println("thread running");
            }
        });

        startThread(() -> System.out.println("thread running"));
    }

    private static void startThread(Runnable r) {
        Thread thread = new Thread(r);
        thread.start();
    }
}
```

```java
@FunctionalInterface
public interface Runnable {
    /**
     * When an object implementing interface <code>Runnable</code> is used
     * to create a thread, starting the thread causes the object's
     * <code>run</code> method to be called in that separately executing
     * thread.
     * <p>
     * The general contract of the method <code>run</code> is that it may
     * take any action whatsoever.
     *
     * @see     java.lang.Thread#run()
     */
    public abstract void run();
}
```


## 3. 函数式接口作为方法的返回值

```java
package com.maqf.demo08;

import java.util.ArrayList;
import java.util.Collections;
import java.util.Comparator;

public class Demo02 {
    public static void main(String[] args) {
        ArrayList<String> list = new ArrayList<>();
        list.add("123656");
        list.add("1238978");
        list.add("123543");
        list.add("6898");
        Collections.sort(list, comparator());
        System.out.println(list);
    }

    private static Comparator<String> comparator() {
        /*return new Comparator<String>() {
            @Override
            public int compare(String o1, String o2) {
                return o1.length() - o2.length();
            }
        };*/
        return (o1, o2) -> o1.length() - o2.length();
        // return Comparator.comparingInt(String::length);
    }
}
```


