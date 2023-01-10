
# 一、switch入口限制

switch后面括号里面的内容可以使用：
- 整型（byte / short / int / long）
- 字符型（char）
- 字符串（String）
- 枚举（enum）

```java
import java.util.Scanner;

public class demo01 {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        int num = sc.nextInt();
        switch (num) {
            case 1:
                System.out.println("1");
                break;
            case 2:
                System.out.println("2");
                break;
            default:
                System.out.println("default");
        }
    }
}
```

> 判断语句中有常量推荐写在前面

```java
if (0 == num) {}
```

