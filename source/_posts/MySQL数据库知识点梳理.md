---
title: MySQL数据库知识点梳理
date: 2021-04-28 11:27:30
tags: 数据库
categories: 编程
---


# MySQL数据库知识点梳理
## 1. 数据库基本命令
```sql
MYSQL -UROOT -P123456 -- 连接数据库
UPDATE MYSQL.USER SET AUTHENTICATION_STRING=PASSWORD('123456') WHERE USER='ROOT' AND HOST='LOCALHOST'; -- 修改用户密码
FLUSH PRIVILEGES; -- 刷新权限
SHOW DATABASES; -- 查看所有数据库
USE 数据库名; -- 切换数据库；
SHOW TABLES; -- 查看数据库中所有表
DESCRIBE 表名; -- 显示数据库中所有的表信息
SHOW CREATE DATABASE `库名`; -- 查看创建数据库语句
SHOW CREATE TABLE `表名;` -- 查看创建数据表语句
DESC `表名`; -- 查看表结构
EXIT; -- 退出连接
```



## 2. 数据库操作

```sql
CREATE DATABASE [IF NOT EXISTS] 数据库名; -- 创建数据库
DROP DATABASE [IF EXISTS] 数据库名; -- 删除数据库
```



## 3. 数据库的列类型

- 数值
  - tinyint 1字节
  - smallint 2字节
  - mediumint 3字节
  - **int 常用标准整数 4字节**
  - bigint 8字节
  - float 浮点数 4字节
  - double 浮点数 8字节
  - decimal 字符串形式的浮点数 用于金融计算
  
- 字符串
  - char 固定大小0~255
  - **varchar 可变字符串 0~65535**
  - tinytext 微型文本 2^8-1
  - **text 大文本 2^16-1**

- 时间日期

  - date YYYY-MM-DD 日期格式
  - time HH:mm:ss 时间格式
  - **datetime  YYYY-MM-DD HH:mm:ss 常用时间格式**
  - timestamp 时间戳
  - year 年份表示

- null



## 4. 数据库字段属性

- Unsigned：
  - 无符号整数
  - 声明该列不能为负数
  
- zerofill： 不足的位数0填充

- 自增： 设计唯一主键，类型必须为整数，可以自定义起始值和步长

- 非空：
  - not null 不赋值会报错
  - null 不赋值默认为null
  
- default

  

## 5. 数据表操作

1. 创建表

   ```sql
   CREATE TABLE [IF NOT EXISTS] `表名`(
    `字段名` 列类型 [属性] [索引] [注释],
   )[表类型][字符集][注释];
   ```

 2. 修改表

    ```sql
    ALTER TABLE 表名 RENAME AS 表名; -- 修改表名 
    ALTER TABLE 表名 ADD 字段名 列属性; -- 表增加字段
    ALTER TABLE 表名 MODIFY 字段名 列属性; -- 表修改约束
    ALTER TABLE 表名 CHANGE 字段名 字段名 列属性; -- 表字段重命名
    ```

3. 删除表字段

   ```sql
   ALTER TABLE 表名 DROP 字段名;
   ```

4. 删除表

    ```sql
    DROP TABLE IF EXISTs 表名;
    ```



## 6. 引擎

- INNODB（默认使用）：安全性高，事务处理。多表多用户操作
  - 支持事务
  - 支持数据行锁定
  - 支持外键约束
  - 不支持全文索引
  - 表空间大小：大
- MYISAM（较早版本使用）：节约空间，速度较快
  - 不支持事务
  - 不支持数据行锁定
  - 不支持外键约束
  - 支持全文索引
  - 表空间大小：小



## 7. DML

- 插入

  ```sql
  INSERT INTO 表名([字段1,字段2]) values ('值1','值2'),('值1','值2');
  ```

- 修改

  ```sql
  UPDATE 表名 SET 字段1 = 值1, 字段2 = 值2, WHERE [条件];
  ```

- 删除

  ```sql
  DELETE FROM 表名 WHERE [条件];
  ```

  ```sql
  TRUNCATE 表名; -- 清空表数据，重置自增列，不影响事务
  ```

- 查询	

    ```sql
    SELECT [ALL|DISTINCT|DISTINCTROW|TOP]
    {*|talbe.*|[table.]field1[AS alias1][,[table.]field2[AS alias2][,…]]}
    FROM TABLE [AS 别名]
    [LEFT | RIGHT | INNER join TABLE on …]
    [WHERE…]
    [GROUP BY…] -- 通过哪个字段分组
    [HAVING…] -- 过滤分组的记录必须满足的次要条件
    [ORDER BY 字段名 ASC|DESC]
    [LIMIT 查询起始下标,数量];
    ```
    
    ```sql
    SELECT @@auto_increment_increment; -- 查询自增步长
    ```



## 8. 七种JOIN

{% asset_img qizhongjon.png %}



## 9. MYSQL常用函数

- 数学运算函数
 ```sql
  ABS(-8)  -- 8，绝对值
  CEILING(9.4) -- 10，向上取整
  FLOOR(9.4) -- 9，向下取整
  RAND() -- 返回0-1的随机数
  SIGN() -- 返回一个数的符号，正数1，负数-1
 ```
- 字符串函数
 ```sql
  CHAR_LENGTH('字符串长度') -- 返回字符串长度
  CONCAT('拼接','多个','字符串') -- 拼接字符串
  INSERT(源字符串,位置,长度,插入字符串) -- 查找位置替换对应长度的字符串
  LOWER(字符串) -- 转小写
  UPPER(字符串) -- 转大写
  INSTR(字符串,子串) -- 返回第一次出现的子串的位置
  REPLACE(字符串,被替换,需替换) -- 替换指定字符串
  SUBSTR(字符串,截取起始位置,长度) -- 截取指定位置和长度的字符串
  REVERSE(字符串) -- 反转
  MD5(明文) -- MD5 加密
 ```
- 时间和日期函数
 ```sql
  CURRENT_DATE() -- 获取当前日期
  CURDATE() -- 获取当前日期
  NOW() -- 获取当前时间，时分秒
  LOCALTIME -- 获取本地时间，时分秒
  SYSDATE() -- 系统时间
  YEAR()
  MONTH()
  DAY()
  HOUR()
  MINUTE()
  SECOND()
 ```
- 系统函数
 ```sql
  SYSTEM_USER()
  USER()
  VERSION() -- 获取版本
 ```
- 聚合函数

```SQL
COUNT() -- 统计数量，count(字段)忽略null，count(*),count(1)不会忽略null
SUM() -- 求和
AVG() -- 求平均
MAX() -- 求最大
MIN() -- 求最小
```



## 10. 事务

将一组SQL放在一个批次中去执行

 1. 事务原则：ACID原则

    - 原子性：一起成功或一起失败

    - 一致性：操作前后数据完整性最终一致

    - 隔离性：多个用户同时操作，排除其他事务对本次事务的影响

    - 持久性：事务结束后的数据不随外界原因导致丢失，事务未提交恢复原状，事务提交后持久化到数据库，一旦提交就不可逆
2. 事务的隔离级别

    - 脏读：一个事务读取到另一个事务未提交的数据
	- 不可重复度：在一个事务内读取表中的某一行数据，多次读取结果不同
	- 虚读（幻读）：在一个事务内读取到了别的事务插入的数据，导致前后读取不一致（比如多了一条）

3. 手动处理事务

   ```sql
   SET autocommit = 0; 关闭事务自动提交
   START TRANSACTION; -- 标记一个事务开始
   ...
   COMMIT; -- 提交
   ROLLBACK; -- 回滚
   SET autocommit = 1; 开启事务自动提交
   SAVEPOINT; -- 保存点
   ROLLBACK TO SAVEPOINT; -- 回滚到保存点
   RELEASE  SAVEPOINT; -- 撤销保存点
   ```

   

## 11. 索引

索引（index）是帮助MYSQL高效获取数据的数据结构。

1. 索引的分类

   - 主键索引（PRIMARY KEY）唯一标识，不可重复，只能有一个列作为主键
   - 唯一索引（UNIQUE KEY）避免重复的列出现，多个列都可以标识
   - 常规索引（KEY/INDEX）默认的
   - 全文索引（FULLTEXT）快速定位数据，在特定数据库引擎下才有

  2. 索引的使用

     ```sql
     SHOW INDEX FROM 表名; -- 显示所有索引信息
     ALTER TABLE 表名 ADD 索引类型 index 索引名(字段名); -- 添加索引
     CREATE INDEX 索引名 on 表名(字段名); -- 添加索引
     SELECT * FROM 表名 use index(索引名) WHERE ...; -- 提示SQL使用索引
     SELECT * FROM 表名 ignore index(索引名) WHERE ...; -- 提示SQL忽略使用索引
     SELECT * FROM 表名 force index(索引名) WHERE ...; -- 提示SQL必须使用索引
     ```

3. 索引原则

   - 索引不是越多越好
   - 不要对进程变动数据加索引
   - 小数据量表不需要加索引
   - 索引一般加在常用来查询的字段上
   - 覆盖索引：查询使用了索引，并且需要返回的列在该索引中全部能找到，减少select *

 4. 索引失效情况

    - 最左前缀原则：如果索引了多列（联合索引），查询从最左列开始，并且不跳过索引中的列。如果跳过某一列，索引将部分失效（后面的字段索引失效）。
    - 联合索引中，出现范围查询（大于小于），范围查询右侧的列索引失效。业务允许的情况下使用大于等于、小于等于。
    - 不要在索引列上进行运算，索引将失效。
    - 字符串类型不加单引号，索引失效。
    - 头部模糊匹配（%_），索引失效。尾部模糊匹配，索引不失效。
    - or分隔的条件，如果or两侧的条件有一侧无索引，则涉及的索引都不会被用到。
    - 如果MySQL评估使用索引比全表扫描更慢，则不使用索引。



## 12. 用户管理

用户表：mysql.user

```sql
CREATE USER 用户名 IDENTIFIED BY 密码; -- 创建用户
SET PASSWORD = PASSWORD(密码); -- 修改当前用户密码
SET PASSWORD FOR 用户名 = PASSWORD(密码); -- 修改指定用户密码
RENAME USER 用户名 TO 新用户名; -- 重命名
GRANT ALL PRIVILEGES ON 库.表 TO 用户; -- 用户授权所有权限
SHOW GRANTS FOR 用户; -- 查询权限
SHOW GRANTS FOR root@localhost; -- 查询权限
REVOKE ALL PRIVILEGES ON 库.表 FROM 用户; -- 撤销权限
DROP USER 用户名; -- 删除用户
```

命令行导出数据库：mysqldump -h主机名 -uroot -p密码 库名 表名1 表名2  > 磁盘名/文件名

命令行导入数据库：source 磁盘名/文件名



## 13. 三大范式

- 第一范式（1NF）：数据库表的每一列都是不可分割的原子数据项
- 第二范式（2NF）：（满足第一范式）数据库表中的每一列都和主键相关，而不是只与主键的某一列相关
- 第三范式（3NF）：（满足第一范式和第二范式）数据库表的每一列都与主键直接相关，而不是间接相关



## 14. JDBC

```java
package com.jdbc.db;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.ResultSet;
import java.sql.SQLException;
import com.mysql.jdbc.Statement;

public class DBUTIL {
	private	static final String URL = "jdbc:mysql://localhost:3306/database";
	private static final String USERNAME = "root";
	private static final String PASSWORD = "password";
	
	public static void main(String[] args) throws ClassNotFoundException, SQLException {
		// JDBC 的三要素文件，加载驱动，获取连接，
		//1. 加载驱动程序
		Class.forName("com.mysql.jdbc.Driver");
		//2. 获取数据库的连接 Connection 数据库连接对象
		Connection connection = DriverManager.getConnection(URL,USERNAME,PASSWORD);
		//3. 通过数据库的连接操作数据库，实现增删改查，Statement SQL执行对象
		Statement statement = connection.createStatement();
		//4. 返回 ResultSet 对象，编写 sql 语句，实现最简单的增删改查
		ResultSet resultSet = statement.executeQuery("select username,password from user");
		//打印数据
		while(resultSet.next()) {
			//返回字符串
			System.out.println(rs.getString("username") + rs.getString("password"));
		}
        //5. 释放连接
        resultSet.close();
        statement.close();
        connection.close();
	}
}
```

```java
package com.thb.study3;

import com.thb.study2.Utils.JdbcUtils;
import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.SQLException;

public class Test03 {
    public static void main(String[] args) {
        Connection connection = null;
        PreparedStatement preparedStatement = null;// 预执行
        try {
            connection = JdbcUtils.getConnection();
            connection.setAutoCommit(false);// 关闭自动提交，开启事务
            String sql = "update account set money=money-100 where `name`='A'";
            preparedStatement = connection.prepareStatement(sql);
            preparedStatement.executeUpdate();
            String sql2 = "update account set money=money+100 where `name`='B'";
            preparedStatement = connection.prepareStatement(sql2);
            preparedStatement.executeUpdate();
            connection.commit();
            System.out.println("成功");
        } catch (SQLException e) {
            //默认失败会回滚
            connection.rollback();
            e.printStackTrace();
        }finally {
            JdbcUtils.release(connection,preparedStatement,null);
        }
    }
}
```



## 15. 视图

虚拟存在的表，数据来自定义视图的查询中使用的表，并且是在使用视图时动态生成的。只保留sql的查询逻辑，不保存查询结果。

作用：1. 简化用户对数据的理解，也简化用户的操作。2. 设定视图用户的操作权限。3. 屏蔽真实表结构变化带来的影响

- 创建

  ```sql
  CERATE VIEW 视图名 AS SELECT语句  [WITH[CASCADED | LOCAL] CHECK OPTION]; -- CASCADED 检查依赖的视图，LOCAL不检查依赖的视图
  ```

- 查询

  ```sql
  SHOW CREATE VIEW 视图名;
  SELECT * FROM 视图名;
  ```
  
- 修改

  ```sql
  CERATE OR REPLACE VIEW 视图名 AS SELECT语句  [WITH[CASCADED | LOCAL] CHECK OPTION]; -- 方式一
  ALTER VIEW 视图名 AS SELECT语句 [WITH[CASCADED | LOCAL] CHECK OPTION]; -- 方式二
  ```

- 删除

  ```sql
  DROP VIEW [IF EXISTS] 视图名;
  ```



## 16. 存储过程

数据库sql语言层面的代码封装和重用。

- 创建

  ```sql
  DELIMITER $$ -- 定义结束符
  CREATE PROCEDURE 存储过程名称([IN|OUT|INOUT 参数名 参数类型])
  BEGIN 
  	-- SQL语句
  END$$
  DELIMITER ; -- 重置结束符
  ```

- 调用

  ```sql
  CALL 名称([参数])
  ```

- 查看

  ```sql
  SELECT * FROM INFORMATION_SCHEMA_ROUTINES WHERE ROUTINE_SCHEMA = 'xxx';  -- 查询指定数据库的存储过程及状态信息
  SHOW CREATE PROCEDURE 存储过程名称;  -- 查询存储过程的定义
  ```
  
- 删除
  
  ```sql
  DROP PROCEDURE [IF EXISTS] 存储过程名称;
  ```
  
- 变量
  
  - 系统变量
  
    ```sql
    SHOW [SESSION | GLOBAL] VARIABLES; -- 查看所有系统变量
    SELECT @@[SESSION | GLOBAL] 系统变量名; -- 查看指定变量的值
    SET [SESSION | GLOBAL] 系统变量名 = 值; -- 设置系统变量
    SET @@[SESSION | GLOBAL] 系统变量名 = 值; -- 设置系统变量
    ```
    
  - 自定义变量
  
    作用域：当前连接
  
    ```sql
    SET @var_name  = expr[,@var_name  = expr]...; -- 赋值
    SET @var_name  := expr[,@var_name  := expr]...; -- 赋值
    SELECT @var_name  = expr[,@var_name  = expr]...; -- 赋值
    SELECT 字段名 INTO @var_name FROM 表名; -- 赋值
    
    SELECT @var_name; -- 使用
    ```
  
  - 局部变量
  
    作用域：BEGIN ... END块;
  
    ```sql
    DECLARE 变量名 变量类型[DEFAULT]; -- 声明
    SET 变量名 = 值; -- 赋值
    SET 变量名 := 值; -- 赋值
    SELECT 字段名 INTO 变量名 FROM 表名; -- 赋值
    ```
    
  
- 语句

  ```sql
  -- 判断
  -- if
  IF 条件1 THEN
  ...
  ELSEIF 条件2 THEN
  ELSE
  ...
  END IF;
  
  -- case
  CASE case_value
  	WHEN ... THEN ...
  	ELSE
  END CASE;
  
  -- 循环
  -- while
  WHILE 条件 DO
  	... sql
  END WHILE;
  
  -- repeat
  REPEAT
  	... sql
  	UNTIL 条件
  END REPEAT;
  
  -- loop
  [begin_label:]LOOP
  	...sql
  END LOOP [end_label];
  
  -- 游标
  DECLARE 游标名称 CURSOR FOR 查询语句; -- 声明游标
  OPEN 游标名称; -- 打开游标
  FETCH 游标名称 INTO 变量[,变量]; -- 获取游标记录
  
  -- handler
  DECLARE handler_action HANDLER FOR condition_value[,condition_value]... statement; 
  -- handler_action
  	CONTINUE; -- 继续执行程序
  	EXIT; -- 终止程序
  -- condition_value
  	SQLSTATE sqlstate_value; -- 状态码
  	SQLWARNING; -- 所有以01开头的SQLSTATE代码的缩写
  	NOT FOUND; -- 所有以02开头的SQLSTATE代码的缩写
  	SQLEXCEPTION; -- 所有没被SQLWARNING和NOT FOUND捕获
  ```

- 存储函数

  ```sql
  CREATE FUNCTION 存储函数名称([参数列表])
  RETURNS TYPE [DETERMINISTIC | NO SQL |READS SQL DATA]
  BEGIN 
  	-- SQL语句
  	RETURN ...;
  END;
  ```



## 17. 触发器

触发器是指与表有关的数据库对象，指在insert/update/delete之前或者之后，触发并执行触发器中定义的SQL集合。OLD，NEW指发生变化的记录内容

- 创建

  ```sql
  CREATE TRIGGER trigger_name
  BEFORE|AFTER INSERT|UPDATE|DELETE
  ON tb_name FOR EACH ROW -- 行级触发器
  BEGIN
  	trigger_stmt;
  END;
  ```

- 查看

  ```sql
  SHOW TRIGGERS;
  ```

 - 删除

   ```sql
   DROP TRIGGER [schema_name] trigger_name; -- 如果没有指定schema_name，默认当前数据库
   ```



## 18. 锁

计算机协调多个进程或线程并发访问某一资源的机制。

- 全局锁

  对整个数据库实例加锁，加锁后处于只读状态。后续写操作将被阻塞。

  使用场景：做全库的逻辑备份，对所有的表进行锁定，从而获取一致性视图，保证数据完整性

  ```
  fush tables with read lock; -- 加全局锁
  unlock tables; -- 释放锁
  ```

- 表级锁

  每次操作锁住整张表。锁定粒度大，发生锁冲突的概率最高，并发度最低。

  - 表锁

    加锁：lock tables 表名... read/write

    释放锁：unlock tables / 客户端连接关闭

    - 表共享读锁（read lock）：阻塞写，不阻塞其他客户端的读

    - 表独占写锁（write lock）：阻塞其他客户端的读写，不阻塞自己的读写

  - 元数据锁（meta data lock）

    系统自动控制，无需显式使用。访问表时会自动加上，维护表元数据的数据一致性。在表上有未提交的事务时，不可以对元数据进行写入操作。避免了DML和DDL的冲突，保证读写的正确性。

    增删改查时，加MDL读锁（共享），表结构变更时，加MDL写锁（排他）。

  - 意向锁

    使用场景：加表锁时需要检查每一行的行锁，为解决效率低的问题，使用意向锁减少检查。

    - 意向共享锁（IS）

      与表锁共享锁（read）兼容，与表锁排他锁（write）互斥。

    - 意向排他锁（IX）

      与表锁共享锁（read）及表锁排他锁（write）都互斥，意向锁之间不互斥
    

- 行级锁

  每次操作锁住对应表。锁定粒度小，发生锁冲突的概率最低，并发度最高。

  通过对索引上的索引项加锁来实现的，而不是对记录加的锁。

  - 行锁（record lock）

    锁定单个记录的锁，防止其他事务对此行进行update和delete。在RC、RR隔离级别下都支持。

    - 共享锁（S）：

      允许一个事物去读一行，阻止其他事务获得相同数据集的排他锁。
  
    - 排他锁（X）：
  
      允许获取排他锁的事务更新数据，阻止其他事务获得相同数据集的共享锁和排他锁。
  
  - 间隙锁（gap lock）
  
    锁定索引记录间隙（不含记录），确保索引记录间隙不变，防止其他事务insert产生幻读。在RR隔离级别下支持
  
  - 临键锁（next-key-lock）
  
    行锁和间隙锁的组合。在RR隔离级别下支持
  



## 19. SQL性能分析

 - SQL执行频率

   ```sql
   SHOW GLOBAL STATUS LIKE 'Com_______)';
   ```

 - 开启慢查询日志

​		/etc/my.cnf配置 ，配置完重启mysql

​		slow_query_log = 1 # 设置开启MYSQL慢查询日志

​		long_query_time = 2 # 设置SQL执行时间

 - profile耗时详情

   ```sql
   show profiles;
   show profile [cpu] [for query query_id]; -- 耗时情况
   ```

 - expain

   sql语句前加上explain/desc

    - select _type

       - SIMPLE 简单表，不使用表连接或者子查询

       - PRIMARY 主查询 外层的查询

       - UNION UNION中的第二个或者后面的查询语句

       - SUBQUERY SELECT / WHERE包含子查询

    - **type**

       ​	连接类型由好到差NULL（不访问表）、system（系统表）、const（主键或唯一索引）、eq_ref（唯一性索引扫描，对于每个索引键，表中只有一条记录与之匹配）、ref（非唯一性的索引）、range（使用索引返回一个范围中的行，如大于小于等情况）、index（用了索引但遍历索引树）、all（全表扫描）

	 - **possible_key** 
	
	    显示可能应用在这张表上的索引，一个或多个
	
	 - **key**
	
	    实际用到的索引，没有使用索引则为null
	
	 - **key_len**
	
	    索引中使用的字节数，索引字段最大可能长度（并非实际使用长度），长度越短越好
	
	 - rows
	
	    mysql认为必须要执行查询的行数（不准确的估计值）
	
	 - filtered
	
	    表示返回结果行数占需读取行数的百分比，值越大越好
	    
	 - extra
	
	    - using index condition; 查找使用了索引，但是需要回表查询数据
	    - using where;using index; 查找使用了索引，但是需要的数据都在索引列中能找到，不需要回表查询数据
	
	    