---
title: Servlet
published: 2026-10-27
description: 讲一下servlet的相关知识，并给出示例
tags: [java,web,servlet]
category: java/web
draft: false
---

# Servlet
## 1. 什么是servlet
是真正的做前后端交互的接口，
- 执行原理：
    - 1，当服务器接收到请求，会解析URL路径
    - 2，查web.xml,看是否有<url-pattern>标签
    - 3，有，找到对应的全类名
    - 4，把该类加载进内存，并创建对象（这一步就相当于找到程序的入口了）
    - 5，调用方法

在实现Servlet的时候，有5个可重写的方法
    - init 初始化方法，直执行一次
    - service 提供服务的方法，每次被访问都会执行一次
    - destory 销毁方法，在服务器关闭前会执行一次
    - getServletConfig() 获取ervletConfig对象
    - getServletInfo() 获取servlet的一些信息

## servlet 的底层
- 它被执行时，只会被创建一个servlet的对象，所有的访问线程都在这一个对象上，所以尽量不要写类的成员变量，会因为数据共享发生错乱。
- 那对于这个servlet对象的创建时机也是可以定义的
    - 在web.xml里，写<load-on-startup>负/正数</load-on-startup>
    (负数表示第一次访问的时候创建，正数表示开启服务器的时候就创建)

## 注解：
在servlet3.0及以上版本，不需要手动在web.xml里写类的映射名了，只需要在实现类上加@WebServlet注解就可以实现相同的功能。
- 格式： 
```java
@WebServlet(urlPattern="......")
class.....
```
**注：当这个servlet只映射一个url时，可以省略前面urlPattern，直接写"/....."映射地址**

## HttpServlet（更适合实际实现的方法）
- 首先servlet作为一个接口有5个方法，但常用的不多所以有他的继承类,如下是他们的继承关系
    - **servlet->GenericServlet->HttpServlet**

### 1，GenericServlet 抽象类
    - 把除了service以外的方法都实现了
### 2，HttpServlet 抽象类
    - 不仅对servlet的五个方法都实现了，还封装了对HTTP协议的7个方法，但实际常用的只有doGet和doPost分别对应处理HTTP的Get和Post请求

若对HTTP不熟，可跳转
https://firefly-9vy.pages.dev/posts/HTTPleran_java/