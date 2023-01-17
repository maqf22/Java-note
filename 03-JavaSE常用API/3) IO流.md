
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


## 1. 字节流读数据

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

## 2. 字节流复制文件

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

## 3. 字节流一次读一个数组

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

## 4. 复制文件

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

# 三、字节输入输出流缓冲区

- BufferedOutputStream：该类实现缓冲输出流，通过设置这样的输出流，应用程序可以向底层输出流写入字节流，而不必为写入每个字节而导致底层系统的调用
- BufferedInputStream：创建BufferedInputStream将创建一个内部缓冲区数组，当从流中读取或跳过字节时，内部缓冲区将概据需要从所包含的输入流中重新填充，一次多个字节

## 1. BufferedInputStream 读

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

## 2. BufferedOutputStream 写

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

# 四、字符流

## 1. 字符串的编码与解码

- 编码
	- getBytes(); 使用平台（IDEA）默认字符集
	- getBytes(String charsetName); 设置字符集
- 解码
	- String(byte\[\] bytes);
	- String(byte\[\] bytes, String charsetName);

```java
package com.maqf.demo02;

import java.io.UnsupportedEncodingException;
import java.util.Arrays;

public class Demo01 {
    public static void main(String[] args) throws UnsupportedEncodingException {
        String str = "你是谁？";
        byte[] bytes = str.getBytes("GBK");
        System.out.println(Arrays.toString(bytes));
        System.out.println(new String(bytes, "GBK"));
    }
}
```

## 2. 字符流写入文件

- InputStreamReader: 字符输入流
- OutputStreamWriter: 字符输出流

```java
package com.maqf.demo02;

import java.io.*;

public class Demo02 {
    public static void main(String[] args) throws IOException {
        // 字符流输出（字符流是基于字节流实现的）
        FileOutputStream fos = new FileOutputStream("./myDemoTest.txt");
        OutputStreamWriter osw = new OutputStreamWriter(fos, "UTF-8");
        /*
        写入一个字符
        osw.write(97);
        osw.flush();
        */
        /*
        写入一个数组
        char[] charArr = {'h', 'e', 'l', 'l', 'o'};
        osw.write(charArr);
        ow.flush();
        */
        // 写一个字符串
        osw.write("早上好~");
        osw.flush(); // 手动刷新流
        osw.close(); // 字符输出流关闭时会自动刷新流

        // 字符流输入
        FileInputStream fis = new FileInputStream("./myDemoTest.txt");
        InputStreamReader isr = new InputStreamReader(fis, "UTF-8");
        /*
        一次读一个
        int by = 0;
        while ((by = isr.read()) != -1)
            System.out.print((char) by);
        */
        // 一次读一个数组
        char[] chars = new char[1024];
        int len;
        while((len = isr.read(chars)) != -1)
            System.out.println(new String(chars, 0, len));
        isr.close();
    }
}
```

## 3. 字符流文本文件复制

```java
package com.maqf.demo02;

import java.io.*;

public class Demo03 {
    public static void main(String[] args) throws IOException {
        final String SRC = "./myDemoTest.txt";
        final String TO = "./myDemoTestNew.txt";
        InputStreamReader isr = new InputStreamReader(new FileInputStream(SRC));
        OutputStreamWriter osw = new OutputStreamWriter(new FileOutputStream(TO));
        /*
        一次读一个字符
        int by;
        while((by = isr.read()) != -1)
            osw.write(by);
            osw.flush();
        */
        // 一次读一个数组
        char[] chars = new char[1024];
        int len = 0;
        while ((len = isr.read(chars)) != -1)
            osw.write(chars, 0, len);

        isr.close();
        osw.close();
    }
}
```

## 4. FileReader & FileWriter

```java
package com.maqf.demo02;

import java.io.FileReader;
import java.io.FileWriter;
import java.io.IOException;

public class Demo04 {
    public static void main(String[] args) throws IOException {
        FileReader fr = new FileReader("./myDemoTest.txt");
        FileWriter fw = new FileWriter("./myDemoTestNew.txt");

        // 一次读取一个数组
        char[] chars = new char[1024];
        int len;
        while((len = fr.read(chars)) != -1)
            fw.write(chars, 0, len);

        fr.close();
        fw.close();
    }
}
```

## 5. 缓冲区

### 1. BufferedReader & BufferedWriter

```java
package com.maqf.demo02;

import java.io.*;

public class Demo05 {
    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new FileReader("./myDemoTest.txt"));
        BufferedWriter bw = new BufferedWriter(new FileWriter("./myDemoTestNew.txt"));

        char[] chars = new char[1024];
        int len;
        while((len = br.read(chars)) != -1)
            bw.write(chars, 0, len);

        br.close();
        bw.close();
    }
}
```

### 2. 特有方法

- BufferedWriter类 - void newLine(); 行分隔符，根据不同系统写入分隔符
- BufferedReader类 - String readLine(); 读一行文字，不包括换行符，读完返回null

```java
package com.maqf.demo02;

import java.io.*;

public class Demo06 {
    public static void main(String[] args) throws IOException {
        final String SRC = "./myDemoTest.txt";
        final String TO = "./myDemoTestNew.txt";

        BufferedReader br = new BufferedReader(new FileReader(SRC));
        BufferedWriter bw = new BufferedWriter(new FileWriter(TO));

        // 一次读取一行
        String line;
        while((line = br.readLine()) != null) {
            bw.write(line);
            bw.newLine(); // 自动跟据系统写入换行符（win: \r\n  linux: \r  mac: \n）
            bw.flush(); // 手动刷新流
        }

        br.close();
        bw.close();
    }
}
```

# 五、标准输入输出

## 1. 标准输入

```java
package com.maqf.demo03;

import java.io.*;
import java.util.Scanner;

public class Demo01 {
    public static void main(String[] args) throws IOException {
        // InputStream in = System.in;
        // BufferedReader br = new BufferedReader(new InputStreamReader(in));
        BufferedWriter bw = new BufferedWriter(new FileWriter("./myTest.txt"));

        String line;
        while(true) {
            // if ((line = br.readLine()).equals("exit")) break;
            if ((line = new Scanner(System.in).nextLine()).equals("exit")) break;
            bw.write(line);
            bw.newLine();
            bw.flush();
        }

        // br.close();
        bw.close();
    }
}
```

### 1. Scanner 字符串采集

```java
package com.maqf.demo03;

import java.util.Scanner;

public class Demo02 {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        // String str = sc.next(); // 获取字符串，但是会被白空格（空格、回车、缩进）截断
        String str = sc.nextLine(); // 获取字符串，通过回车来采集一行
        System.out.println("Input: " + str);
    }
}
```

### 2. Scanner 匿名对象的使用

```java
package com.maqf.demo03;

import java.util.Scanner;

public class Demo03 {
    public static void main(String[] args) {
        /*
        Scanner sc = new Scanner(System.in);
        System.out.print("name: ");
        String name = sc.nextLine();
        System.out.print("age: ");
        int age = sc.nextInt();
        sc.nextInt(); // 清除残留换行符
        System.out.print("gender: ");
        String gender = sc.nextLine();
        */

        System.out.print("name: ");
        String name = new Scanner(System.in).nextLine();
        System.out.print("age: ");
        int age = new Scanner(System.in).nextInt();
        System.out.print("gender: ");
        String gender = new Scanner(System.in).nextLine();

        System.out.printf("my name is %s, %d, %s\n", name, age, gender);
    }
}
```

## 2. 标准输出

```java
package com.maqf.demo03;

import java.io.BufferedWriter;
import java.io.IOException;
import java.io.OutputStreamWriter;
import java.io.PrintStream;

public class Demo04 {
    public static void main(String[] args) throws IOException {
        PrintStream out = System.out;
        BufferedWriter bw = new BufferedWriter(new OutputStreamWriter(out));
        bw.write("Hello Java");
        System.out.println("Hello Java");
        System.out.printf("Hello %20s Java\n", "&&");
        System.out.printf("Hello %-20s Java\n", "&&");
        bw.close();
    }
}
```

## 3. 标准错误

```java
System.err.println("error");
```

# 六、序列化

## 1. 对象序列化

对象序列化是指将对象保存到磁盘当中，或者是在网络当中传输对象。
这种机制就是使用一个字节序列表示一个对象，该字节序列包括：对象的类型、对象的数据和对象中存储的属性等信息，字节序列写到文件之后，相当于文件中持久化保存了一个对象的信息。
返之，该字节序列还可以从文件中读取回来，重构对象，对其进行反序列化操作。

- 要实现序列化与反序列化就需要使用对象序列化流和对象反序列化流：
	- 对象序列化流：`ObjectOutputStream`
	- 对象反序列化流：`ObjectInputStream`

## 2. 对象的序列化流

- 对象序列化流： `ObjectOutputStream`

将Java对象的原始数据类型和图形写入`OutputStream`。可以使用`ObjectInputStream`读取（重构）对象。可以通过使用流的文件来实现对象的持久化存储。如果是网络套接字流，则可以在另一个主机上或另一个进程中重构对象。

- 构造序列化流：`ObjectOutputStream(OutputStream out)`: 创建一个写入指定的`ObjectStream`的`ObjectOutputStream`
- 序列化对象的方法：`void WriteObject(Object obj)`: 将指定的对象写入`ObjectOutputStream`

注意：
	- 一个对象如果想被序列化，该对象所属的类必须实现`Serializable`接口
	- `Serializable`是一个标记接口，实现该接口，不需要重写任何方法

## 3. 对象的反序列化流

- 对象反序列化流：`ObjectInputStream`
	- `ObjectInputStream`反序列化之前使用`ObjectOutputStream`序列化过的原始数据和对象
- 构造方法：`ObjectINputStream(InputStream in)`: 创建从指定的`InputStream`对象读取`ObjectInputStream`
- 反序列化对象的方法：`Ojbect readObject()`: 从`ObjectInputStream`读取一个对象

## 4. 案例（读写）

```java
package com.maqf.demo04;

import java.io.Serializable;

public class Student implements Serializable {
    private String name;
    private transient int age; // transient 标记不想序列化的成员
    // （之后添加的成员属性）序列化时没的成员，反序列化后是获取不到相应数据的
    private float score; 
    // 如果类修改过后，反序列化时会出错，要指定一个长整开型UID
    public static final long serialVersionUID = -8292359001271080669L; 

    public void m() {} // 之后添加的成员方法

    Student() {}

    Student(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public String getName() {
        return this.name;
    }

    public int getAge() {
        return this.age;
    }
}
```

```java
package com.maqf.demo04;

import java.io.*;

public class Demo01 {
    public static void main(String[] args) throws IOException, ClassNotFoundException {
        // 序列化
        ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream("./Student.txt"));
        oos.writeObject(new Student("MaQF", 29));
        oos.close();

        // 反序列化
        ObjectInputStream ois = new ObjectInputStream(new FileInputStream("./Student.txt"));
        Student stu = (Student) ois.readObject();
        System.out.println(stu);
        System.out.println(stu.getName());
        System.out.println(stu.getAge());
        ois.close();
    }
}
```


# 总结：

- 字节流
	- FileInputStream
	- FileOutputStream
	- ObjectInputStream
	- ObjectOutputStream

- 字符流
	- InputStreamReader
	- OutputStreamWriter
	- FileReader
	- FileWriter

- 缓冲区
	- 字节流缓冲区
		- BufferedInputStream
		- BufferedOutputStream
	- 字符流缓冲区
		- BufferedReader
		- BuferedWriter



