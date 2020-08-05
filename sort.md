# 排序

## 数据类型

在以下的排序算法都使用了Comparable的数据结构，所以适用于所有实现了Comparable接口的数据类型。

在Java中，许多数据结构，比如**Integer**和**Double**，或者**String**和其他许多高级数据类型（File和URL等）都实现了Comparable接口。当然，也可以自行定义数据类型。

**在创建自己的数据结构时，只要实现Comparable接口，并且在其中实现compareTo()方法来定义目标类型对象的顺序即可**。比如：

```java
public class Date implements Comparable<Date> {
    private final int year;
    private final int month;
    private final int day;

    public Date(int year, int month, int day) {
        this.year = year;
        this.month = month;
        this.day = day;
    }

    @Override
    public int compareTo(Date that) {
        if(this.year > that.year) {
            return +1;
        }
        if(this.year < that.year) {
            return -1;
        }
        if(this.month > that.month) {
            return +1;
        }
        if(this.month < that.month) {
            return -1;
        }
        if(this.day > that.day) {
            return +1;
        }
        if(this.day < that.day) {
            return -1;
        }
        return 0;
    }

    public String toString() {
        return year + "/" + month + "/" + day;
    }
}
```

## 选择排序

### 介绍

- 首先，找到数组中最小的一个元素，将他和第一个元素交换位置，那么第一个元素就是最小值（如果是他自己，就自己和自己交换）；
- 之后，在剩下的元素中继续找最小值，和第二个元素交换位置；
- 如此反复，直到最后一个元素为止。

### 算法

```java
public class Selection {
    public static void sort(Comparable[] a) {
        int N = a.length;
        for(int i = 0; i < N; i++) {
            int min = i;
            for(int j = i + 1; j < N; j++) {
                if(less(a[j], a[min])) {
                    min = j;
                }
                exch(a, i, min);
            }
        }
    }

    public static boolean less(Comparable a, Comparable b) {
        return a.compareTo(b) < 0;
    }

    public static void exch(Comparable[] a, int i, int j) {
        Comparable t = a[i];
        a[i] = a[j];
        a[j] = t;
    }
}
```

### 分析

从代码可以看出，选择排序主要时间花在循环里的比较次数上，比较了**N²/2**次。且比较次数和数组本身并无关系，所以说**运行时间和输入无关**。而且在所学习的排序中**数据移动次数是最少的**。

> 命题A 对于长度为N的数组，选择排序需要大约N²/2次比较和N次交换。

## 插入排序

### 介绍

就像打扑克牌一样，每次先排好两张，第三张对比前面选择它的位置，第四张也一样和前面三张对比。

- 从第二张开始，和第一张比较，如果小于第一张则交换位置，否则不动；

- 第三张先和第二张比较，如果可以交换位置，则和第一张比较，如果比第二张大，则没有和第一张比的必要性了；

- 以此类推，直到最后一张为止。

### 算法
``` java
public class Insertion {
    public static void sort(Comparable[] a) {
        int N = a.length;
        for(int i = 1; i < N; i++) {
            for(int j = i; j > 0 && less(a[j], a[j-1]); j--) {
                exch(a, j, j-1);
            }
        }
    }

    public static boolean less(Comparable a, Comparable b) {
        return a.compareTo(b) < 0;
    }

    public static void exch(Comparable[] a, int i, int j) {
        Comparable t = a[i];
        a[i] = a[j];
        a[j] = t;
    }
}
```

## 希尔排序

### 介绍

使数组中间隔为h的数列是有序的。

### 算法

``` java
public class Shell {
    public static void sort(Comparable[] a) {
        int N = a.length;
        int h = 1;
        while(h < N/3) h = h*3 + 1;
        while(h >= 1) {
            for(int i = h; i < N; i++) {
                for(int j = i; j >= h && less(a[j], a[j-h]); j -= h) {
                    exch(a, j, j-h);
                }
            }
            h = h/3;
        }
    }

    public static boolean less(Comparable a, Comparable b) {
        return a.compareTo(b) < 0;
    }

    public static void exch(Comparable[] a, int i, int j) {
        Comparable t = a[i];
        a[i] = a[j];
        a[j] = t;
    }
}
```

## 归并排序

### 介绍

将两个有序的数组归并成一个大的有序数组。

将一个数组排序，先递归的将它分为两半，然后将结果合并起来。

### 算法

```java
public class Merge {
    private static Comparable[] aux;

    public static void sort(Comparable[] a) {
        aux = new Comparable[a.length];
        sort(a, 0, a.length - 1);
    }

    private static void sort(Comparable[] a, int lo, int hi) {
        if(lo <= hi) return;
        int mid = lo + (lo + hi)/2;
        sort(a, lo, mid);
        sort(a, mid + 1, hi);
        merge(a, lo, mid, hi);
    }

    public static void merge(Comparable[] a, int lo, int mid, int hi) {
        int i = lo, j = mid + 1;


        for(int k = lo; k <= hi; k++) {
            aux[k] = a[k];
        }

        for(int k = lo; k <= hi; k++) {
            if(i > mid) {
                a[k] = aux[j++];
            } else if(j > hi) {
                a[k] = aux[i++];
            } else if(less(aux[j], aux[i])) {
                a[k] = aux[j++];
            } else {
                a[k] = a[i++];
            }
        }
    }

    public static boolean less(Comparable a, Comparable b) {
        return a.compareTo(b) < 0;
    }

    public static void exch(Comparable[] a, int i, int j) {
        Comparable t = a[i];
        a[i] = a[j];
        a[j] = t;
    }
}
```

## 快速排序

### 介绍

快速排序将数组分为两部分，两部分都有序的时候整个数组就是有序了的。与归并排序相反，归并先递归再排序，*快速排序先排序后递归，并且不是平均划分数组，是根据partition的值划分*。

核心为**partition函数**，找到一个数（一般定位a[0]），使其左边的数都小于他，右边的数都大于他。

具体实现为，从左右两边开始移动游标，左边如果找到大于他的数就停止，右边如果小于他也停止，之后交换这两个地方的数值。直到两个游标相遇，说明右边游标的左边都比他小，右边都比他大，这时交换右边游标和a[0]的数值，并返回右边游标。

经过partition后会得到一个确定的位置，这个位置上的数左边都比他小，右边都比他大。之后再继续partition他左边部分的数组，和他右边部分的数组。

### 算法

```java
public class Quick {
    public static void sort(Comparable[] a) {
        StdRandom.shuffle(a);
        sort(a, 0, a.length-1);
    }

    private static void sort(Comparable[] a, int lo, int hi) {
        if(hi <= lo) return;
        int j = partition(a, lo, hi);
        sort(a, lo, j-1);
        sort(a, j+1, hi);
    }

    public static int partition(Comparable[] a, int lo, int hi) {
        int i = lo, j = hi + 1;
        Comparable v = a[lo];
        while(true) {
            while(less(a[++i], v)) {
                if(i == hi) break;
            }
            while(less(v, a[--j])) {
                if(j == lo) break;
            }
            if(i >= j) break;
            exch(a, i, j);
        }
        exch(a, lo, j);
        return j;
    }

    public static boolean less(Comparable a, Comparable b) {
        return a.compareTo(b) < 0;
    }

    public static void exch(Comparable[] a, int i, int j) {
        Comparable t = a[i];
        a[i] = a[j];
        a[j] = t;
    }
}
```

## 优先队列 - 堆排序

### 介绍

堆排序就是指一个二叉树，树的根节点要比它的两个子节点大。

核心是*swim*和*sink*两个方法。

**优先队列**是使用堆排序实现的。**插入数据**时将数据放入数组最后一个位置，之后使用swim将数字上浮直到找到合适的位置。**删除最大值**时将根节点pq[1]和数组末尾元素交换，之后sinkpq[1]元素直到合适的位置。

**swim**方法的实现是，当子节点大于父节点时，和父节点交换位置即可，直到新的父节点比他大为止。**sink**的方法实现是，当父节点大于子节点时，和子节点中数值较大的一个交换位置，直到新的子节点都比自己小为止。

### 算法

- 优先队列

```java
public class MaxPQ<Key extends Comparable<Key>> {
    private Key[] pq;
    private int N;

    public MaxPQ(int maxN) {
        pq = (Key[])new Object[maxN + 1]; //pq[0]不存储元素
    }

    public boolean isEmpty() {
        return N == 0;
    }

    public int size() {
        return N;
    }

    public void insert(Key v) {
        pq[++N] = v;
        swim(N);
    }

    public Key delMax() {
        Key max = pq[1];
        exch(1, N--);
        pq[N+1] = null;
        sink(1);
        return max;
    }

    private boolean less(int i, int j) {
        return pq[i].compareTo(pq[j]) < 0;
    }

    private void exch(int i, int j) {
        Key t = pq[i];
        pq[i] = pq[j];
        pq[j] = t;
    }

    private void swim(int k) {
        while(k > 1 && less(k/2, k)) {
            exch(k/2, k);
            k = k/2;
        }
    }

    private void sink(int k) {
        while(k < N) {
            int j = k * 2;
            if(j < N && less(j, j+1)) j++;
            if(!less(k, j)) break;
            exch(k, j);
            k = j;
        }
    }
}
```

- 堆排序

```java
public static void sort(Comparable[] a){
    int N = a.length;
    for(int i = N/2; i >= 1; i--){
        sink(a, i, N);
    }
    while(N > 1){
        exch(a, 1, N--);
        sink(a, 1, N);
    }
}

private static void sink(Comparable[] a, int k, int N){
    while(2*k <= N){
        int j = k * 2;
        if(j < N && less(a, j, j+1)) j++;
        if(!less(a, k, j)) break;
        exch(a, k, j);
        k = j;
    }
}
```

## 应用

### 将各种数据排序

一般情况下，由于Java的回调机制，我们可以将任何实现了Comparable接口的数据类型进行排序，比如**Integer**，**String**，**Double**，**File**，**URL**这些类型的数组。但是，在实际情况中，我们需要排序的数据类型都是需要自己定义的。

#### 多键数组

在一个应用程序中，一个元素的多种属性都可能被用来用作排序的键。比如在*交易*的例子中，有时需要将交易按照客户排序，有时需要将交易按照时间排序，有时需要将交易按照金钱排序。**要实现这种灵活性，可以使用Comparator接口**。然后修改排序方法，在排序时直接调用：

```java
Insertion.sort(a, new Transaction.WhenOrder());//使用时间排序
```

这样sort方法每次都会回调Transaction类中用例指定的compare()方法。

```java
public static void sort(Object[] a, Comparator c) {
    int N = a.length;
    for(int i = 1; i < N; i++) {
        for(int j = i; j > 0 && less(c, a[j], a[j-1]); j--) {
            exch(a, j, j-1);
        }
    }
}

private static boolean less(Comparator c, Object v, Object w) {
    return c.compare(v, w) < 0;
}

private static void exch(Onject[] a, int i, int j) {
    Object t = a[i];
    a[i] = a[j];
    a[j] = t;
}
```

以上为使用了Comparator的插入排序。

下面是Transaction的实现：

```java
import java.util.Comparator;

public class Transaction {
    private final String who;
    private final Date when;
    private final double amount;

    public Transaction(String who, Date when, double amount) {
        this.who = who;
        this.when = when;
        this.amount = amount;
    }
    
    public static class WhoOrder implements Comparator<Transaction> {

        @Override
        public int compare(Transaction v, Transaction w) {
            return v.who.compareTo(w.who);
        }
    }
    
    public static class WhenOrder implements Comparator<Transaction> {

        @Override
        public int compare(Transaction v, Transaction w) {
            return v.when.compareTo(w.when);
        }
    }
    
    public static class HowMuchOrder implements Comparator<Transaction> {

        @Override
        public int compare(Transaction v, Transaction w) {
            if(v.amount < w.amount) return -1;
            if(v.amount > w.amount) return +1;
            return 0;
        }
    }
}
```

#### 稳定性

如果一个排序可以保留数组中重复元素的顺序，则被称为*稳定的*。

在以上学习的排序算法中，**插入排序和归并排序是稳定的**，**选择排序，希尔排序，快速排序和堆排序则不是稳定的**。

举个例子，比如得到一个数组要将其按照时间和地理位置排序，我们**首先**将其变为按时间排序，**再将其按照地理位置排序**，这时就要考虑使用的排序方法是否稳定，否则就会出现乱序。

### 我们应该使用哪种方法排序

大多数实际情况中，**快速排序**是最佳选择。

#### Java系统库的排序方法

Java的系统程序员选择对**原始数据**使用**（三向切分）快速排序**，对**引用类型**使用**归并排序**。

当为实际应用开发Java程序时，直接使用Java的**Arrays.sort()**已经基本够用了。Java还提供了优先队列 PriorityQueue可以直接使用。

### 问题的归约

归约指的是未解决某一问题所设计的方法也可以用来解决其他的问题。

#### 找到重复元素

1. 将数组排序
2. 遍历有序数组，记录重复元素即可

以下为记录不重复的元素的案例：

```java
Quick.sort(a);
int count = 1; // 假设a.length > 0
for(int i = 1; i < a.length; i++) {
    if(a[i].compareTo(a[i-1]) != 0) {
        count++;
    }
}
```

#### 排名

#### 优先队列

#### 中位数与顺序统计

比如找到一组数列中第k小的元素：

```java
public static Comparable select(Comparable[] a, int k) {
    StdRandom.shuffle(a);
    int lo = 0, hi = a.length - 1;
    while(hi > lo) {
        int j = partition(a, lo, hi);
        if(j == k) return a[k];
        else if(j > k) hi = j - 1;
        else if(j < k) lo = j + 1;
    }
    return a[k];
}
```

