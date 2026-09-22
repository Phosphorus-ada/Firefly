---
title: java stream流
published: 2026-09-22 
description: 详细介绍stream流的使用
tags: [java，学习]
category: java
draft: false
---

## stream流介绍
可以想象成一个流水线，过程中使用api实现过滤，转换，统计，打印等

整体使用步骤
- 1，得到一条stream流，放入数据
- 2，使用中间方法对数据做操作
- 3，使用终结方法对数据做操作

一般我们在要对集合，数组，或者一堆数据类型一致的零散数据做过滤，类型转换，统计等操作时，就可以使用stream流

## stream流的使用
### 1，得到一条stream流，放入数据
创建方式

| 获取方式 | 方法名 | 说明 |
| ---- | ---- | ---- |
| 单列集合 | default Stream<E> stream() | Collection中的默认方法 |
| 双列集合 | 无 | 无法直接使用stream流 |
| 数组 | public static <T> Stream<T> stream(T[] array) | Arrays工具类中的静态方法 |
| 一堆零散数据 | public static<T> Stream<T> of(T... values) | Stream接口中的静态方法 |

#### 单列集合创键示例
```java
ArrayList<Integer> list = new ArrayList<>();
        Collections.addAll(list,1,2,3,4,5);
        Stream<Integer> stream = list.stream();
```  
#### 双列集合的示例
```java
//双列集合无法直接使用，但是可以通过entrySet()和keySet()方法获取set集合以后再用单列集合的方式去获取stream流
HashMap<Integer,Integer> map = new HashMap<>();
        map.put(1,1);
        map.put(2,2);
        map.put(3,3);
        map.put(4,4);
        map.put(5,5);
//用keyset获取set集合
        Set<Integer> set = map.keySet();
        set.stream().forEach(s->{System.out.print(s);
        System.out.println(map.get(s));
        });
//用entryset获取
        Set<Map.Entry<Integer, Integer>> set1 = map.entrySet();
        set1.stream().forEach(s->{System.out.println(s);
//            System.out.println(s.getKey());
//            System.out.println(s.getValue());

        });
```
#### 数组的示例
```java
int []arr1 = {1,2,3,4,5};
String[] arr2 = {"a","b","c","d","e"};
Arrays.stream(arr1).forEach(s->{System.out.print(s);});
```
#### 零散数据的stream流创建以及示例

使用stream静态方法of，从底层上看，这个方法传入的是可变参数，又因为可变参数的底层是数组，所以理论上是可以传入数组的，但是它不会自动解包，所以当传入基础数据类型的数组的时候不会读取里面的数据，而是读取数组的地址
```java
Stream.of(1,2,3,4,5).forEach(s->{System.out.print(s);});
```

### 2，使用中间方法对数据做操作
| 名称 | 说明 |
| ---- | ---- |
| Stream<T> filter(Predicate<? super T> predicate) | 过滤 |
| Stream<T> limit(long maxSize) | 获取前几个元素 |
| Stream<T> skip(long n) | 跳过前几个元素 |
| Stream<T> distinct() | 元素去重，依赖(hashCode和equals方法) |
| static <T> Stream<T> concat(Stream a, Stream b) | 合并a和b两个流为一个流 |
| Stream<R> map(Function<T , R> mapper) | 转换流中的数据类型 |

#### filter过滤
这个方法里面的内部类是用于设定过滤方式的，重写里面的test方法，return true表示保留，return false表示过滤
```java

Stream.of(1,2,3,4,5).filter(new Predicate<Integer>() {
    @Override
    public boolean test(Integer integer) {
        return integer%2==0;
    }
}).forEach(s->{System.out.print(s);});
//可以替换成lambda表达式
Stream.of(1,2,3,4,5).filter(integer->integer%2==0).forEach(s->{System.out.print(s);});
```

#### map转换流中的数据类型
这个方法里面的内部类是用于设定转换方式的，重写里面的apply方法，return转换后的数据
```java
Stream.of(1,2,3,4,5).map(new Function<Integer, Integer>() {//这里的前者Integer是输入的类型，后者Integer是输出的类型
    @Override
    public Integer apply(Integer integer) {
        return integer*2;
    }
}).forEach(s->{System.out.print(s);});
```
我们使用这个方法的时候可以更加灵活，它不仅字面上的做数据类型转换，还可以根据需要做数据的选取，过滤，拼接等，因此它可以转换成自定义的类型。

#### 小细节：
- 1，stream的中间方法每次都会返回一个新的流，且用过方法的流不能再使用第二次，所以我们使用链式编程
- 2，stream流不会影响原来的数组和集合

### 3，使用终结方法对数据做操作
（之所以叫作终结方法，就是因为这些方法没有返回值，不能继续链式调用其他方法）
| 名称 | 说明 |
| ---- | ---- |
| void forEach(Consumer action)	|遍历|
| long count()	|统计|
| T[] toArray(IntFunction<T[]> generator)	|收集流中的数据，放到数组中|
| collect(Collector collector)	|收集流中的数据，放到集合中|

#### forEach遍历
```java
ArrayList arr1=new ArrayList();
        Collections.addAll(arr1,1,2,3,4,5,6);;
        arr1.stream().forEach(new  Consumer<Integer>() {
            //s指代遍历获得的元素
            @Override
            public void accept(Integer s) {
                System.out.println(s);
            }
        });
        //lambda写法
        arr1.stream().forEach(s->{System.out.print(s);});
```
#### count统计
```java
System.out.println(arr1.stream().count());
```

#### toArray收集数组
```java
	ArrayList<Integer> arr1=new ArrayList<>();
        Collections.addAll(arr1,1,2,3,4,5,6);

        Integer[] result1 = arr1.stream().toArray(new IntFunction<Integer[]>() {
            //这里的内部类功能只是创建一个数组（value是数组的大小）给这个函数
            @Override
            public Integer[] apply(int value) {
                return new Integer[value];
            }
        });
        //Lambda格式
        Integer[] result = arr1.stream().toArray(value -> new Integer[value]);
        System.out.println (result);
```

#### collect(Collector collector)
分为三种，分别放到（List，Set，Map）

- 1，放于List
```java
.collect(Collectors.toList())
```

- 2,放于Set
```java
.collect(Collectors.toSet())
```
- 3,放于Map
```java
collect(Collectors.toMap(Function<? super T, ? extends K> keyMapper,
Function<? super T, ? extends U> valueMapper))
```
有两个内部类需要写，分别前后对应键和值的返回规则
例子
```java
HashMap<Integer,String> map= (HashMap<Integer, String>) list2.stream().collect(Collectors.toMap(new Function<String, Integer>() {//这里接口后面的类型是一一对应的，前者是流里的数据的类型，后者是要存入集合里时的类型
            @Override
            public Integer apply(String s) {//这里的s表示流里的数据
                 return Integer.parseInt(s.split(",")[0]);
            }
        }, new Function<String, String>() {
            @Override
            public String apply(String s) {
                return s.split(",")[1];
            }
        }));

        System.out.println(map.keySet());
        System.out.println(map.values());
```
**小细节，在使用collect往Map集合存数据的时候，键的值一定不能重复，不然会报错**

## 方法引用
### 方法引用的基础说明
由于方法引用常常用在stream流中，所以把方法引用放在这

把已经有的方法拿来当作函数式接口中抽象方法的方法体

- 前提：
1. 引用处必须是函数式接口
2. 被引用的方法必须已经存在
3. 被引用方法的形参和返回值需要跟抽象方法保持一致
4. 被引用方法的功能要满足当前需求

引用方法符“::”

### 1，引用静态方法
格式：类名：：静态方法


### 2，引用构造方法
格式：类名：：成员方法
- （1）其他类：类对象：：成员方法
- （2）本类：this：：方法名（需要在非静态的类里才能这样用，因为静态类没有this指针）
- （3）父类：super：：方法名

### 3，引用构造方法
格式：类名：：new

### 4，其他：用类名引用成员方法（局限：无法自由引用任何一个类中的方法）
格式：类名：：成员方法
- 它有独特的规则：
1. 需要有函数式接口
2. 被引用的方法必须已经存在
3. 被引用方法的形参，需要跟抽象方法的第二个形参到最后一个形参保持一致，返回值需要保持一致。（替换普通的3）
4. 被引用方法的功能需要满足当前的需求


### 5，其他：引用数组的构造方法
格式：数据类型[]：：new


示例
```java
public class Test4 {
    public static void main(String[] args) {
        ArrayList<Integer> list = new ArrayList<>();
        Collections.addAll(list,1,2,3,4,5);
        list.stream().filter(Test4::chose)//因为这个main方法是静态的，所以要用完整的类名去引用
			.forEach(System.out::println);
    }

    public static boolean chose(int i){
        return i%2==0;
    }

}
```

总的来说就是在满足前面所说的条件之下，作替换就可以实现想要的功能，但其实最常用的还是forEach（）对System.out::println的引用