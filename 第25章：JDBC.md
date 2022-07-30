## JDBC 

为了访问不同的数据库提供了统一的接口，为使用者屏蔽了细节问题

JDBC是Java提供一套用于数据库操作的接口API， Java程序员只需要面向这套接口编程即可。

---

### JDBC程序编写步骤

1.注册驱动
2.获取链接  (?useUnicode=true&characterEncoding=utf8  解决中文编码错误)
3.执行增删改查
4.释放资源

```Java
import com.mysql.jdbc.Driver;

import java.sql.Connection;
import java.sql.SQLException;
import java.sql.Statement;
import java.util.Properties;

/**
 * @author Zexi He.
 * @date 2022/6/14 14:29
 * @description:
 */
public class Jdbc01 {
    public static void main(String[] args) throws SQLException {
        // 前置工作：在项目下创建一个文件夹；
        // 将 mysql.jar 拷贝到该目录下，点击 add to project 加入到项目中

        //1. 注册驱动
        Driver driver = new Driver();   //先创建一个 driver 对象
        //2. 得到连接
        // 本质就是 前面说过的 socket 的连接
        String url = "jdbc:mysql://localhost:3306/hsp_db02?useUnicode=true&characterEncoding=utf8";
        //将用户名和密码封装到 properties 对象中
        Properties properties = new Properties();
        // 说明 user 和 password 是规定好的
        properties.setProperty("user", "root");// 用户
        properties.setProperty("password", "hzx");  //密码

        Connection connect = driver.connect(url, properties);

        //3. 执行 SQL语句
        //String sql = "insert into actor values(null, '刘德华', '男', '1970-11-11', '110')";
        String sql = "update actor set name='周星驰' where id = 1";
        // 用于执行sql语句并返回生成的结果的对象
        Statement statement = connect.createStatement();
        int rows = statement.executeUpdate(sql);    // 如果是 dml 语句,返回的说就是影响的行数
        System.out.println( rows > 0 ? "成功" : "失败");

        //4. 关闭资源连接
        statement.close();
        connect.close();
    }
}
```



### 连接数据库的5种方式：

```Java
public void connect05() throws ClassNotFoundException, SQLException, IOException {
        //通过properties获取信息
        Properties properties = new Properties();
        properties.load(new FileInputStream("src\\mysql.properties"));
        //获取相关的值
        String user = properties.getProperty("user");
        String password = properties.getProperty("password");
        String driver = properties.getProperty("driver");
        String url = properties.getProperty("url");

        Class.forName(driver);
        Connection connection = DriverManager.getConnection(url, user, password);
        System.out.println("方式5 = " + connection);
    }
```



### 课堂练习：

```Java
public class ClassPractice01 {
    public static void main(String[] args) throws IOException, ClassNotFoundException, SQLException {
        Properties properties = new Properties();
        properties.load(new FileInputStream("src\\mysql.properties"));
        //获取相关的值
        String driver = properties.getProperty("driver");
        String user = properties.getProperty("user");
        String password = properties.getProperty("password");
        String url = properties.getProperty("url");
        //创建连接
        Class.forName(driver);
        Connection connection = DriverManager.getConnection(url, user, password);

        //执行Mysql文件
        String sql1 = "insert into new values(1, 'hsp'),(2, 'hzx'),(3, 'jazx'),(4, 'difs'),(5, 'anna')";
        String sql2 = "update new set content='QQ最可爱！' where id = 1";
        String sql3 = "delete from new where id = 3";
        Statement statement = connection.createStatement();
        int rows = statement.executeUpdate(sql3);
        System.out.println(rows > 0 ? "执行成功" : "执行失败");

        //关闭资源
        statement.close();
        connection.close();
    }
}
```



## ResultSet [结果集]

1.表示数据库结果集的数据表，通常通过执行查询数据库的语句生成

2.ResultSet对象保持一个光标指向其当前的数据行。 最初，光标位于第一行之前

3.next方法将光标移动到下一行，并且由于在ResultSet对象种没有更多行时返回 false， 因此可以在 while循环中使用循环来遍历结果集

```java
@SuppressWarnings({"all"})
public class ResultSet_ {

    public static void main(String[] args) throws IOException, ClassNotFoundException, SQLException {
        Properties properties = new Properties();
        properties.load(new FileInputStream("src\\mysql.properties"));
        //获取相关的值
        String driver = properties.getProperty("driver");
        String user = properties.getProperty("user");
        String password = properties.getProperty("password");
        String url = properties.getProperty("url");
        //创建连接
        Class.forName(driver);
        Connection connection = DriverManager.getConnection(url, user, password);

        //得到statement
        Statement statement = connection.createStatement();
        //创建SQL
        String sql = "select id, name, sex, borndate from actor";
        
        ResultSet resultSet = statement.executeQuery(sql);
        //使用while循环取出
        while (resultSet.next()) {
            int id = resultSet.getInt(1);//获取改行的第一列数据
            String name = resultSet.getString(2);
            String sex = resultSet.getString(3);
            Date date = resultSet.getDate(4);
            System.out.println(id + "\t" + name + "\t" + sex + "\t" + date);
        }

        //关闭
        resultSet.close();
        statement.close();
        connection.close();
    }
}
```



## Statement

在建立连接后，需要对数据库进行访问，执行 命名或是 SQL 语句， 可以通过
1.Statement		[存在SQL注入]
2.PreparedStatement	[预处理]
3.CallableStatement		[存储过程]

**Statement对象执行 SQL 语句，存在 SQL 注入风险**

注：SQL注入是利用某些系统没有对用户输入的数据进行充分的检查，而在用户输入数据中注入非法的SQL语句段或命令，恶意攻击数据库

**要防范SQL注入，只要使用 PreparedStatement 取代 Statement**

---



## PreparedStatement

1.PreparedStatement执行的 SQL 语句中的参数用问好 （？） 来表示，调用 PreparedStatement对象的 SetXxx() 方法来设置这样蚕食。
2.调用  **executerQuery()**, 返回ResultSet 对象
3.调用 **executerUpdate()：** 执行更新，包括增、删、修改

```java
@SuppressWarnings({"all"})
public class PreparedStatement_ {
    public static void main(String[] args) throws IOException, ClassNotFoundException, SQLException {

        Scanner scanner = new Scanner(System.in);
        System.out.print("请输入管理员的名字:");
        //让用户输入 管理员名 和 密码
        String admin_name = scanner.nextLine();
        System.out.print("请输入管理员的密码:");
        String admin_pwd = scanner.nextLine();

        Properties properties = new Properties();
        properties.load(new FileInputStream("src\\mysql.properties"));
        //获取相关的值
        String driver = properties.getProperty("driver");
        String user = properties.getProperty("user");
        String password = properties.getProperty("password");
        String url = properties.getProperty("url");
        //创建连接
        Class.forName(driver);
        Connection connection = DriverManager.getConnection(url, user, password);

        String sql = "select name, pwd from admin where name = ? and  pwd = ?";

        //得到Preparedstatement
        // preparedStatement 对象实现了 PreparedStatement 接口的实现类的对象
        PreparedStatement preparedStatement = connection.prepareStatement(sql);
        //给 ？ 赋值
        preparedStatement.setString(1, admin_name);
        preparedStatement.setString(2, admin_pwd);

        //这里执行 excuteQuery, 不要再写 sql
        ResultSet resultSet = preparedStatement.executeQuery();
        if (resultSet.next()) {
            System.out.println("恭喜，登录成功！");
        } else {
            System.out.println("登陆失败！");
        }
        //关闭连接
        resultSet.close();
        preparedStatement.close();
        connection.close();
    }
}

=================== 修改成 executeUpdate()================
    //String sql = "insert into admin values(?, ?)";
    String sql = "update admin set pwd = ? where name = ?";
    //得到Preparedstatement
    // preparedStatement 对象实现了 PreparedStatement 接口的实现类的对象
    PreparedStatement preparedStatement = connection.prepareStatement(sql);
    //给 ？ 赋值
    preparedStatement.setString(2, admin_name);
    preparedStatement.setString(1, admin_pwd);
    //这里执行 excuteQuery, 不要再写 sql
    int rows = preparedStatement.executeUpdate();
    System.out.println(rows > 0 ? "Succeed!" : "Fail");
    //关闭连接
    preparedStatement.close();
    connection.close();
```



## JDBC API 小结

DriverManager驱动管理类 ->	getConnection(url, user, pwd);	获取到连接

Connection接口   ->					createStatement	创建Statement 对象
								        			preparedStatement(sql)	生成预处理对象

Statement接口 - >						executeUpdate(sql)	执行 dml 语句，返回影响的行数
													executeQuery(sql)	 执行查询			返回 resultSet
													execute(sql)	    执行任意 sql   	返回布尔值

PreparedStatement接口  ->	executeUpdate(sql) 		  执行 dml
												excuteQuery()  				 执行查询，返回 resultSet
												execute(sql)	    			  执行任意 sql   	返回布尔值
												SetXxx(占位符索引， 占位符的值)
												SetObject(占位符索引，占位符的值)	

ResultSet 结果集  ->				next() 向下移动一行， 同时如果没有下一行， 返回 false
												previous() 向上移动一行， 如果没有上一行了， 返回 false
												getXxx(列的索引值，列名) 
												getObject(列的索引值， 列名)  		

---

​	

## 封装JDBC工具类

```Java
package com.hspedu.jdbc.utils;

import java.io.FileInputStream;
import java.io.IOException;
import java.sql.*;
import java.util.Properties;

/**
 * @author Zexi He.
 * @date 2022/6/14 17:55
 * @description:    这是一个工具类，完成 mysql 的连接和关闭资源
 */
public class JDBCUtils {
    // 定义相关的属性, 做成
    private static String user; //用户名
    private static String password; //密码
    private static String url; //url
    private static String driver; //驱动名

    //在 static 代码块中去初始化
    static {
        Properties properties = new Properties();
        try {
            properties.load(new FileInputStream("src\\mysql.properties"));
            user = properties.getProperty("user");
            password = properties.getProperty("password");
            url = properties.getProperty("url");
            driver = properties.getProperty("driver");
        } catch (IOException e) {
            /*
            在实际开发中，我们可以抛出
            1.将编译异常 转成 运行异常
            2. 这是调用者，可以选择捕获该异常，也可以选择默认处理该异常，比较方便;
             */
            throw new RuntimeException(e);
        }
    }

    // 编写方法，连接数据库
    public static Connection getConnection() {
        try {
            return DriverManager.getConnection(url, user, password);
        } catch (SQLException e) {
            //将编译异常转化成 运行异常
            throw new RuntimeException(e);
        }
    }

    //关闭资源
    /*
        1. ResultSet 结果集
        2. Statement 或者 PreparedStatement
        3. Connection
        4. 如果需要关闭资源，传入对象； 否则传入 null
     */
    public static void close(ResultSet resultSet, Statement statement, Connection connection) {
        try {
            if (resultSet != null) {
                resultSet.close();
            }
            if (statement != null) {
                statement.close();
            }
            if (connection != null) {
                connection.close();
            }
        } catch (SQLException e) {
            throw new RuntimeException(e);
        }
    }
}
```



## 使用工具类

```Java
package com.hspedu.jdbc.utils;

import org.testng.annotations.Test;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;

/**
 * @author Zexi He.
 * @date 2022/6/14 18:05
 * @description:    该类演示如何使用 JdbcUtils
 */
public class JDBCUtilsUse_ {

    public static void main(String[] args) {

        //测试

    }

    @Test
    public void testSelect() {
        // 1.得到连接
        Connection connection = null;

        //2.组织sql
        String sql = "Select * from actor where name = ? and id = ?";
        //3. 创建 PreparedStatement
        PreparedStatement preparedStatement = null;
        //4.创建一个ResultSet
        ResultSet resultSet = null;

        try {
            connection = JDBCUtils.getConnection();
            preparedStatement = connection.prepareStatement(sql);
            //占位符赋值
            preparedStatement.setString(1, "刘德华");
            preparedStatement.setInt(2, 2);
            //执行
            resultSet = preparedStatement.executeQuery();
            while (resultSet.next()) {
                int id = resultSet.getInt("id");
                String name = resultSet.getString("name");
                String sex = resultSet.getString("sex");
                String borndate = resultSet.getString("borndate");
                String phone = resultSet.getString("phone");
                System.out.println(id + "\t" + name + "\t" + sex + "\t" + borndate +
                            "\t" + phone);
            }
        } catch (SQLException e) {
            e.printStackTrace();
        } finally {
            //关闭
            JDBCUtils.close(resultSet, preparedStatement, connection);
        }
    }



    @Test
    public void testDML() {
        // 1.得到连接
        Connection connection = null;

        //2.组织sql
        String sql = "update actor set name = ? where id = ?";
        PreparedStatement preparedStatement = null;
        //3. 创建 PreparedStatement
        try {
            connection = JDBCUtils.getConnection();
            preparedStatement = connection.prepareStatement(sql);
            //占位符赋值
            preparedStatement.setString(1, "吴梦淇");
            preparedStatement.setInt(2, 1);
            //执行
            preparedStatement.executeUpdate();
        } catch (SQLException e) {
            e.printStackTrace();
        } finally {
            //关闭
            JDBCUtils.close(null, preparedStatement, connection);
        }
    }
}
```



## 事务

调用 Connection 对象后， 设置 setAutoCommit(false)	可以取消自动提交事务

所有SQL语句都成功执行了过后， 调用 Connection 的 commit(); 方法提交事务

在其中某个操作失败或出现异常时，调用Connection 的 rollback(); 方法回滚事务

### Java使用事务

```java
package com.hspedu.jdbc.transaction_;

import com.hspedu.jdbc.utils.JDBCUtils;
import org.testng.annotations.Test;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.SQLException;

/**
 * @author Zexi He.
 * @date 2022/6/14 21:07
 * @description:    演示使用事务
 */
@SuppressWarnings({"all"})
public class Transaction_ {

    //没有使用事务
    @Test
    public void noTransaction() {
        // 1.得到连接
        Connection connection = null;
        //2.组织sql
        String sql_1 = "update account set balance = balance - 100 where id = 1";
        String sql_2 = "update account set balance = balance +100 where id = 2";
        PreparedStatement preparedStatement = null;
        //3. 创建 PreparedStatement
        try {
            connection = JDBCUtils.getConnection();     //在默认情况下 connection是默认自动提交
            //执行第一条 sql
            preparedStatement = connection.prepareStatement(sql_1);
            preparedStatement.executeUpdate();
            // 执行第二条
            int i = 1 / 0;
            preparedStatement = connection.prepareStatement(sql_2);
            preparedStatement.executeUpdate();
        } catch (SQLException e) {
            e.printStackTrace();
        } finally {
            //关闭
            JDBCUtils.close(null, preparedStatement, connection);
        }
    }

    //使用事务来解决
    @Test
    public void useTransaction() {
        // 1.得到连接
        Connection connection = null;
        //2.组织sql
        String sql_1 = "update account set balance = balance - 100 where id = 1";
        String sql_2 = "update account set balance = balance +100 where id = 2";
        PreparedStatement preparedStatement = null;
        //3. 创建 PreparedStatement
        try {
            connection = JDBCUtils.getConnection();     //在默认情况下 connection是默认自动提交
            //将 connection 设置为不自动提交
            connection.setAutoCommit(false);        //开启了事务
            //执行第一条 sql
            preparedStatement = connection.prepareStatement(sql_1);
            preparedStatement.executeUpdate();
            // 执行第二条
            preparedStatement = connection.prepareStatement(sql_2);
            preparedStatement.executeUpdate();

            //提交事务
            connection.commit();
        } catch (SQLException e) {
            //这里我们可以进行回滚;   即撤销执行的 SQL
            //默认回滚到事务开始的状态
            System.out.println("执行发生了异常；撤销");
            try {
                connection.rollback();
            } catch (SQLException ex) {
                ex.printStackTrace();
            }
            e.printStackTrace();
        } finally {
            //关闭
            JDBCUtils.close(null, preparedStatement, connection);
        }
    }
}

```



## 批处理

addBath()	添加需要批量处理的 SQL 语句或参数
executeBath()	执行批量处理语句
clearBath()	清空批处理包

**JDBC连接 Mysql 时，如果需要使用批处理功能，请再 url 中加参**

```java
@Test
    public void useBatch() throws SQLException {
        Connection connection = JDBCUtils.getConnection();
        String sql = "insert into admin2 values(null, ?, ?)";
        PreparedStatement preparedStatement = connection.prepareStatement(sql);
        System.out.println("开始执行");
        long start = System.currentTimeMillis();
        for (int i = 0; i < 5000; i++) {
            preparedStatement.setString(1, "jack" + i);
            preparedStatement.setString(2, "666");
            //将sql语句加入到批处理包中
            preparedStatement.addBatch();
            //当有1000条时，再批量执行
            if ((i + 1) % 1000 == 0) {
                preparedStatement.executeBatch();
                //清空
                preparedStatement.clearBatch();
            }
        }
        long end = System.currentTimeMillis();
        System.out.println("批量方法的时间:" + (end - start));     //78
        JDBCUtils.close(null, preparedStatement, connection);
    }
```

---



## 数据库连接池

传统获取 Connection问题分析

1.耗时长，占用过多系统资源，容易造成服务器崩溃
2.如果程序出现异常而未能正常关闭，将导致数据库内存泄漏，最终导致重启数据库。

### **连接池介绍：**

1.预先再缓冲池中放入一定数量的连接
2.数据库连接词负责分配、管理和释放数据库连接
3.当应用程序向连接池请求的连接数超过最大连接数量时，这些请求将呗加入到等待队列中

### 连接池种类：

1.C3P0	（hibernate、spring）
2.DBCP
3.Proxool
4.BoneCP
**5.Druid	(德鲁伊)**

### C3P0创建和连接：

```java
    //第二种方式：    使用配置文件模板来使用
    //1. 将 c3p0 提供的 c3p0.config.xml 拷贝到 src 目录下
    @Test
    public void testC3P0_02() throws SQLException {
        ComboPooledDataSource comboPooledDataSource = new ComboPooledDataSource("hzx");
//        Connection connection = comboPooledDataSource.getConnection();
//        System.out.println("连接成功！");
//        connection.close();
        System.out.println("开始执行！");
        long start = System.currentTimeMillis();
        for (int i = 0; i < 5000; i++) {
            Connection connection = comboPooledDataSource.getConnection();  //从 DataSource 实现
            connection.close();
        }
        long end = System.currentTimeMillis();
        System.out.println("测试时间方法二 = " + (end - start));
    }
```



### 德鲁伊创建和连接

```Java
@Test
public class Druid_ {
    public void testDruid_() throws Exception {
        //1. 加入 德鲁伊 的 jar 包
        //2. 加入 配置文件 druid.properties, 将该文件拷贝项目到 src 目录
        //3. 创建一个 properties 对象 用来读取文件
        Properties properties = new Properties();
        properties.load(new FileInputStream("src\\druid.properties"));

        //4.创建一个指定参数的数据库连接池 Druid 的连接池
        DataSource dataSource = DruidDataSourceFactory.createDataSource(properties);
        Connection connection = dataSource.getConnection();
        System.out.println("连接成功！");
        connection.close();

        //测试速度
//        System.out.println("开始执行！");
//        long start = System.currentTimeMillis();
//        for (int i = 0; i < 500000; i++) {
//            Connection connection = dataSource.getConnection();
//            connection.close();
//        }
//        long end = System.currentTimeMillis();
//        System.out.println("德鲁伊连接池测试速度= " + (end - start));
    }
}
```



---

## ApacheDBUtils 引出

问题：
1.结果集合 connection 是关联的，即如过关闭连接，就不能使用结果集
2.结果集不利于数据管理 【只能使用一次】
3.使用返回信息也不方便

```Java
package com.hspedu.jdbc.datasource;

import org.apache.commons.dbutils.QueryRunner;
import org.apache.commons.dbutils.handlers.BeanHandler;
import org.apache.commons.dbutils.handlers.BeanListHandler;
import org.testng.annotations.Test;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.util.List;

/**
 * @author Zexi He.
 * @date 2022/6/14 23:23
 * @description:    使用 apache-DBUtils 工具类 + druid 完成对表的crud 操作
 */

@SuppressWarnings({"all"})
public class DBUtils_Use {
    @Test
    public void testQueryMany() throws SQLException {
            //返回结果是多行数据
        //1.得到廉价而
        Connection connection = JDBCUtilsByDruid.getConnection();
        //2.使用 DBUtils 类和接口,先引入 DBUtils 的 jar 文件, 加入到 Project
        //3. 创建一个 QueryRunner
        QueryRunner queryRunner = new QueryRunner();
        //4.就可以执行相关的方法，返回一个结果集
        String sql = "Select * from actor where id >= ?";
        // sql 语句也可以查询部分列
        
        //1. query 方法执行 SQL 语句，得到 Resultset --> 封装到 --> ArrayList 集合中
        //2.返回集合
        //3. connection: 连接
        //4. SQL: 执行 SQL 语句
        //5. new BeanListHandler<>(Actor.class): 在 resultset --> Actor 对象 -> 封装到ArrayList
        //  底层使用反射机制 去获取 Actor 属性
        // 1 就是给 sql 中的 问号 赋值的
        List<Actor> list = queryRunner.query(connection, sql, new BeanListHandler<>(Actor.class),1);
        for (Actor actor : list) {
            System.out.println(actor);
        }

        // 释放资源
        JDBCUtilsByDruid.close(null, null, connection);
    }
}
```

---



## BasicDao

1.SQL语句是股东的，不能通过参数传入，通用性不好，需要进行改进，更方便执行 增删改查
2.对应 Select 操作，如果有返回值，返回类型不能固定，需要使用泛型
3.将来的表很多，业务需求负责，不可能只靠一个 java类完成

Dao:	data access object 数据访问对象
BasicDao: 是专门和数据科交互的，即完成对数据库（表）的 crud 操作

```Java
public class BasicDao<T> {  //泛型指定具体的类型

    private QueryRunner queryrunner =  new QueryRunner();

    //开发通用的 dml 方法，针对任意的表
    public int update(String sql, Object... parameters) {
        Connection connection = null;
        try {
            connection = JDBCUtilsByDruid.getConnection();
            int updateRows = queryrunner.update(connection, sql, parameters);
            return updateRows;
        } catch (SQLException e) {
            throw new RuntimeException(e);
        } finally {
            JDBCUtilsByDruid.close(null, null, connection);
        }
    }

    // 返回多个对象（即查询的结果是多行),针对任意的表
    /**
     *
     * @param sql   SQL语句
     * @param clazz     传入一个类的Class对象
     * @param parameters    传入 ？ 的具体的值，可以是多个
     * @return  根据 类.class 返回对应的 ArrayList 集合
     */
    public List<T> queryMultiply(String sql, Class<T> clazz, Object... parameters) {
        Connection connection = null;
        try {
            connection = JDBCUtilsByDruid.getConnection();
            List<T> query = queryrunner.query(connection, sql, new BeanListHandler<T>(clazz), parameters);
            return query;
        } catch (SQLException e) {
            throw new RuntimeException(e);
        } finally {
            JDBCUtilsByDruid.close(null, null, connection);
        }
    }

    // 查询单行结果 的通用方法
    public T querySingle(String sql, Class<T> clazz, Object... parameters) {
        Connection connection = null;
        try {
            connection = JDBCUtilsByDruid.getConnection();
            return queryrunner.query(connection, sql, new BeanHandler<T>(clazz), parameters);
        } catch (SQLException e) {
            throw new RuntimeException(e);
        } finally {
            JDBCUtilsByDruid.close(null, null, connection);
        }
    }

    // 查询单行单列的方法，即返回单值的方法
    public Object queryScalar(String sql, Object... parameters) {
        Connection connection = null;
        try {
            connection = JDBCUtilsByDruid.getConnection();
            return queryrunner.query(connection, sql, new ScalarHandler(), parameters);
        } catch (SQLException e) {
            throw new RuntimeException(e);
        } finally {
            JDBCUtilsByDruid.close(null, null, connection);
        }
    }

}
```

