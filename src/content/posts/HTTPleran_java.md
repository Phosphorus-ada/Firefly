---
title: HTTP相关
published: 2026-10-09
description: 写了关于HTTP的相关内容
tags: [java,web,HTTP]
category: java/web
draft: false
---

# HTTP
## 特点
- 1. 基于 TCP/IP 的高级协议
- 2. 默认端口号：80
- 3. 基于请求 / 响应模型的：一次请求对应一次响应
- 4. 无状态的：每次请求之间相互独立，不能交互数据

## HTTP的消息结构
主要是做信息传输的，每次请求和响应都是一条消息，他有独特的结构
它的请求消息结构
### 1，请求行（给浏览器做解析的）
|请求方式  |请求url|请求协议/版本|
|-|-|-|
|GET      |login.html|HTTP/1.1 |

这里讲一下请求方式
它有七种方法，常用只有2种
 - GET：
    - 1. 请求参数在请求行中，在 url 后。
    - 2. 请求的 url 长度有限制的
    - 3. 不太安全
 - POST：
    - 1. 请求参数在请求体中
    - 2. 请求的 url 长度没有限制的
    - 3. 相对安全

   
### 2，请求头（浏览器给服务器内容）
都是带上的配置信息，以键值对的方式存储
### 3，请求空行
### 4，请求体（真正存数据的）


## 请求信息的获取方法
### 一，对请求行的获取
例如对于这么个请求行：GET /day14/demo1?name=zhangsan HTTP/1.1
- 方法：
    - 1. 获取请求方式 ：GET
        - String getMethod()
    - 2. 获取虚拟目录：/day14
        - String getContextPath()
    - 3. 获取 Servlet 路径：/demo1
        - String getServletPath()
    - 4. 获取 get 方式请求参数：name=zhangsan
        - String getQueryString()
    - 5. 获取请求 URI：/day14/demo1
        - String getRequestURI():		/day14/demo1
        - StringBuffer getRequestURL()  :[http://localhost/day14/demo1](http://localhost/day14/demo1)
    - 6. 获取协议及版本：HTTP/1.1
        - String getProtocol()
    - 7. 获取客户机的 IP 地址：
        - String getRemoteAddr()


### 二，对请求头的获取
| 方法|说明|
|-|-|
| (*) String getHeader (String name)| 通过请求头的名称获取请求头的值|
| Enumeration<String> getHeaderNames ()| 获取所有的请求头名称|

### 三，对请求体的获取
只有POST请求方式，才有请求体，在请求体中封装了POST请求的参数
- 步骤：（这个方式可以获取所有的数据）
    - 1，获取流对象：
    - 2，再从流对象获取数据

### 其他常用的数据类型的获取的方法
#### 1，获取请求参数的通用方式（get和post都可用），步骤
- 1，String getParameter(String name):根据参数名称获取参数值
- 2，String[] getParameterValues(String name):根据参数名称获取参数值数组
- 3，Enumeration<String> getParameterNames();  获取所有参数名
- 4，Map<String,String[]> getParameterMap();   获取所有参数的map集合
		
**乱码问题：在post方法里，中文可能出乱码，要在方法里加一个：request.setCharacterEncoding("utf-8")**

#### 2，请求转发：一种在服务器内部的一种资源跳转的方式，
- 步骤：
	- 1. 通过 request 对象获取请求转发器对象：RequestDispatcher getRequestDispatcher (String path)
	- 2. 使用 RequestDispatcher 对象来进行转发：forward (ServletRequest request, ServletResponse response)
    - 特点：
		- 1，虽然做了转发，但是浏览器的地址是不会变的
		- 2，而这个转发只能转发到服务器的内部资源里
		- 3，转发是一次请求

#### 3，共享数据：
- 域对象：一个有作用范围的对象，在范围内可以共享数据
	- 包含三个域对象
		- request域对象：代表一次请求的范围，一般用于请求转发的多个资源中共享数据
		- session域对象：同一个浏览器用户的多次请求共享，每个浏览器单独一个域，适合那种做用户数据的
		- ServletContext域对象：作用域为整个web项目，所有用户都用同一个，适合做那种公告数据
	- 方法：（可以把这个Attribute理解为一个键值对集合）
		- 1. void setAttribute (String name,Object obj): 存储数据，参数一是键，参数二是值
		- 2. Object getAttribute (String name): 通过键获取值
		- 3. void removeAttribute (String name): 通过键移除键值对

#### 4，获取ServletContext:
- ServletContext getServletContext(): getServletContext()

