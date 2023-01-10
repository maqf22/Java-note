
```java
public class demo01 {
    public static void main(String[] args) {
        for (int i = 1; i <= 10; i++) {
            System.out.println(i);
        }
        
        int i = 1;
        for (;;) {
            if (!(i <= 10)) break;
            System.out.println(i);
            i++;
        }
    }
}
```

```java
public class demo02 {
    public static void main(String[] args) {
        for (int i = 1; i <= 10; i++) {
            if (i % 2 != 0) continue;
            System.out.println(i);
        }

        for (int i = 1; i <= 10; i++) {
            if (i % 2 != 0) break;
            System.out.println(i);
        }
    }
}
```

