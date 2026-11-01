---
title: 网络编程和反射
published: 2026-10-21
description: 以java为基础，介绍网络编程的相关知识，并认识java的网络编程类
tags: [java,web,学习]
category: java
draft: false
---

# 网络编程
首先来了解常见的软件架构
- C/S：客户端/服务器
- B/S：浏览器/服务器

## 网络编程三要素
### 1，IP地址：设备的唯一标识（有IPV4和IPV6）
    - 实现类：InetAddress
    - 它没有构造方法，而是调用静态方法getByName（"IP"）来获取对象
**特殊IP：127.0.0.1是回送地址也叫localhost，它一定会指向本机（在底层上，他是经过网卡识别然后直接传回本机，是不过网络的）**

### 2，端口号
进程的唯一标识（0-65535）

### 3，协议：数据在网络中传输的规则
#### UDP协议
    - 面向无连接通的信协议（直接传，成不成功不管）
    - 速度快，有大小限制，但不安全
UDP实现
##### 发送
以UDP的方式发送数据步骤
    - 1，创建发送端DatagramSocket对象
    - 2，数据打包
    - 3，发送
    - 4，释放

```java
例子
//接收端的端口号
        int port = 8888;			
        //1，创建发送端DatagramSocket对象
        DatagramSocket ds = new DatagramSocket();//类比快递公司
        //设置一个数据
        String str = "hello world";
        byte[] bytes = str.getBytes();
        //2,数据打包
        DatagramPacket dp = new DatagramPacket(bytes, bytes.length, InetAddress.getByName("127.0.0.1"), port);//相当于创建一个快递包，放入数据和长度
        //3,发送
        ds.send(dp);
        //4,释放
        ds.close();
```
##### 接收
步骤
    - 1，创建接收端DatagramSocket对象
    - 2，接收数据
    - 3，解析数据
    - 4，释放

```java
例子
//1,创建接收端DatagramSocket对象
        DatagramSocket socket = new DatagramSocket(8888);//接收端的快递公司

        //接收端也要创建一个数组，用于接收数据
        byte[] bytes = new byte[1024];

        //2,接收数据
        DatagramPacket dp = new DatagramPacket(bytes, bytes.length);//这一步相当于给一个本地的快递包的信息，告诉他接收数据放在哪
        socket.receive(dp);//这一步相当于从快递公司接收数据
        //receive方法会阻塞，直到有数据到达
        //接受完数据，是放在bytes数组中的
        //3,解析数据
        String str = new String(bytes, 0, dp.getLength());
        System.out.println(str);
```
##### UDP实际有三种通信方式
- 1，单播：一对一
- 2，组播：一对多
- 3，广播：一对全

**广播地址：255.255.255.255，使用这个ip可以给局域网里所有ip广播**

- 单播就是前面的例子，
- 广播只需在前面的例子上改一个ip就行
- 这里讲讲组播:
其实大部分步骤也是一样的，只有在创建“快递公司”对象时要创建MulticastSocket对象，在接收端然后要执行joinGroup

#### TCP协议
    - 面向连接的通信协议（会确保连接成功在传输）
    - 慢，没大小限制，安全
TCP的通信实现（用Socket对象，创建io流来实现）
##### 客户端(接受端)
- 1，创建socket对象与服务端连接
    - 构造方法 socket（string host，int port）//服务端ip，输出端口号
- 2，获取输出流
- 3，释放资源

##### 服务端（发送端）
- 1，创建socket对象
    - 方法： ServerSocket（int port）//端口号要对应一致
- 2，使用accept方法监听客户端连接，返回一个socket对象
- 3，获取输入流
- 4，释放 

##### 细节
对于获取输入输出流，可以通过socket的方法
- getOutputStream()
- getInputStream()
- 可以理解为获取了在连接通道上的流（对比对本地文件的输入输出步骤，这个方法相当于省去了手动写地址的步骤）


# 反射
## 什么是反射
- 通过反射，能获得到类的所有信息
- 有时我们需要获得一个类内的成员方法，构造方法等等信息，反射就是用来解决这个问题的，他有一个最常见的应用场景，即编码时的提示框
- 反射分为获取和解剖两个维度

## Class对象
从底层上，反射是从class字节码文件里获取类的信息，因此要了解如何获取Class对象

三个获取方式，都可用
- 1，Class.forName("全类名")//全类名=包名+类名
- 2，类名.class
- 3，对象.getClass()
**小细节：这三个方法获取出来的是同一个引用地址**

## 通过Class对象获取类的内容
在获取到class对象以后就可以通过他的方法获得所有想要的信息，值得一提，他里面的getXXX方法大多是都喜欢用对应类型的数组来返回结果
    
对类的内容获取可分为三大类
### 1，对构造方法的获取
Class 类中用于获取构造方法的方法
| 方法 | 说明|
| --- | --- |
|Constructor[] getConstructors()|返回所有公共构造方法对象的数组|
|Constructor[] getDeclaredConstructors ()|返回所有构造方法对象的数组|
|Constructor<T> getConstructor(Class<?>... parameterTypes)|返回单个公共构造方法对象|
|Constructor<T> getDeclaredConstructor(Class<?>... parameterTypes)|返回单个构造方法对象|


- Constructor 类中用于创建对象的方法（用反射创建对象的方式）
    - T newInstance (Object... initargs)：根据指定的构造方法创建对象
    - setAccessible (boolean flag)：设置为 true, 表示取消访问检查

### 2，对成员变量的获取
Class 类中用于获取成员变量的方法
|方法| 说明|
| --- | --- |
|Field [] getFields ()|返回所有公共成员变量对象的数组|
|Field [] getDeclaredFields ()|返回所有成员变量对象的数组|
|Field getField (String name)|返回单个公共成员变量对象|
|Field getDeclaredField (String name)|返回单个成员变量对象|

- Field 类中用于创建对象的方法（用反射创建对象的方式）
    - void set (Object obj, Object value)：赋值
    - Object get (Object obj) 获取值。

### 3，对成员方法的获取
Class 类中用于获取成员方法的方法
|方法| 说明|
| --- | --- |
|Method [] getMethods ()|返回所有公共成员方法对象的数组，包括继承的|
|Method [] getDeclaredMethods ()|返回所有成员方法对象的数组，不包括继承的|
|Method getMethod (String name, Class... parameterTypes)|返回单个公共成员方法对象|
|Method getDeclaredMethod(String name, Class... parameterTypes)|返回单个成员方法对象|

- Method 类中用于创建对象的方法（用反射创建对象的方式）
    - Object invoke (Object obj, Object... args)：运行方法
        - 参数一：用 obj 对象调用该方法
        - 参数二：调用方法的传递的参数（如果没有就不写）
        - 返回值：方法的返回值（如果没有就不写）

# 动态代理
- 使用动态代理可以安全的给已有的方法添加功能
- 大概的实现
- 对于一个类A，它里面有写好的方法，为它做一个动态代理
    - 1，创建一个接口B，把目标方法写进B里作为抽象方法，然后在类A上实现接口B
    - 2，创建一个代理类，用Proxy.newProxyInstance创建代理对象
    - 3，在这个构造方法中传入对应的参数，然后里面的内部类就是新的执行逻辑
        