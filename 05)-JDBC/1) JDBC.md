> (Java DataBase Connectivity) - 由一些接口和类构成的API，J2SE的一部分，由java.sql, javax.sql包组成。

```java
package com.jdbc.demo01;

import java.sql.*;

public class JdbcTest {
    public static void main(String[] args) throws SQLException, ClassNotFoundException {
        // 1. 注册驱区
        // a. 将MySQL的驱动包复制到一个目录下（如：当前项目根目录）【oracle官网下载】
        // b. 在IDEA中设置导入驱动包（.jar）【file -> Project structure -> Libraries】
        // c. 通过代码注册驱动（三种方式）
        /*
        DriverManager.registerDriver(new com.mysql.jdbc.Driver());
         */
        /*
        System.setProperty("jdbc.drivers", "com.mysql.jdbc.Driver");
         */
        Class.forName("com.mysql.jdbc.Driver");
        // 2. 建立连接
        final String URL = "jdbc:mysql://localhost:3306/lx_java";
        final String USER = "root";
        final String PSW = "root";
        Connection conn = DriverManager.getConnection(URL, USER, PSW);
        // 3. 创建执行sql语句的对象
        Statement st = conn.createStatement();
        // 4. 组织并执行sql语句
        String sql = "select `id`, `name`, `gender`, `age`, `score`, `tel`, `classid` from `stu`;";
        ResultSet res = st.executeQuery(sql);
        // 5. 处理结果
        while (res.next()) {
            System.out.println(
                    /*res.getObject(1) + "\t" +
                    res.getObject(2) + "\t" +
                    res.getObject(3) + "\t" +
                    res.getObject(4) + "\t" +
                    res.getObject(5) + "\t" +
                    res.getObject(6) + "\t" +
                    res.getObject(7)*/
                    res.getInt("id") + "\t" +
                            res.getString("name") + "\t" +
                            res.getString("gender") + "\t" +
                            res.getInt("age") + "\t" +
                            res.getFloat("score") + "\t" +
                            res.getString("tel") + "\t" +
                            res.getString("classid")
            );
        }
        // 6. 释放资源
        res.close();
        st.close();
        conn.close();
    }
}
```


> [! 补充]
> Java中的特殊语句块

```java
package com.jdbc.demo02;

public class Father {
    public Father() {
        System.out.println("父类中的构造方法");
    }

    {
        System.out.println("父类中的构造语句块");
    }
    
    static {
        System.out.println("父类中的静态构造语句块");
    }
}
```

```java
package com.jdbc.demo02;

public class Son extends Father {
    // 构造方法，创建实时在构造语句之后执行
    public Son() {
        System.out.println("子类中的构造方法");
    }

    // 构造语句块，创建实例时在构造方法之前执行
    {
        System.out.println("子类中的构造语句块");
    }

    // 静态构造语句块，类被加载时执行，且只会执行一次
    static {
        System.out.println("子类中的静态构造语句块");
    }
}
```

```java
package com.jdbc.demo02;

public class Main {
    public static void main(String[] args) {
        Son son = new Son();
    }
}
/*
父类中的静态构造语句块
子类中的静态构造语句块
父类中的构造语句块
父类中的构造方法
子类中的构造语句块
子类中的构造方法
*/
```

# 案例

## 1. 通过静态方法封装

```java
package com.jdbc.demo03;

import java.sql.*;

public class JDBCUtils {
    private static final String URL = "jdbc:mysql://localhost:3306/lx_java";
    private static final String USER = "root";
    private static final String PSW = "root";

    // 初始化数据库驱动
    static {
        try {
            DriverManager.registerDriver(new com.mysql.jdbc.Driver());
        } catch (SQLException e) {
            throw new RuntimeException(e);
        }
    }

    // 私有化构造方，禁止实列化
    private JDBCUtils() {
    }

    // 创建数据库连接对象
    public static Connection getConnection() {
        Connection conn = null;
        try {
            conn = DriverManager.getConnection(URL, USER, PSW);
            return conn;
        } catch (SQLException e) {
            throw new RuntimeException(e);
        }
    }

    // 释放资源
    public static void free(ResultSet res, Statement st, Connection conn) {
        try {
            if (null != res)
                res.close();
        } catch (SQLException e) {
            throw new RuntimeException(e);
        } finally {
            try {
                if (null != st)
                    st.close();
            } catch (SQLException e) {
                throw new RuntimeException(e);
            } finally {
                try {
                    if (null != conn)
                        conn.close();
                } catch (SQLException e) {
                    throw new RuntimeException(e);
                }
            }
        }
    }
}
```

```java
package com.jdbc.demo03;

import java.sql.Connection;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;

public class JDBCUtilsTest {
    public static void main(String[] args) throws SQLException {
        Connection conn = JDBCUtils.getConnection();
        Statement st = conn.createStatement();
        String sql = "select `id`, `name` from `teacher`";
        ResultSet res = st.executeQuery(sql);
        showRes(res);
        JDBCUtils.free(res, st, conn);
    }

    private static void showRes(ResultSet res) throws SQLException {
        while (res.next()) {
            System.out.println(
                    res.getInt("id") + "\t" +
                            res.getString("name")
            );
        }
    }
}
```




