
# 一、IO流的分类

IO流概述

IO：输入/输出
流：是一种抽象的概念，是对数据传输的总称，就是说数据在设备间的传输成为流，流的本质是数据的传输
IO流就是用来处理设备间数据传输问题的常见的应用：文件的复制、上传、下载等 

- 根据数据的流向
	- 输入流：读数据
	- 输出流：写数据
- 根据数据类型
	- 字节流（万能流）：（读不懂的用字节流）
		- 字节输入流
		- 字节输出流
	- 字符流：
		- 字符输入流
		- 字符输出流


# 二、字节流写数据

- 字节流抽象基类
	- InputStream：字节输入流
	- OutputStream：字节输出流
	- 子类名称特点：子类名称都是以父类名称作为子类名称的后缀

```java
package com.maqf.demo01;

import java.io.FileOutputStream;
import java.io.IOException;

public class Demo01 {
    public static void main(String[] args) throws IOException {
        // 创建一个输出流的对象
        // 如果目标文件不存在会创建一个目标文件
        FileOutputStream fos = new FileOutputStream("./myDemoTest.txt", true);
        // 通过输出流的对象写入数据
        fos.write(65); // 写一个字符
        byte[] bytes = "Hello Java".getBytes();
        fos.write(bytes); // 写一个完整的字符串对应的字节数组
        fos.write(bytes, 0, 5);
        fos.close();
    }
}
```


# 三、字节流读数据

- FileInputStream：从文件系统中的文件获取输入字节
	- FileInputStream(String name)：通过打开与实际文件的连接来创建一个FileInputStream，该文件由文件系统中路径名name命名
- 使用字节输入流读取数据的具体步骤
	1. 创建字节流输入对象
	2. 调用字节流对象的读取方法
	3. 释放资源

```java
package com.maqf.demo01;

import java.io.FileInputStream;
import java.io.IOException;

public class Demo02 {
    public static void main(String[] args) throws IOException {
        FileInputStream fis = new FileInputStream("./myDemoTest.txt");
        System.out.println(fis.read()); // 读一个
        int by = 0;
        while((by = fis.read()) != -1) {
            System.out.print((char)by);
        }
        fis.close();
    }
}
```

## 字节流复制文件

```java
package com.maqf.demo01;

import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.io.IOException;

public class Demo03 {
    public static void main(String[] args) throws IOException {
        FileInputStream fis = new FileInputStream("./myDemoTest.txt");
        FileOutputStream fos = new FileOutputStream("./myDemoTestNew.txt");

        int by = 0;
        while((by = fis.read()) != -1) {
            fos.write(by);
        }

        fis.close();
        fos.close();
    }
}
```

## 字节流一次读一个数组

```java
package com.maqf.demo01;

import java.io.FileInputStream;
import java.io.IOException;

public class Demo04 {
    public static void main(String[] args) throws IOException {
        FileInputStream fis = new FileInputStream("./myDemoTest.txt");

        byte[] bytes = new byte[1024];
        int len = 0;
        // fis.read(bytes); // 输入的是byte[] 会把读到的结果存到byte[]中 此时返回值是读取到的长度
        while((len = fis.read(bytes)) != -1) {
            System.out.print(new String(bytes, 0, len));
        }

        fis.close();
    }
}
```

## 复制文件

```java
package com.maqf.demo01;

import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.io.IOException;

public class Demo05 {
    public static void main(String[] args) throws IOException {
        FileInputStream fis = new FileInputStream("./myDemoTest.txt");
        FileOutputStream fos = new FileOutputStream("myDemoTestNew.txt");

        // 每次读取1k
        byte[] bytes = new byte[1024];
        int len = 0;
        while((len = fis.read(bytes)) != -1)
            fos.write(bytes, 0, len);

        fis.close();
        fos.close();
    }
}
```

# 四、字节输入输出流缓冲区

- BufferedOutputStream：该类实现缓冲输出流，通过设置这样的输出流，应用程序可以向底层输出流写入字节流，而不必为写入每个字节而导致底层系统的调用
- BufferedInputStream：创建BufferedInputStream将创建一个内部缓冲区数组，当从流中读取或跳过字节时，内部缓冲区将概据需要从所包含的输入流中重新填充，一次多个字节

## BufferedInputStream 读

```java
package com.maqf.demo01;

import java.io.BufferedInputStream;
import java.io.FileInputStream;
import java.io.IOException;

public class Demo06 {
    public static void main(String[] args) throws IOException {
        FileInputStream fis = new FileInputStream("./myDemoTest.txt");
        BufferedInputStream bis = new BufferedInputStream(fis);

        byte[] bytes = new byte[1024];
        int len = 0;
        while((len = bis.read(bytes)) != -1)
            System.out.print(new String(bytes, 0, len));

        bis.close();
    }
}

```

## BufferedOutputStream 写

```java
package com.maqf.demo01;

import java.io.BufferedOutputStream;
import java.io.FileOutputStream;
import java.io.IOException;

public class Demo07 {
    public static void main(String[] args) throws IOException {
        FileOutputStream fos = new FileOutputStream("./myDemoTest.txt");
        BufferedOutputStream bos = new BufferedOutputStream(fos);

        for (int i = 0; i < 50; i++) {
            bos.write("Hello Java Javascript Golang Php .Net C# Dart.\r\n".getBytes());
        }

        bos.close();
    }
}
```
