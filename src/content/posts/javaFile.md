---
title: java的io的一部分：File类
published: 2026-09-23
description: 仔细介绍一下File类以及使用方式
tags: [java，学习]
category: java
draft: false
---

## File类
### 1，File类的介绍
- File类是java.io包下的一个类，主要用于文件和目录的创建、删除、判断等操作。
- 而一个File 对象就表示一个路径，可以是文件的路径、也可以是文件夹的路径
- 这个路径可以是存在的，也允许是不存在的
- 由于这个类的对象都是表示路径的，因此对这个类的对象的操作都是对路径的操作，或者说是对文件或文件夹的操作，而不会影响文件里的内容。

### 2，File类的构造方法
|方法名称|说明|
|--|--|
|public File(String pathname)	|根据文件路径创建文件对象|
|public File(String parent, String child)	|根据父路径名字符串和子路径名字符串创建文件对象|
|public File(File parent, String child)	|根据父路径对应文件对象和子路径名字符串创建文件对象|
示例
```java
        //知识补充：\是转义字符，两个\才能正常表示一个\
        //public File()
        //1一个参数，直接把字符串传换成file对象
        String str="C:\\Users\\LY\\Desktop\\file.txt";
        File file=new File(str);
        System.out.println(file);

        //2,两个string参数，把两个字符串拼接然后创建file对象，前者是父路径，后者是子路径
        String Father1="C:\\Users\\LY\\Desktop";//所谓父路径就是最后一个文件所在的文件夹的位置
        String son1="file.txt";//子路径就是文件名
        File file1=new File(Father1,son1);
        System.out.println(file1);

        //3,一个File对象，一个string子路径，将两个拼接后传给新的file对象
        File file2=new File("C:\\Users\\LY\\Desktop");
        String son2="file.txt";
        File file3=new File(file2,son2);
        System.out.println(file3);
```

### 3，File类的方法
由于它有很多的方法，我这里分类去介绍
#### 1，判断文件类型
|方法名称|说明|
|--|--|
|public boolean isDirectory()	|判断此路径名表示的 File 是否为文件夹|
|public boolean isFile()	|判断此路径名表示的 File 是否为文件|
|public boolean exists()	|判断此路径名表示的 File 是否存在|

#### 2，获取文件的一些信息
|方法名称|说明|
|--|--|
|public long length()	|返回文件的大小（字节数量）|
|public String getAbsolutePath()	|返回文件的绝对路径|
|public String getPath()	|返回定义文件时使用的路径|
|public String getName()	|返回文件的名称，带后缀|
|public long lastModified()	|返回文件的最后修改时间（时间毫秒值）|

小细节：
1. 使用相对路径时，是以当前的这个项目来看相对位置的
2. length（）方法只能返回文件的大小，没办法直接返回文件夹的大小，要的话只能获得文件夹里每个文件的大小再求和。
3. getName（）方法作用于文件的时候会返回文件名加后缀，如果作用于文件夹，只会返回文件夹的名字

#### 3，创建和删除文件以及文件夹
|方法名称|说明|
|--|--|
|public boolean createNewFile()	|创建一个新的空的文件|
|public boolean mkdir()	|创建单级文件夹|
|public boolean mkdirs()	|创建多级文件夹|
|public boolean delete()	|删除文件、空文件夹|

小细节：
1. **createNewFile()**
	- 如果当前路径表示的文件是不存在的，则创建成功，方法返回true，如果当前路径表示的文件是存在的，则创建失败，方法返回false
	- 如果父级路径是不存在的，那么方法会有异常IOException
	- createNewFile方法创建的一定是文件，如果路径中不包含后缀名，则创建一个没有后缀的文件

2. **mkdir()**
	- windows当中路径是唯一的，如果当前路径已经存在，则创建失败，返回false
	- mkdir方法只能创建单级文件夹，无法创建多级文件夹。


3. **mkdirs()**
	- 既可以创建单级文件夹也可以创建多级文件夹，因此，我们基本都用它，而不会用上面那个

4. **delete()**
	- 它只能删除文件和空文件夹
	- 它的删除是直接删除的，不走回收站


#### 4,对文件的获取和遍历
|方法名称|说明|
|--|--|
|public static File[] listRoots()	|列出可用的文件系统根（获取所有盘符）|
|public String[] list()	|获取当前该路径下所有内容|
|public String[] list(FilenameFilter filter)	|利用文件名过滤器获取当前该路径下所有内容|
|public File[] listFiles()	|获取当前该路径下所有内容|
|public File[] listFiles(FileFilter filter)	|利用文件名过滤器获取当前该路径下所有内容|
|public File[] listFiles(FilenameFilter filter)	|利用文件名过滤器获取当前该路径下所有内容|

**但是唯一需要掌握的只有listFiles()方法，因为通过这个方法能实现其他的遍历方式**
|方法名称|说明|
|--|--|
|public File[] listFiles()	|获取当前该路径下所有内容|


- listFiles()小细节：
    - 当调用者File表示的路径不存在时，返回null
    - 当调用者File表示的路径是文件时，返回null
    - 当调用者File表示的路径是一个空文件夹时，返回一个长度为0的数组
    - 当调用者File表示的路径是一个有内容的文件夹时，将里面所有文件和文件夹的路径放在File数组中返回
    - 当调用者File表示的路径是一个有隐藏文件的文件夹时，将里面所有文件和文件夹的路径放在File数组中返回，包含隐藏文件
    - 当调用者File表示的路径是需要权限才能访问的文件夹时，返回null

- 示例
```java
File F1=new File("C:\\Users\\LY\\Desktop\\files");
        File[] F2=F1.listFiles();
        for(File f:F2)
        {
            if(f.getName().endsWith(".txt"))
            {
                System.out.println(f.getName());
            }
        }  
//在files文件加里有aaa（文件夹），aaa.txt，bbb（文件夹），bbb.txt
```