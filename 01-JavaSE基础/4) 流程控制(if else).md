
# 一、if基本语法

```java
public class demo01 {
    public static void main(String[] args) {
        if (true) {
            System.out.println("true");
        }
        // if语句只一条的时候可以不用大括号
        if (false)
            System.out.println("false");
        System.out.println("end");

        int age = 29;
        if (age > 18) {
            System.out.println("over 18");
        } else {
            System.out.println("lower 18");
        }
    }
}
```

> 退出Java虚拟机

```java
System.exit(0);
```

# 二、if else-if

```java
import java.util.Scanner;

public class demo02 {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        int score = sc.nextInt();
        if (score > 100 || score < 0) {
            System.out.println("成绩非法");
            System.exit(0);
        }
        if (score >= 86) {
            System.out.println("A");
        } else if(score >= 70) {
            System.out.println("B");
        } else if(score >= 60) {
            System.out.println("C");
        } else {
            System.out.println("D");
        }
    }
}
```

# 三、if嵌套

```java
public class demo03 {
	if () {
		if () {}
	}
}
```