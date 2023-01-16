
# 一、构造方法

- File(String pathname)
- File(String parent, String child)
- File(File parent, String child)

```java
package com.maqf.demo01;

import java.io.File;

public class Demo01 {
    public static void main(String[] args) {
        File file01 = new File("./myDir/myTest.txt");
        System.out.println(file01);

        File file02 = new File("./myDir", "myText02.txt");
        System.out.println(file02);

        File file03 = new File("./myDir");
        File file04 = new File(file03, "myText03.txt");
        System.out.println(file04);
    }
}
```

# 二、File类的创建功能

- createNewFile(); 创建文件
- mkdir(); 创建目录
- mkdirs(); 创建多级目录

```java
package com.maqf.demo01;

import java.io.File;
import java.io.IOException;

public class Demo02 {
    public static void main(String[] args) throws IOException {
        File file = new File("./myTest.txt");
        System.out.println(file.createNewFile()); // 创建成功返回true，已存了创建不成功返回false

        File file1 = new File("./myDir");
        System.out.println(file1.mkdir());

        File file2 = new File("./H/E/L/L/O");
        System.out.println(file2.mkdirs());
    }
}
```

# 三、File类的判断和获取功能

- isDriectory(); 是否为目录
- isFile(); 是否为文件
- exists(); 是否存在
- getAbsolutePath(); 返回绝对路径
- getPath(); 返回给定的路径名字符串
- getName(); 返回File封装的文件或目录名
- list(); 返回目文件列表（字符串）
- listFiles(); 返回文件列表（对象）

```java
package com.maqf.demo01;

import java.io.File;
import java.io.IOException;
import java.util.Arrays;

public class Demo03 {
    public static void main(String[] args) throws IOException {
        File file = new File("./myTest.txt");
        file.createNewFile();
        // isDirectory()
        System.out.println(file.isDirectory());
        // isFile()
        System.out.println(file.isFile());
        // exists()
        System.out.println(file.exists());
        // getAbsolutePath()
        System.out.println(file.getAbsoluteFile());
        // getPath()
        System.out.println(file.getPath());
        // getName()
        System.out.println(file.getName());

        // list()
        File file1 = new File("./");
        System.out.println(Arrays.toString(file1.list()));
        // listFiles()
        File[] files = file1.listFiles();
        System.out.println(Arrays.toString(files));
    }
}
```

# 四、File类的删除功能

```java
package com.maqf.demo01;

import java.io.File;
import java.io.IOException;

public class Demo04 {
    public static void main(String[] args) throws IOException {
        File file = new File("./myTest.txt");
        // createFile(file);
        delFile(file);
    }

    private static void delFile(File file) {
        if (file.exists()) {
            if (file.delete())
                System.out.println(file.getName() + " 删除成功");
            else
                System.out.println(file.getName() + " 删除失败");
        } else {
            System.out.println(file.getName() + "文件不存在");
        }
    }

    private static void createFile(File file) throws IOException {
        if (file.exists()) {
            System.out.println(file.getName() + "文件已存在");
            return;
        }
        if (file.createNewFile())
            System.out.println(file.getName() + " 创建成功");
        else
            System.out.println(file.getName() + " 创建失败");
    }
}
```

## 案例-遍历目录和删除目录

```java
package com.maqf.demo01;

import java.io.File;

public class Demo06 {
    public static void main(String[] args) {
        final String SRC = "./delDir";
        File file = new File(SRC);
        printDirFile(file);
        delDir(file);
    }

    private static void delDir(File file) {
        if (!file.exists()) {
            System.out.println("文件不存在");
            return;
        }
        File[] files = file.listFiles();
        for (File f : files) {
            if (f.isDirectory())
                delDir(f);
            else
                f.delete();
        }
        // 最后再册除目录
        file.delete();
    }

    private static void printDirFile(File file) {
        if (!file.exists()) {
            System.out.println("文件或目录不存在");
            return;
        }
        File[] files = file.listFiles();
        for (File f : files) {
            if (f.isDirectory())
                printDirFile(f);
            else
                System.out.println(f.getName());
        }
    }
}
```






