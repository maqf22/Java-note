
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

## 4. 常用的函数式接口

### Supplier

> 是一个生产者接口，用来生产数据，通常返回的是一个数据（可以使用Lambda表达式）

```java
package com.maqf.demo09;

import java.util.function.Supplier;

public class Demo01 {
    public static void main(String[] args) {
        /*
        String str = getString(new Supplier<String>() {
            @Override
            public String get() {
                return "Hello JavaSE Lambda".substring(6, 12);
            }
        });
        */
        String str = getString(() -> "Hello JavaSE Lambda".substring(6, 12));
        System.out.println(str);
    }

    private static String getString(Supplier<String> sup) {
        return sup.get();
    }
}
```

### Consumer

> 是一个消费者接口，针对于数据做一些提取操作，不需要返回值

```java
package com.maqf.demo09;

import java.util.Comparator;
import java.util.function.Consumer;

public class Demo02 {
    public static void main(String[] args) {
        operatorString("Jack", new Consumer<String>() {
            @Override
            public void accept(String name) {
                System.out.println(name);
            }
        });
        operatorString("Rose", name -> System.out.println(name));
        operatorString("Lily", System.out::println);

        operatorString("Victorian", System.out::println, name -> System.out.println(new StringBuilder(name).reverse()));
    }

    private static void operatorString(String name, Consumer<String> con) {
        con.accept(name);
    }

    private static void operatorString(String name, Consumer<String> con1, Consumer<String> con2) {
        // con1.accept(name);
        // con2.accept(name);
        // 等价于上两行
        con1.andThen(con2).accept(name);
    }
}
```

Consumer案例

```java
package com.maqf.demo09;

import java.util.function.Consumer;

public class Demo03 {
    public static void main(String[] args) {
        String[] infoArr = {"东邪：黄固", "西毒：欧阳峰", "南帝：段智兴", "北丐：洪七公"};
        operateStringArr(
                infoArr,
                str -> {
                    String nickName = str.split("：")[0];
                    System.out.println("外号：" + nickName);
                },
                str -> {
                    String name = str.split("：")[1];
                    System.out.println("真名：" + name);
                });
    }

    private static void operateStringArr(String[] arr, Consumer<String> con1, Consumer<String> con2) {
        for (String str : arr) {
            con1.andThen(con2).accept(str);
        }
    }
}
```

### Predicate

> 通常用于判断参数是否满足指定的条件

```java
package com.maqf.demo09;

import java.util.function.Predicate;

public class Demo04 {
    public static void main(String[] args) {
        String str = "Hello JavaSE Lambda";
        boolean res = checkString(str, s -> s.length() < 5);
        System.out.println(res);

        boolean r = checkString(str, s -> s.length() > 5, s -> s.length() < 20);
        System.out.println(r);
    }

    private static boolean checkString(String str, Predicate<String> pre) {
        // return pre.test(str);
        // return !pre.test(str);
        // 逻辑非同上
        return pre.negate().test(str);
    }

    private static boolean checkString(String str, Predicate<String> pre1, Predicate<String> pre2) {
        // return pre1.test(str) && pre2.test(str);
        return pre1.and(pre2).test(str); // 同上
    }
}
```

Predicate案例

```java
package com.maqf.demo09;

import java.util.ArrayList;
import java.util.function.Predicate;

public class Demo05 {
    public static void main(String[] args) {
        String[] infoArr = {"Rose,29", "Jack,30", "Lily,19", "Ben,23", "Kitty,25"};
        ArrayList<String> filterRes = myFilter(infoArr, s -> Integer.parseInt(s.split(",")[1]) > 25, s -> s.split(",")[0].length() > 3);
        System.out.println(filterRes);
    }

    private static ArrayList<String> myFilter(String[] arr, Predicate<String> pre1, Predicate<String> pre2) {
        ArrayList<String> res = new ArrayList<>();
        for (String str : arr) {
            if (pre1.and(pre2).test(str)) {
                res.add(str);
            }
        }
        return res;
    }
}
```

### Function

> 接收一个参数，并产生一个结果
> Function<T, R>：接口通常用于对参数进行处理，转换（处理逻辑有Lambda实现），然后返回一个新的值

```java
package com.maqf.demo09;

import java.util.function.Function;

public class Demo06 {
    public static void main(String[] args) {
        convert("9527", s -> Integer.parseInt(s) + 1);
        convert("29", Integer::parseInt, i -> String.valueOf(i - 1));
    }

    private static void convert(String str, Function<String, Integer> func) {
        Integer i = func.apply(str);
        System.out.println(i);
    }

    private static void convert(String str, Function<String, Integer> func1, Function<Integer, String> func2) {
        String res = func1.andThen(func2).apply(str);
        System.out.println(res);
    }
}
```


# 四、Stream流

*了解Stream流*

```java
package com.maqf.demo10;

import java.util.ArrayList;

public class Demo01 {
    public static void main(String[] args) {
        ArrayList<String> list = new ArrayList<>();
        initList(list);

        ArrayList<String> threeList = new ArrayList<>();
        for (String s : list) {
            if (s.length() >= 3)
                threeList.add(s);
        }
        System.out.println(threeList);

        ArrayList<String> guoList = new ArrayList<>();
        for (String s : list) {
            if (s.startsWith("郭"))
                guoList.add(s);
        }
        System.out.println(guoList);

        ArrayList<String> threeGuoList = new ArrayList<>();
        for (String s : guoList) {
            if (s.length() >= 3) {
                threeGuoList.add(s);
            }
        }
        System.out.println(threeGuoList);

        // Stream流的写法
        list.stream().filter(s -> s.length() >= 3).filter(s -> s.startsWith("郭")).forEach(System.out::println);
    }

    private static void initList(ArrayList<String> list) {
        list.add("郭靖");
        list.add("郭嘉");
        list.add("郭德纲");
        list.add("郭汾阳");
        list.add("杨过");
        list.add("小龙女");
    }
}
```

## 1. 流的概述

- 生成流：通过数据源（数组或者集合）生成流，list.stream()
- 中间流：一个流后面可以跟随任意个中间操作，其目的是打开流，在过程中做过滤/映射，然后返回一个新流交给下一个操作使用，filter()
- 结束流：一个流只能有一个终结操作，当这个操作执行后，流就被使用完了。forEach()

> Stream才是真正的把函数式编带给了Java


*对象的连续操作*

```java
package com.maqf.demo11;

public class MyStringStream {
    private String str = "";

    public String getStr() {
        return str;
    }

    public MyStringStream append(String str) {
        this.str += str + " ";
        return this;
    }
    public void printStr() {
        System.out.println(this.str);
    }
}
```

## 2. Stream流的生成方式

```java
package com.maqf.demo10;

import java.util.ArrayList;
import java.util.HashMap;
import java.util.HashSet;
import java.util.Map;
import java.util.stream.Stream;

public class Demo02 {
    public static void main(String[] args) {
        // Collection
        Stream<String> listStream = new ArrayList<String>().stream();
        Stream<String> setStream = new HashSet<String>().stream();
        // Map
        HashMap<String, String> map = new HashMap<>();
        Stream<String> keysStream = map.keySet().stream();
        Stream<String> valuesStream = map.values().stream();
        Stream<Map.Entry<String, String>> entrySetStream = map.entrySet().stream();
        // Array
        String[] strArr = {};
        Stream<String> strArrStream = Stream.of(strArr);
        Stream<Integer> intStream = Stream.of(1, 2, 3);
        Stream<String> strStream = Stream.of("a", "b", "c");
        Stream<Double> doubleStream = Stream.of(3.14);
        Stream<Float> floatStream = Stream.of(3.14f);
    }
}
```

## 3. Stream中间流的操作方法

```java
package com.maqf.demo10;

import java.util.ArrayList;
import java.util.Comparator;
import java.util.stream.Stream;

public class Demo03 {
    public static void main(String[] args) {
        ArrayList<String> list = new ArrayList<>();
        initList(list);

        // filter
        list.stream().filter(s -> s.startsWith("郭")).forEach(System.out::println);

        // skip limit
        list.stream().skip(1).limit(3).forEach(System.out::println);

        Stream<String> s1 = list.stream().skip(2).limit(3);
        Stream<String> s2 = list.stream().skip(1).limit(2);

        // Stream.concat
        // Stream<String> c1 = Stream.concat(s1, s2); // 包含重复的值
        Stream<String> c2 = Stream.concat(s1, s2).distinct(); // 不包含重复的值
        c2.forEach(System.out::println);

        // sorted
        list.stream().sorted().forEach(System.out::println); // 自然排序
        list.stream().sorted(Comparator.comparingInt(String::length)).forEach(System.out::println); // 自定义排序
    }

    private static void initList(ArrayList<String> list) {
        list.add("郭靖");
        list.add("郭嘉");
        list.add("郭德纲");
        list.add("郭汾阳");
        list.add("杨过");
        list.add("小龙女");
    }
}
```

## 4. Stream终结流的方式

```java
package com.maqf.demo10;

import java.util.ArrayList;
import java.util.Comparator;
import java.util.OptionalDouble;

public class Demo04 {
    public static void main(String[] args) {
        ArrayList<String> strList = new ArrayList<>();
        strList.add("23456789");
        strList.add("56789");
        strList.add("123456789");
        strList.add("456789");
        strList.add("3456789");

        // map forEach
        strList.stream().map(Integer::parseInt).sorted((Comparator.comparingInt(o -> o))).forEach(System.out::println);

        // mapToInt sum
        int sum = strList.stream().mapToInt(Integer::parseInt).sum();
        System.out.println(sum);
        // average getAsDouble
        OptionalDouble average = strList.stream().mapToInt(Integer::parseInt).average();
        double asDouble = average.getAsDouble();
        System.out.println(asDouble);
        // count
        long count = strList.stream().filter(s -> s.length() > 5).count();
        System.out.println(count);
        // max getAsInt
        int max = strList.stream().mapToInt(Integer::parseInt).max().getAsInt();
        System.out.println(max);
        // min
        int min = strList.stream().mapToInt(Integer::parseInt).min().getAsInt();
        System.out.println(min);
    }
}
```

## 5. 翻页案例

```java
package com.maqf.demo10;

import java.util.ArrayList;
import java.util.Scanner;

public class Demo05 {
    public static void main(String[] args) {
        ArrayList<String> list = new ArrayList<>();
        initList(list);

        final int PAGE_SIZE = 5; // 每页大小
        final int MAX_PAGE = list.size() / PAGE_SIZE + (list.size() % PAGE_SIZE != 0 ? 1 : 0); // 最大页码

        int currentPage = 1; // 当前页码
        while (true) {
            list.stream().skip((currentPage - 1) * PAGE_SIZE).limit(PAGE_SIZE).forEach(System.out::println);
            System.out.println("----------------------- Next(n) Back(b)");
            switch (new Scanner(System.in).nextLine()) {
                case "N":
                case "n":
                    currentPage = Math.min(currentPage + 1, MAX_PAGE);
                    break;
                case "B":
                case "b":
                    currentPage = Math.max(currentPage - 1, 1);
                    break;
                default:
                    System.err.println("please type N or B");
            }
        }
    }

    private static void initList(ArrayList<String> list) {
        list.add("郭靖");
        list.add("郭嘉");
        list.add("郭德纲");
        list.add("郭汾阳");
        list.add("杨过");
        list.add("小龙女");
        list.add("洪七公");
        list.add("欧阳峰");
        list.add("王重阳");
        list.add("段智兴");
        list.add("黄固");
        list.add("齐天大圣");
    }
}
```