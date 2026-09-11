---
title: 一些常见的基础算法
published: 2026-09-11
description: 主要详细讲顺序查找，二分查找，插值查找，分块查找，以及冒泡排序，插入排序，快速排序等
tags: [java，学习]
category: java
draft: false
---

## 基本查找
### 1. 顺序查找
就是字面意思，把数组中的每个元素都遍历一次，直到找到目标元素为止。

由于十分简单不做过多说明

### 2. 二分查找
二分查找是一种在有序数组中查找元素的查找算法，它的基本思想是：

1，先直接找到数组中居中的元素作比较，然后看目标元素在数组的左边还是右边。

2，如果目标元素在数组的左边，就继续在左边的数组中查找，否则继续在右边的数组中查找。

3，如果目标元素在数组的中间，就直接返回目标元素的索引。

4，如果数组为空，就返回-1。

5，如果数组中只有一个元素，就直接返回该元素的索引。

**但是二分查找只适用于有序数组，不能适用于无序数组。**

```java
public class BinarySearch {
    public static int binarySearch(int[] arr, int target) {
        int left = 0;
        int right = arr.length - 1;
        while (left <= right) {
            int mid = (left + right) / 2;
            if (arr[mid] == target) {
                return mid;
            } else if (arr[mid] < target) {
                left = mid + 1;
            } else {
                right = mid - 1;
            }
        }
        return -1;
    }
}
```

### 3. 插值查找

基础思路与二分查找类似，只是在查找目标元素时，是按元素占比的百分比来找目标比较数，因此适合那种元素分布比较均匀的数组。

有这样一个公式

**mid=(low+high)/2, 即mid=low+1/2*(high-low)**来计算中间元素的索引

其余的基本一致，都是比较目标数字与mid索引对应的元素的关系，再来确定下一次查找的范围。

### 4. 分块查找
原则：块内无序，块间有序

它的基础思路是，把数组分成多个块，每个块的元素都是有序的，但是块之间的元素是无序的。

而这个分的步骤是我们自己写的，遵守原则的情况下，我们可以根据数组的特征来确定块的大小。

具体方式是写一个块的类来表示里面的元素范围，以及开始索引和结束索引。

再在具体的方法里调用这个类的元素

```java
package mydo_levelup.algorithm;

public class BasicSearch03 {
    public static void main(String[] args) {
        int[]arr1={27,22,30,40,36,13,19,16,20,7,10,43,50,48};
        Block block1=new Block(22,40,0,4);
        Block block2=new Block(13,20,5,8);
        Block block3=new Block(7,10,9,10);
        Block block4=new Block(43,50,11,13);
        Block[] blockArr=new Block[4];
        blockArr[0]=block1;
        blockArr[1]=block2;
        blockArr[2]=block3;
        blockArr[3]=block4;
        System.out.println(basicFind(arr1,blockArr,48));//13

    }

    //写一个寻找，需要位置数组，需要找的数
    //返回对应的块区,没有找到就返回-1
    public static int blockFind(Block[]blockArr,int target){
        for(int i=0;i<blockArr.length;i++){
            if(target>=blockArr[i].getMin()&&target<=blockArr[i].getMax()){
                return i;
            }
        }
        return -1;
    }

    public static int basicFind(int[]arr, Block[]blockArr,int target){
        int blockIndex=blockFind(blockArr,target);
        if(blockIndex==-1){
            return -1;
        }
        for(int i=blockArr[blockIndex].getStart();i<=blockArr[blockIndex].getEnd();i++){
            if (arr[i]==target){
                return i;
            }
        }
        return -1;

    }



}

class Block{
    int min;
    int max;
    int start;
    int end;

    public Block(int min,int max,int start,int end){
        this.min=min;
        this.max=max;
        this.start=start;
        this.end=end;
    }

    public int getMin() {
        return min;
    }
    public void setMin(int min) {
        this.min = min;
    }
    public int getMax() {
        return max;
    }
    public void setMax(int max) {
        this.max = max;
    }
    public int getStart() {
        return start;
    }
    public void setStart(int start) {
        this.start = start;
    }
    public int getEnd() {
        return end;
    }
    public void setEnd(int end) {
        this.end = end;
    }
}

```

### 5. 斐波那契查找
这个查找只做简单的介绍

在介绍斐波那契查找算法之前，我们先介绍一下很它紧密相连并且大家都熟知的一个概念——黄金分割——1:0.168。

基本思想：也是二分查找的一种提升算法，通过运用黄金比例的概念在数列中选择查找点进行查找，提高查找效率。同样地，斐波那契查找也属于一种有序查找算法。

斐波那契查找也是在二分查找的基础上进行了优化，优化中间点mid的计算方式即可

说白了就是按黄金比例来选择中间点mid，其余都是二分查找的基本操作。

代码示例：

```java
public class FeiBoSearchDemo {
    public static int maxSize = 20;

    public static void main(String[] args) {
        int[] arr = {1, 8, 10, 89, 1000, 1234};
        System.out.println(search(arr, 1234));
    }

    public static int[] getFeiBo() {
        int[] arr = new int[maxSize];
        arr[0] = 1;
        arr[1] = 1;
        for (int i = 2; i < maxSize; i++) {
            arr[i] = arr[i - 1] + arr[i - 2];
        }
        return arr;
    }

    public static int search(int[] arr, int key) {
        int low = 0;
        int high = arr.length - 1;
        //表示斐波那契数分割数的下标值
        int index = 0;
        int mid = 0;
        //调用斐波那契数列
        int[] f = getFeiBo();
        //获取斐波那契分割数值的下标
        while (high > (f[index] - 1)) {
            index++;
        }
        //因为f[k]值可能大于a的长度，因此需要使用Arrays工具类，构造一个新法数组，并指向temp[],不足的部分会使用0补齐
        int[] temp = Arrays.copyOf(arr, f[index]);
        //实际需要使用arr数组的最后一个数来填充不足的部分
        for (int i = high + 1; i < temp.length; i++) {
            temp[i] = arr[high];
        }
        //使用while循环处理，找到key值
        while (low <= high) {
            mid = low + f[index - 1] - 1;
            if (key < temp[mid]) {//向数组的前面部分进行查找
                high = mid - 1;
                /*
                  对k--进行理解
                  1.全部元素=前面的元素+后面的元素
                  2.f[k]=k[k-1]+f[k-2]
                  因为前面有k-1个元素没所以可以继续分为f[k-1]=f[k-2]+f[k-3]
                  即在f[k-1]的前面继续查找k--
                  即下次循环,mid=f[k-1-1]-1
                 */
                index--;
            } else if (key > temp[mid]) {//向数组的后面的部分进行查找
                low = mid + 1;
                index -= 2;
            } else {//找到了
                //需要确定返回的是哪个下标
                if (mid <= high) {
                    return mid;
                } else {
                    return high;
                }
            }
        }
        return -1;
    }
}

```

### 6. 哈希查找
这个查找只做简单的介绍

哈希查找是分块查找的进阶版，适用于数据一边添加一边查找的情况。

一般是数组 + 链表的结合体或者是数组+链表 + 红黑树的结合体

暂时先放这些后面再补充


## 排序
### 1.冒泡排序
冒泡排序（Bubble Sort）也是一种简单直观的排序算法。

它重复的遍历过要排序的数列，一次比较相邻的两个元素，如果他们的顺序错误就把他们交换过来。

每次一次循环就会确定一个剩余元素的最大元素，所以执行n-1次循环。
n为数组的长度。

```java

package mydo_levelup.algorithm.sort;

public class PopSort {
    /*
            冒泡排序：
            核心思想：
            1，相邻的元素两两比较，大的放右边，小的放左边。
            2，第一轮比较完毕之后，最大值就已经确定，第二轮可以少循环一次，后面以此类推。
            3，如果数组中有n个数据，总共我们只要执行n-1轮的代码就可以。
        */
    public static void main(String[] args) {
        int[]arr={2,4,3,1,5,6,};
        int[]arr1=popSort(arr);
        for(int i=0;i<arr1.length;i++){
            System.out.print(arr1[i]+" ");
        }
    }


    public static int[] popSort(int[]arr){
        for(int i=0;i<arr.length-1;i++){//外面的大的循环，控制轮数
            for(int j=0;j<arr.length-1-i;j++){//里面的循环，控制比较的次数，上限-i，避免重复比较
                if(arr[j]>arr[j+1]){
                    int temp=arr[j];
                    arr[j]=arr[j+1];
                    arr[j+1]=temp;
                }
            }
        }
        return arr;
    }

}

```

### 2.选择排序
选择排序有点像冒泡排序，但是它先确定较小的元素，然后放到数组的前面。

具体从0索引开始，分别向后面的其他元素进行比较，若大于后面的数就交换位置。

冒泡排序和选择排序写法上最大的区别就是，在一轮比较中，冒泡排序选取元素的两个索引都会往后移，而选择排序里，一个索引固定，然后另一个索引后移

代码示例：
```java
package mydo_levelup.algorithm.sort;

public class ChooseSort {
    /*
            选择排序：
                1，从0索引开始，跟后面的元素一一比较。
                2，小的放前面，大的放后面。
                3，第一次循环结束后，最小的数据已经确定。
                4，第二次循环从1索引开始以此类推。

         */
    public static void main(String[] args) {
        int[]arr ={3,2,4,1,5,9};
        int[]arr1=chooseSort(arr);
        for(int j=0;j<arr.length;j++){
            System.out.print(arr[j]+" ");
        }
    }

    public static int[] chooseSort(int[]arr){
        for(int i=0;i<arr.length;i++){//外面的大的循环，控制轮数,这个确定其中那个不动的索引
            for(int j=i+1;j<arr.length;j++){//看作那个会后裔的索引，从不动的索引出发
                if(arr[i]>arr[j]){
                    int temp=arr[i];
                    arr[i]=arr[j];
                    arr[j]=temp;
                }
            }
        }
        return arr;
    }

}

```

### 3.插入排序
插入排序（Insertion Sort）

先认为0到n是有序的，然后从n+1开始，每次将n+1这个元素插入到有序的序列中。

每次插入都需要比较，直到找到合适的位置。

重复以上步骤，直到n+1等于数组的长度。

然而，这个“插入”的动作，我们并不使用真实的插入方式，我们从要被插入的元素开始，往前比较，被插入的数更小就交换，知道被插入的数比前面的数大，此时也就找到了正确的位置，实现了所谓的“插入”。

```java
package com.itheima.mysort;


public class A03_InsertDemo {
    public static void main(String[] args) {
        /*
            插入排序：
                将0索引的元素到N索引的元素看做是有序的，把N+1索引的元素到最后一个当成是无序的。
                遍历无序的数据，将遍历到的元素插入有序序列中适当的位置，如遇到相同数据，插在后面。
                N的范围：0~最大索引

        */
        int[] arr = {3, 44, 38, 5, 47, 15, 36, 26, 27, 2, 46, 4, 19, 50, 48};

        //1.找到无序的哪一组数组是从哪个索引开始的。  2
        int startIndex = -1;
        for (int i = 0; i < arr.length; i++) {
            if(arr[i] > arr[i + 1]){
                startIndex = i + 1;
                break;
            }
        }

        //2.遍历从startIndex开始到最后一个元素，依次得到无序的哪一组数据中的每一个元素
        for (int i = startIndex; i < arr.length; i++) {
            //问题：如何把遍历到的数据，插入到前面有序的这一组当中

            //记录当前要插入数据的索引
            int j = i;

            while(j > 0 && arr[j] < arr[j - 1]){
                //交换位置
                int temp = arr[j];
                arr[j] = arr[j - 1];
                arr[j - 1] = temp;
                j--;
            }

        }
        printArr(arr);
    }

    private static void printArr(int[] arr) {
        //3.遍历数组
        for (int i = 0; i < arr.length; i++) {
            System.out.print(arr[i] + " ");
        }
        System.out.println();
    }

}

```

### 4.快速排序（最重要）
顾名思义，它真的很快

#### 具体步骤
1. 从数列中挑出一个元素，一般都是左边第一个数字，称为 "基准数";

2. 创建两个指针，一个从前往后走，一个从后往前走。

3. 先执行后面的指针，找出第一个比基准数小的数字

4. 再执行前面的指针，找出第一个比基准数大的数字

5. 交换两个指针指向的数字

6. 直到两个指针相遇

7. 将基准数跟指针指向位置的数字交换位置，称之为：基准数归位。

8. 第一轮结束之后，基准数左边的数字都是比基准数小的，基准数右边的数字都是比基准数大的。

9. 把基准数左边看做一个序列，把基准数右边看做一个序列，按照刚刚的规则递归排序

10.直至让里面的两个指针不符合条件，即start>end,递归结束。

代码演示：
```java
package mydo_levelup.algorithm.sort;

public class quickSort {
    public static void main(String[] args) {
        //快速排序，
        //首先要找索引0位置的数作为标准，然后用两个指标start，end来找比对，start找比标准数大的，end找小的
        int[] arr={4,3,1,2,6,5};
        chooseSort(arr,0,arr.length-1);
        for(int j=0;j<arr.length;j++)
        System.out.print(arr[j]+" ");
    }

    //快速排序
    //需要数组，涉及递归，还要一个start和end?
    public static void chooseSort(int[] arr,int i,int j){
        if(i>=j){return;}//递归跳出条件
        int start = i;
        int end = j;
        int basicNum = arr[i];
        while(start!=end){
            while(true){
                //做end的寻找
                if(arr[i]>arr[end]||end<=start){
                    break;
                }
                end--;
            }
            while(true){
                if(arr[i]<arr[start]||end<=start){
                    break;
                }
                start++;
            }
            //确定的end和start做交换
            int temp=arr[start];
            arr[start]=arr[end];
            arr[end]=temp;
        }
        //索引0的数和中间数做交换
        int temp=arr[i];
        arr[i]=arr[end];
        arr[end]=temp;

        chooseSort(arr,i,start-1);//对基准数左边的序列递归排序
        chooseSort(arr,start+1,j);//对基准数右边的序列递归排序
    }

}

```