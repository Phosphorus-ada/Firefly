---
title: java的线程机制与锁
published: 2026-10-17
description: 介绍java的多线程机制，并且给出示例练习
tags: [java,学习]
category: java
draft: false
---

# 线程
## 线程的概念
- 是操作系统能调度的最小单位，被包在进程中
- 而在日常的使用中，应当理解为这是可用代码开启关闭的一种功能，实现一些同时进行的功能

## 并发和并行
- 并发：在同一时刻，多个指令在同一个CPU上交替执行
- 并行：在同一时刻，多个指令在多个CPU上同时执行

## 多线程的实现
### 1，继承Thread，重写run方法
- 步骤：
    - 1 .在一个新类上继承Thread
    - 2 .重写run方法
    - 3 .创建子类对象
    - 4 .用start方法启动线程


```java
示例
public class ThreadTry extends Thread {
    @Override
    public void run() {
        System.out.println("开了新线程");
        for(int i=0;i<10;i++){
            System.out.println("线程"+Thread.currentThread().getName()+"在运行"+i);
        }
    }
}

 public static void main(String[] args) {
        ThreadTry threadTry=new ThreadTry();
        threadTry.start();
        System.out.println("主线程在运行");
        for(int i=0;i<10;i++){
            System.out.println("主线程在运行"+i);

/*结果
主线程在运行
开了新线程
主线程在运行0
主线程在运行1
主线程在运行2
主线程在运行3
主线程在运行4
主线程在运行5
主线程在运行6
主线程在运行7
主线程在运行8
主线程在运行9
线程Thread-0在运行0
线程Thread-0在运行1
线程Thread-0在运行2
线程Thread-0在运行3
线程Thread-0在运行4
线程Thread-0在运行5
线程Thread-0在运行6
线程Thread-0在运行7
线程Thread-0在运行8
线程Thread-0在运行9
*/
        }
    }
```

### 2，实现Runnable接口
- 步骤：
  - 1 .在一个新的类上实现Runnable接口
  - 2 .实现run方法
  - 3 .创建该类对象
  - 4 .创建一个Thread对象并传入该类对象（这里可以把类对象理解为实现方法，真正的启动对象是Thread对象）然后用start方法启动。

```java
示例
实现类
public class ThreadTry2 implements Runnable {
    //步骤，实现runnable接口，重写run方法
    @Override
    public void run() {
        System.out.println("开了新线程");
        for(int i=0;i<5;i++){
            System.out.println("线程"+Thread.currentThread().getName()+"在运行"+i);
        }
    }
}
测试类
public static void main(String[] args) {
        ThreadTry2 threadTry2=new ThreadTry2();
        //把对象传递给thread类的构造方法
        Thread thread=new Thread(threadTry2);
        //调用start方法，启动线程
        thread.start();
        System.out.println("主线程在运行");
        for(int i=0;i<5;i++){
            System.out.println("主线程在运行"+i);
        }
    }
/*结果
主线程在运行
开了新线程
主线程在运行0
主线程在运行1
主线程在运行2
主线程在运行3
主线程在运行4
线程Thread-0在运行0
线程Thread-0在运行1
线程Thread-0在运行2
线程Thread-0在运行3
线程Thread-0在运行4

*/
```

### 3，利用Callabel和FutureTask接口方法实现
这个方式主要是可以获取到多线程运行结果
- 步骤
  - 1创建一个MyCallable类实现Callable接口
  - 2重写call方法（可设定返回值）
  - 3创建MyCallable对象mc
  - 4创建FutureTask对象ft，并且传入mc（这个对象用来承接返回值）
  - 5创建Thread类对象，并且传入ft，start启动线程

```java
  示例
实现类
public class MyCallable implements Callable<String> {//注意这里就可以设置返回的泛型了
    @Override
    public String call() throws Exception {
        for(int i=0;i<5;i++){
            System.out.println("线程"+Thread.currentThread().getName()+"在运行");
        }
        return "hello world";
    }
}
测试类
public static void main(String[] args) {
        MyCallable mc=new MyCallable();
        //把Callable对象传递给FutureTask的构造方法
        FutureTask ft=new FutureTask(mc);
        Thread th=new Thread(ft);
        th.start();
        for (int i = 0; i < 5; i++) {
            System.out.println("主线程在运行"+i);
        }
        try {
            System.out.println(ft.get());//获取线程执行的结果
        } catch (InterruptedException e) {
            throw new RuntimeException(e);
        } catch (ExecutionException e) {
            throw new RuntimeException(e);
        }
    }
/*结果
主线程在运行0
主线程在运行1
主线程在运行2
主线程在运行3
主线程在运行4
线程Thread-0在运行
线程Thread-0在运行
线程Thread-0在运行
线程Thread-0在运行
线程Thread-0在运行
hello world
*/
```

## Thread成员方法
|方法名称|	说明|
|--|--|
|String getName()|	返回此线程的名称|
|void setName(String name)|	设置线程的名字（构造方法也可以设置名字）|
|static Thread currentThread()|	获取当前线程的对象|
|static void sleep(long time)|	让线程休眠指定的时间，单位为毫秒|
|setPriority(int newPriority)|	设置线程的优先级|
|final int getPriority()|	获取线程的优先级|
|final void setDaemon(boolean on)|	设置为守护线程（会在其他非守护线程全部关闭以后，陆续地关闭）|
|public static void yield()|	出让线程 / 礼让线程（会让调用这个方法的线程让出cpu的使用，但是又会立刻开始尝试抢夺cpu运行）|
|public static void join()|	插入线程 / 插队线程（让调用这个方法的线程插在当前运行的线程前）|

### 方法上的细节
- 1，String getName() 返回此线程的名称
- void setName(String name) 设置线程的名字（构造方法也可以设置名字）
    - 细节：
        - 1、如果我们没有给线程设置名字，线程也是有默认的名字的,
        格式：Thread-X（X序号，从0开始的）
        - 2、如果我们要给线程设置名字，可以用set方法进行设置，也可以构造方法设置

- 2,static Thread currentThread() 获取当前线程的对象
    - 细节：
        当JVM虚拟机启动之后，会自动的启动多条线程,
        其中有一条线程就叫做main线程,
        他的作用就是去调用main方法，并执行里面的代码,
        在以前，我们写的所有的代码，其实都是运行在main线程当中

- 3,static void sleep(long time)    让线程休眠指定的时间，单位为毫秒
   - 细节：
        - 1、哪条线程执行到这个方法，那么哪条线程就会在这里停留对应的时间
        - 2、方法的参数：就表示睡眠的时间，单位毫秒
            (1 秒= 1000毫秒)
        - 3、当时间到了之后，线程会自动的醒来，继续执行下面的其他代码

## 锁（synchronized）
有时，几个线程操作同一个代码块的时候，会因为阻塞等问题发生线程抢占问题，此时我们希望几个线程能先后的使用，那么锁的作用就发挥出来了
### 锁的实现（三种）

- 1，同步代码块
    - 格式：synchronized（锁对象）{......}
    - 细节：这里的锁对象可以是任何类，我们甚至可以new一个Object对象放进去，但要保证锁对象用的对，不然一人一个不同的锁，谁都能进，那锁就没意义了

- 2，如果想给某个方法上锁，可以直接加synchronized关键字
    - 格式：public synchronized void test()

- 3，使用锁对象（Lock）手动上锁解锁
    - 首先要明确，Lock是接口，我们用他的实现类ReentrantLocks

    |相关方法|说明|
    |--|--|
    |void lock() 	|上锁|
    |void unlock() 	|解锁|

### 死锁
他是一种错误，大概是说，两个线程因为相互嵌套导致了A等B解锁，B又在等A解锁的情况而让程序卡死在这。总之记住**别让两个锁相互嵌套**就可以避免

### 等待唤醒机制
相关方法
|方法|说明|
|--|--|
|wait()		|让当前线程等待，并且会释放所占用的锁|
|notify()		|随机唤醒单个线程（这个唤醒的范围是指停在这个锁前的所有线程）|
|notifyAll()	|唤醒所有线程|

**这几个方法都应使用在同步代码块内，并且传入同步代码块的锁对象还得一样，并且以传入同步代码块的锁对象来调用才符合逻辑**
```java
例如   
Object lock = new Object();
synchronized(lock){
    lock.wait(); // 当前线程释放lock锁，等待
}
synchronized(lock){
    lock.notify(); // 唤醒等待lock的线程
}
```
- 特别示例：而在类似消费者和生产者这种示例关系中，需要对wait的执行做条件判断的，建议用while包裹wait，这样可以保证一定做一次条件判断，这样不管用notify()还是notifyAll()都不会有问题，也能规避一些神秘bug
例如
```java
 // 消费者
    static class Consumer implements Runnable {
        @Override
        public void run() {
            for (int i = 0; i < 10; i++) {
                synchronized (lock) {
                    // 队列空，等待生产者生产
                    while (count <= 0) {                                                                
                        try {                                               // 这一块用if是这样
                            System.out.println("【消费者】队列空，wait等待"); //if(count<=0){
                            lock.wait();                                    //Lock.wait();}
                        } catch (InterruptedException e) {                      
                            Thread.currentThread().interrupt();                 
                        }                                                       
                    }

                    count--;
                    System.out.println("【消费者】消费，当前数量：" + count);
                    lock.notifyAll(); // 唤醒
                }
```
- 依照生产者生产食物，消费者吃，桌子上最多两份来写
完整例子联系
```java
public class ComsumerThread extends Thread {
    //消费者线程
    @Override
    public void run() {
        /*
        1，循环
        2，写同步代码块
        3，判断是否到了末尾（到了，退出）
        4，判断是否到了末尾（没到，继续消费）
        */
        while(true){
            synchronized (test1.lock){
                //判断是否到了末尾，即剩下的食物数量为0了
                if(test1.maxFoodCount<=0&&test1.foodCount<=0){
                    break;
                }else {//没到末尾执行
                    //但是我们要看桌子上有没有食物
                    //没有，等待
                    while(test1.foodCount<=0){
                        try {
                            System.out.println("桌子上没有食物，消费者等待");
                            test1.lock.wait();

                        } catch (InterruptedException e) {
                            throw new RuntimeException(e);
                        }
                    }
                    //有，吃
                    test1.foodCount--;
                    System.out.println("消费者吃了一份食物，桌子上还剩"+test1.foodCount+"份");
                    //吃完叫醒生产者
                    test1.lock.notify();
                    //给哦一个睡眠，尽可能做到做一吃一
                    try {
                        Thread.sleep(200);
                    } catch (InterruptedException e) {
                        throw new RuntimeException(e);
                    }
                }
            }
        }
    }
}
//=============生产者线程=====================
public class ProducerThread extends Thread{
    @Override
    public void run() {
        //生产者线程
        /*
         * 1，循环
         * 2，写同步代码块
         * 3，判断是否到了末尾（到了，退出）
         * 4，判断是否到了末尾（没到，继续生产）
         */
        while (true) {
            synchronized (test1.lock) {
                //判断是否到了末尾（到了，退出）
                if (test1.maxFoodCount<=0) {
                    break;
                }
                while (test1.foodCount>=2) {//最多放两份食物
                    try {
                        System.out.println("桌子上有两份了，生产者等待");
                        test1.lock.wait();

                    } catch (InterruptedException e) {
                        throw new RuntimeException(e);
                    }
                }
                //桌子上不足两份，做一份
                test1.foodCount++;
                test1.maxFoodCount--;
                System.out.println("生产者生产了一份食物，桌子上有"+test1.foodCount+"份，总还剩"+test1.maxFoodCount+"份");
                test1.lock.notify();//唤醒消费者
                //给点时间
                try {
                    Thread.sleep(200);
                } catch (InterruptedException e) {
                    throw new RuntimeException(e);
                }
            }
        }
    }
}
//=============测试类=====================
public class test1 {
    //运行的时候要有固定的锁对象
    static Object lock = new Object();
    //要有一标识，表示桌子上的食物有几个
    static int foodCount = 0;
    //还要有一个标识，表示最多上几份，这里就表示还剩几份没上
    static int maxFoodCount = 10;

    public static void main(String[] args) {
        //启动两线程
        ComsumerThread comsumerThread = new ComsumerThread();
        ProducerThread producerThread = new ProducerThread();
        comsumerThread.start();
        producerThread.start();
    }
}
```




