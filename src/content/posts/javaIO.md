---
title: java的io
published: 2026-09-23
description: 这里将详细介绍java的io操作，这更多是对文件内容的操作
tags: [java，学习]
category: java
draft: false
---

## 前提紧要
这里的io流操作都是基于文件的，所以对于file文件的处理的相关知识一定要了解，如果有些忘记了，建议先去复习一下。

在https://firefly-9vy.pages.dev/posts/javafile/里有详细的关于file文件流的使用方式。

## IO流
### IO总览
它是用于读写文件中的数据的一种机制。

分类：
- 在方向上：
  - 输入流
  - 输出流
- 在数据类型上：
  - 字节流（可操作所有文件，适合拷贝文件）
  - 字符流（只能操作纯文本文件，适合读写数据）

注：这里的所有输入输出都是相对这个程序而言的
也就是说，输出是把内容从程序里输出到文件，输入是从文件的到数据到程序

以下是他的体系图：
![IO流系列图](./images/IO流体系图.png)

在接下来，我们会按照字节流和字符流来介绍，然后每个流都有输入和输出流

### 字节流
体系图
![字节流系列图](./images/字节流体系图.png)

#### 字节输出流（FileOutputStream）
- 步骤：
    1. 创建对象（FileOutputStream fos=new FileOutputStream("test.txt");）
    2. 写出数据（.write()）

    这write方法有三个重载版本

    |方法名称|说明|
    |--|--|
    |void write(int b)	|一次写一个字节数据|
    |void write(byte[] b)	|一次写一个字节数组数据|
    |void write(byte[] b, int off, int len)	|一次写一个字节数组的部分数据|
    3. 释放（.close()）

- 小细节：
    - 一，创建对象：
        1. 参数可以是字符串或者file对象
        2. 如果文件地址不存在，会新建这个文件，但是要保证父地址是完整的
        3. 如果文件本身有内容，会被覆盖
    - 二，写出数据：
        1. write方法虽然传入的是整数，但实际上是整数在ASCII上对应的字符
        2. 在文件里换行：使用“\r\n”。
        3. 续写：在创建对象的时候，参数多加一个true，表示续写开关，默认是false


#### 字节输入流（FileInputStream）
- 步骤：
    1. 创建对象
    2. 读取数据（.read）

    有两个重载

    |方法名称|说明|
    |--|--|
    |int read()	|一次读一个字节数据|
    |public int read(byte[] buffer)	|一次读一个字节数组数据（用于大文件的读取）|
    3. 释放（.close()）

- 小细节：
    - 一，创建对象：
        1. 文件地址不存在会直接保存
    - 二，读取数据
        1. 一次读取一个字节，读出来返回的是数据的ASCII对应数字
        2. 读到文件末尾以后会返回-1

#### 应用示例
那么循环读取就可以这么写
```java
    FileInputStream fis=new FileInputStream("test.txt");
    FileOutputStream fos=new FileOutputStream("test2.txt");
    int i=0;
    while((i=fis.read())!=-1){
        fos.write(i);
    }
    fos.close();
    fis.close();
```
结合使用read的重载，read(byte[] buffer)，可以一次性读多个数据，提高效率

它的运行方式是，每次把读到的数据放在传入的数组里，直到数组填满，同时会返回本次读取的长度int length
例如下例，把test1的数据复制到test2
```java
public static void main(String[] args) throws IOException {
        FileOutputStream fos=new FileOutputStream("test1.txt");
        FileInputStream fis=new FileInputStream("test2.txt");
        byte[] b=new byte[1024];
        int len;
        while((len=fis.read(b))!=-1){
            fos.write(b,0,len);
        }
        fos.close();
        fis.close();
```

### 知识补充：字符集
- 我们用的都是GBK字符集：他的汉字存两个字节，英文存一个字节
- 另外我们也常用Unicode字符集
    - 他有UTF-16，UTF-32等编码方式，分别表示统一用16位/32位去保存字符
    - 而UTF-8才最常用，他是用1-4个字节动态保存字符的：中文他用3个字节，英文用1个字节


### 知识补充：乱码
- 产生原因：
    1. 读取数据时没有读完整个汉字
    2. 编码和解码的方式不一致

### 知识补充：编码和解码的方法
- 编码方法

    就是把数据转成字节数组

    |方法名称|说明|
    |--|--|
    |public byte[] getBytes()	|使用默认方式进行编码|
    |public byte[] getBytes(String charsetName)	|使用指定方式进行编码|

- 解码方法

    就是把字节数组还原成数组，因此是构造方法

    |方法名称|说明|
    |--|--|
    |String(byte[] bytes)	|使用默认方式进行解码|
    |String(byte[] bytes, String charsetName)	|使用指定方式进行解码|

### 字符流
本质是字节流加字符集

特点：
    1. 字符输入流（reader）：一次读一个字节，遇到中文时，一次读多个字节
    2. 字符输出流（writer）：底层会把数据按照指定的编码方式进行编码，变成字节再写到文件中

**参考字节流对实现类命名规律，这里同样是“针对对象+执行方式的格式”例如最常见的对纯文本的字符输入流是File+Reader**

#### 字符输入流（FileReader）
- 步骤：
1. 创建字符输入流对象
    |方法名称|说明|
    |--|--|
    |public FileReader(File file)	|创建字符输入流关联本地文件|
    |public FileReader(String pathname)	|创建字符输入流关联本地文件|
     - 细节：
         - 文件地址不存在会报错 
2. 读取数据（read）
    |方法名称|说明|
    |--|--|
    |public int read()	|读取数据，读到末尾返回 - 1|
    |public int read(char[] buffer)	|读取多个数据，读到末尾返回 - 1|
    - 细节：
        - 1：按字节进行读取，遇到中文，一次读多个字节，读取后解码成十进制，如果要以中文汉字或者英文的形式呈现，只需要做一个char类型的强转，因为char类型正好
        - 2：读到文件末尾了，read 方法返回 - 1。
    示例：

    ```java
    File f =new File("C:\\Users\\LY\\Desktop\\files\\aaa.txt");
        FileReader fr=new FileReader(f);
        int i=0;
        while((i=fr.read())!=-1){
            System.out.print((char) i);
        }
    ```
    - 对于有参read（char[] buffer）:
        把读取，解码，强转集合起来了，并把结果放进了buffer数组中，所以我们想要结果只要遍历数组（给的数组足够大，或者文件足够小）或者把数组用string类型拼接。

3. 释放

#### 字符输出流（FileWriter）
步骤
1. 创建字符输出流对象
    |方法名称|说明|
    |--|--|
    |public FileWriter(File file)	|创建字符输出流关联本地文件，默认覆盖写入|
    |public FileWriter(String pathname)	|创建字符输出流关联本地文件，默认覆盖写入|
    |public FileWriter(File file, boolean append)	|创建字符输出流关联本地文件，append=true 开启续写|
    |public FileWriter(String pathname, boolean append)	|创建字符输出流关联本地文件，append=true 开启续写|

    - 细节：
        - 1：参数是字符串表示的路径或者 File 对象都是可以的
        - 2：如果文件不存在会创建一个新的文件，但是要保证父级路径是存在的
        - 3：如果文件已经存在，则会清空文件，如果不想清空可以打开续写开关
2. 写出数据
    |方法名称|说明|
    |--|--|
    |void write(int c)	|写出一个字符|
    |void write(String str)	|写出一个字符串|
    |void write(String str, int off, int len)	|写出一个字符串的一部分|
    |void write(char[] cbuf)	|写出一个字符数组|
    |void write(char[] cbuf, int off, int len)	|写出字符数组的一部分（包括下标）|

    - 细节：
        - 1：如果write方法的参数是整数，但是实际上写到本地文件中的是整数在字符集上对应的字符。
3. 释放


### 字符流底层
- 字符流的输出和输入都存在一个缓冲区，这个缓冲区本质是一个8192字节长的数组。
- 在用read输入的时候，他会先把数据放进缓冲区里，然后再从缓冲区里读取数据，若读取的数据大于8192字节，在超过的时候会将剩余数据覆盖进缓冲区，再去读取数据
- 在用write输出的时候，也会把要输出的数据先放进缓冲区里，然后在执行.close()或者.flush()的时候才把数据真正的写入文件，（这个flush相当于刷新，它不会关闭通道，能继续写入），而如果写入的数据超过了8192字节，他会先把存满缓冲区的数据存进本地文件，然后多出来的数据覆盖缓冲区，直到.close（）
