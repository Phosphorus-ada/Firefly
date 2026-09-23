---
title: java的异常处理
published: 2026-09-23
description: 介绍什么是异常，常见的异常处理有哪些
tags: [java，学习]
category: java
draft: false
---

## 异常
### 1. 什么是异常
在编写代码的时候难免会出现各种各样的异常情况，我们希望能对这些异常进行处理，而不是让程序崩溃，因此java提供了异常机制。

![异常系列图](./images/异常体系图.png)


看这张图，error是底层的问题，我们只看Exception

- Exception: 叫做异常，代表程序可能出现的问题。我们通常会用 Exception 以及他的子类来封装程序出现的问题。
- 运行时异常：RuntimeException 及其子类，编译阶段不会出现异常提醒。运行时出现的异常（如：数组索引越界异常）
- 编译时异常：编译阶段就会出现异常提醒的。（如：日期解析异常）

### 2，异常的作用
1. 查bug
2. 可以作为特殊返回值，提示哪里出问题了（使用throw）

### 3，异常的处理
程序出现异常的处理方法有两种：
#### 1，JVM默认处理：
会把异常信息输出在控制台，并且会在出异常的地方停止运行
#### 2，try...catch...自定义处理
格式：
```java
try{可能出现异常的代码
}catch(异常类名 变量名){
出现对应异常后要执行的代码
}
```
##### 运作的方式:
在try代码块内出现异常后会跳出try，然后在catch里找对应的异常，然后再执行对应的catch内的代码，然后会接着执行后面的内容，这也是try...catch最重要的功能：能在处理异常的同时不让程序停下来。

##### 小细节：
1. 通常一个try语句块里可能不止会出现以一种异常类，所以我们可以使用多个catch去捕获，但是他只会进入其中一个，并且是按顺序往下找的，因此，当这些异常类有父子关系的话，要把父类往后面放
2. 另外如果catch的异常检测没有命中，那就会使用JVM的默认异常处理方式


### 4，异常处理中的常见方法
|方法名称|说明|
|--|--|
|public String getMessage()	|返回此 throwable 的详细消息字符串|
|public String toString()	|返回此可抛出的简短描述|
|public void printStackTrace()	|把异常的错误信息输出在控制台|

- 前面两种都只会展示信息的一部分，而且会终止程序运行
- 只有printStackTrace()，能把信息给全，而且不会终止程序运行，所以这也是最常用的

示例
```java
int[] arr=new int[5];
        try{
            arr[10]=10;
        }catch(ArrayIndexOutOfBoundsException e){
            e.printStackTrace();
        }

        System.out.println("后面的代码有没有运行");
```

### 5，抛出异常处理（throws与throw）
1. throws（编译时异常要写，运行时异常不用）
写在方法定义处，表示声明方法内可能出现那些异常

格式
**public void method1()throws 异常类名1，异常类名2{...}**
2. throw
写在方法内，手动抛出异常对象，会结束方法（有点类似return）

### 6，自定义异常
自定义异常（四步）
1. 定义异常类
2. 写继承（运行时异常，继承RuntimeExcetion;编译时异常，继承Exception）
3. 空参构造
4. 带参构造
- 具体不需要记，IDEA用Alt+insert自动生成构造函数，但是只选一个无参的，和一个带string参数的，带string参数的表示传入报错的信息

对抛出异常和自定义异常结合使用的示例
创建了两个异常类
```java
//对于自己想要的条件想返回异常的，但却没有java提供对应方法的，我们可以自定义异常类
//比如我这里想让添加的Girl类名字长度在3-10，年龄在18-40的，可以这么写
//先定义两个异常类
public class AgeOutOfRange extends RuntimeException {
    public AgeOutOfRange(String message) {
        super(message);
    }

    public AgeOutOfRange() {
    }
}

public class NameFormatException extends RuntimeException {
    public NameFormatException() {
    }

    public NameFormatException(String message) {
        super(message);
    }
}
```

测试类实现try...catch...调用
```java
//示例（girl类只有name和age两个属性）
public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        Girl a=new Girl();
        while(true){
            try {
                System.out.println("请输入名字和年龄");
                String name = sc.nextLine();
                int age = Integer.parseInt(sc.nextLine()) ;
                a = creatGirl(name,age);
                break;
            } catch (AgeOutOfRange e) {
                System.out.println("年龄不匹配，重新输入");
                e.printStackTrace();
            }catch (NameFormatException e) {
                System.out.println("名字不匹配，请重新输入");
                e.printStackTrace();
            }
        }
        System.out.println(a);

    }
    //添加girl写一个方法，使用RuntimeExceptio异常
    public static Girl creatGirl(String name, int age) throws RuntimeException{
        if(name.length()<3||name.length()>10){
            throw new AgeOutOfRange(name+"超出了范围");
        }
        else if(age<18||age>40){
            throw new NameFormatException(age+"超出了范围");
        }
        else {
            return new Girl(name,age);
        }
    }
```
