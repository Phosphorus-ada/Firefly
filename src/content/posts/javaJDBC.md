---
title: java的JDBC连接数据库
published: 2026-10-27
description: 讲一下java的JDBC连接数据库的相关知识，并给出示例
tags: [java,web]
category: java/数据库
draft: false
---

# JDBC
其实是套通过java来操作数据的API工具
,因而需要提前导入对应数据库的jar包，也可以通过maven来加

## 步骤

- 1，导入jar包
- 2，注册驱动
    Class.forName("数据库包的Driver")//本质创建一个class对象，这里相当于加载进内存，而在Driver类内的静态代码块才真正地创建了驱动
- 3，获取连接  
        Connection conn=DriverManager.getConnection(url,user,password)
        url:数据库地址
        user:用户名
        password:密码
- 4，定义sql语句
- 5，从连接中（相当于那个快递（相当于那个快递员）
        conn.createStatement()
- 6，执行sql语句,并且会返回收影响的行数（快递员送sql语句到数据库并执行）
- 7，处理结果
- 8，释放资源

## Connection的相关API

- 1，获取执行SQL的对象
    - 普通执行 SQL 对象
        - Statement createStatement()
    - 预编译 SQL 的执行 SQL 对象：防止 SQL 注入
        - PreparedStatement prepareStatement (sql)
    - 执行存储过程的对象
        - CallableStatement prepareCall (sql)

- 2，事务管理
    - 在连接的对象上做数据库事务的开启，提交，回滚
    - 开启事务：
        - conn.setAutoCommit(false) //关闭自动提交，相当于要手动做提交，所以叫开启事务
    - 提交：
        - conn.commit()//手动提交
        - conn.rollback()//手动回滚

## Statement
- 是那个执行sql的快递员，java提供不同方法分别执行DDL，DML，DQL

|方法|说明|
|---|--- |
|int executeUpdate(sql语句) |执行DML,DDL返回影响行数|
|ResultSet executeQuery(sql语句) |执行DQL，返回ResultSet集合对象

- 知识补充：ResultSet:一个封装了DQL查询结果的集合，所以它也有查询结果的方法
    |方法|说明|
    |---|--- |
    | boolean next() |迭代器下移，若下移后没有数据返回false|
    | XXX getXXX(参数) |参数可选：1，int：找列的编号，从1开始。2，String：找列的名称|
- 使用例子

    ```java
        while(res.next()){
        	res.getXXX("name")//找名为name的列}
    ```

## PreparedStatement(继承于statement)
- 这个类能在父类statement的基础上，加了防注入功能，具体就是能在写sql时用？去做占位符，并能将敏感字符转义。所以这也是我们写接口真正会用的方式

- 此外他还有一个能预编译的功能，可以提高性能，但需要手动开启：将&useServerPrepStmts=true放在url后面
- 实现步骤
    - 1，写sql语句，在传数据的地方用占位符
    - 2，创建：通过Connertion获得并传入sql
        PreparedStatement pstm = conn.prepareStatement(sql)
    - 3,设置参数：
        - pstm.setXXX(第几个"?"，从1开始数，传入数据)
        - 说明：1，第几个"?"，从1开始数，2，XXX传入数据的类型
    - 4,执行更新或者查询
        - pstm.executeUpdate()//增删改
        - pstm.executeQuery()//查询

## 数据库连接池
- 就像线程池一样，给了一个中间层，能实现资源的复用提高性能，并且能防止数据连接遗漏
- 标准接口：DateSource
- 一个第三方jar作为实现类：Druid
- 不展开了，有需要的时候再去了解


