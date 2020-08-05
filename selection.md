# 基础查找

## 无序链表中的顺序查找

### 介绍

在无序链表中查找，使用最老套的方法，从头开始遍历，如果没有，就添加一个新结点在表头。

### 算法

```java
public class SequentialSearchST<Key, Value> {
    private Node first;

    private class Node{
        Key key;
        Value value;
        Node next;

        public Node(Key key, Value value, Node next) {
            this.key = key;
            this.value = value;
            this.next = next;
        }
    }

    public Value get(Key key) {
        for(Node x = first; x != null; x = x.next) {
            if(x.key.equals(key)) {
                return x.value;
            }
        }
        return null;
    }

    public void put(Key key, Value value) {
        for(Node x = first; x != null; x = x.next) {
            if(x.key.equals(key)) {
                x.value = value;
                return;
            }
        }
        first = new Node(key, value, first);
    }
}
```

此方法非常低效。随机命中所需要的平均比较次数为~N/2。

## 有序数组中的二分查找

### 介绍

它使用的数据结构是一对平行数组，一个存储键，一个存储值。其中要保证键是有序的（为Comparable类型的）值。

这份实现的核心是**rank()方法，它返回表中小于给定键的键的数量**。

### 算法

```java
public class BinarySearchST<Key extends Comparable<Key>, Value> {
    private Key[] keys;
    private Value[] values;
    private int N;

    public BinarySearchST(int capacity) {
        this.keys = (Key[])new Object[capacity];
        this.values = (Value[])new Object[capacity];
    }

    public int size() {
        return N;
    }

    public Value get(Key key) {
        if(isEmpty()) {
            return null;
        }
        int i = rank(key);
        if(i < N && keys[i].compareTo(key) == 0) {
            return values[i];
        }
        return null;
    }

    public int rank(Key key) {
        int lo = 0;
        int hi = N - 1;
        while(lo <= hi) {
            int mid = lo + (lo + hi) / 2;
            int cmp = key.compareTo(keys[mid]);
            if(cmp < 0) hi = mid - 1;
            else if(cmp > 0) lo = mid + 1;
            else return mid;
        }
        return lo; //此时lo>hi，返回的值大于比较值
    }

    public void put(Key key, Value value) {
        int i = rank(key);
        if(i < N && keys[i].compareTo(key) == 0) {
            values[i] = value;
            return;
        }
        for(int j = N; j > i; j--) {
            keys[j] = keys[j-1];
            values[j] = values[j-1];
        }
        keys[i] = key;
        values[i] = value;
        N++;
    }

    public boolean isEmpty() {
        return N == 0;
    }

    public Key min() {
        return keys[0];
    }

    public Key max() {
        return keys[N-1];
    }

    public Key select(int k) {
        return keys[k];
    }

    public Key ceiling(Key key) {
        int k = rank(key);
        return keys[k];
    }

    public Key floor(Key key) {
        int k = rank(key);
        return keys[k - 1];
    }

    public Key delete(Key key) {
        if(isEmpty()) return null;
        int i = rank(key);
        if(i < N && keys[i].compareTo(key) == 0) {
            Key delKey = keys[i];
            for(int j = i; j < N - 1; j++) {
                keys[j] = keys[j+1];
                values[j] = values[j+1];
            }
            N--;
            keys[N] = null;
            values[N] = null;
            return delKey;
        }
        return null;
    }

    public Iterable<Key> keys(Key lo, Key hi) {
        Queue<Key> q = new Queue<Key>();
        for(int i = rank(lo); i < rank(hi); i++) {
            q.enqueue(keys[i]);
        }
        if(contains(hi)) {
            q.enqueue(keys[rank(hi)]);
        }
        return q;
    }

    public boolean contains(Key key) {
        return get(key) != null;
    }
}
```

rank()方法还有一种递归实现：

```java
public int rank(Key key, int lo, int hi) {
    if(hi < lo) return lo;
    int mid = lo + (lo + hi) / 2;
    int cmp = key.compareTo(keys[mid]);
    if(cmp < 0) {
        return rank(key, lo, mid - 1);
    } else if(cmp > 0) {
        return rank(key, mid + 1, hi);
    } else {
        return mid;
    }
}
```

在使用的时候调用`rank(key, 0, N - 1)`即可。

# 二叉查找树

## 定义

一棵二叉查找树（BST）每个结点都含有一个Comparable的键（以及相关联的值）且每个结点的键都大于其左子树中任意结点的键而小于右子树的任意结点的键。

## 基本实现

```java
public class BST<Key extends Comparable<Key>, Value> {
    private Node root;

    // 结点表示
    private class Node{
        private Key key;
        private Value value;
        private Node left, right;
        private int N; // 指该结点下的根的个数

        public Node(Key key, Value value, int N) {
            this.key = key;
            this.value = value;
            this.N = N;
        }
    }

    public int size() {
        return size(root);
    }

    private int size(Node node) {
        if(node == null) return 0;
        else return node.N;
    }

    public Value get(Key key) {
        return get(root, key);
    }

    // get意味着一系列的return指令
    private Value get(Node node, Key key) {
        if(node == null) return null;
        int cmp = key.compareTo(node.key);
        if(cmp < 0) return get(node.left, key);
        else if(cmp > 0) return get(node.right, key);
        else return node.value;
    }

    public void put(Key key, Value value) {
        root = put(root, key, value);
    }

    // put意味着重置搜索路径上每个父结点指向子结点的链接，并且增加路径上每个结点中的计数器的值
    // 唯一的新链接指的就是最底层指向新结点的链接，更上层的链接可以通过比较规避掉
    private Node put(Node node, Key key, Value value) {
        if(node == null) return new Node(key, value, 1);
        int cmp = key.compareTo(node.key);
        if(cmp < 0) return node.left = put(node.left, key, value);
        else if(cmp > 0) return node.right = put(node.right, key, value);
        else node.value = value;
        node.N = size(node.left) + size(node.right) + 1;
        return node;
    }

    public Key min() {
        return min(root).key;
    }

    private Node min(Node node) {
        if(node.left == null) return node;
        return min(node.left);
    }

    public Key max() {
        return max(root).key;
    }

    private Node max(Node node) {
        if(node.right == null) return node;
        return max(node.right);
    }

    public Key floor(Key key) {
        Node node = floor(root, key);
        if(node == null) return null;
        return node.key;
    }

    // 向下取整，如果小于根结点，表示在左子树中；
    // 如果大于根结点，除非右子树中存在小于它的值，才找右子树，否则为根结点
    private Node floor(Node node, Key key) {
        if(node == null) return null;
        int cmp = key.compareTo(node.key);
        if(cmp == 0) return node;
        if(cmp < 0) return floor(node.left, key);
        Node t = floor(node.right, key); // 把node.right当做根结点找一遍，找不到返回null
        if(t != null) return t;
        else return node; // t为null，说明node.right（右子树）中没有比它小的，所以返回根结点
    }

    public Key select(int k) {
        return select(root, k).key;
    }

    private Node select(Node node, int k) {
        if(node == null) return null;
        int t = size(node.left);
        if(t > k) return select(node.left, k);
        else if(t < k) return select(node.right, k - t - 1);
        else return node;
    }

    public int rank(Key key) {
        return rank(key ,root);
    }

    private int rank(Key key, Node node) {
        if(node == null) return 0;
        int cmp = key.compareTo(node.key);
        if(cmp < 0) return rank(key, node.left);
        else if(cmp > 0) return 1 + size(node.left) + rank(key, node.right);
        else return size(node.left);
    }

    public void deleteMin() {
        root = deleteMin(root);
    }

    private Node deleteMin(Node node) {
        if(node.left == null) return node.right;
        node.left = deleteMin(node.left);
        node.N = size(node.left) + size(node.right) + 1;
        return node;
    }

    public void delete(Key key) {
        root = delete(root, key);
    }

    private Node delete(Node node, Key key) {
        if(node == null) return null;
        int cmp = key.compareTo(node.key);
        if(cmp < 0) node.left = delete(node.left, key);
        else if(cmp > 0) node.right = delete(node.right, key);
        else {
            Node t = node;
            node = min(t.right);
            node.left = t.left;
            node.right = deleteMin(t.right);
        }
        node.N = size(node.left) + size(node.right) + 1;
        return node;
    }

    public Iterable<Key> keys() {
        return keys(min(), max());
    }

    public Iterable<Key> keys(Key lo, Key hi) {
        Queue<Key> queue = new Queue<>();
        keys(root, queue, lo, hi);
        return queue;
    }

    private void keys(Node node, Queue<Key> queue, Key lo, Key hi) {
        if(node == null) return;
        int cmplo = lo.compareTo(node.key);
        int cmphi = hi.compareTo(node.key);
        if(cmplo < 0) keys(node.left, queue, lo, hi);
        if(cmplo <= 0 && cmphi >= 0) queue.enqueue(node.key);
        if(cmphi > 0) keys(node.right, queue, lo, hi);
    }
}
```

### 数据表示

和链表一样，使用一个私有的内部类表示树上的一个结点。每个结点含有一个键，一个值，一条左链接，一条右链接和一个结点计数器（以该结点为根的子树的根结点的总数）。

```java
private class Node{
    private Key key;
    private Value value;
    private Node left, right;
    private int N; // 指该结点下的根的个数

    public Node(Key key, Value value, int N) {
        this.key = key;
        this.value = value;
        this.N = N;
    }
}
```

*一棵二叉树代表一组键的集合，而一组键的集合可以用多棵二叉树来表示。*

### 查找

使用递归完成，存在则返回，不存在则为null

```java
    public Value get(Key key) {
        return get(root, key);
    }

    // get意味着一系列的return指令
    private Value get(Node node, Key key) {
        if(node == null) return null;
        int cmp = key.compareTo(node.key);
        if(cmp < 0) return get(node.left, key);
        else if(cmp > 0) return get(node.right, key);
        else return node.value;
    }
```

### 插入

如果树是空的，就**返回一个含有该键值对的新的结点**；如果被查找的键小于根结点的键，则继续在左子树中查找，否则，就会在右子树中查找。 

```java
    public void put(Key key, Value value) {
        root = put(root, key, value);
    }

    // put意味着重置搜索路径上每个父结点指向子结点的链接，并且增加路径上每个结点中的计数器的值
    // 唯一的新链接指的就是最底层指向新结点的链接，更上层的链接可以通过比较规避掉
    private Node put(Node node, Key key, Value value) {
        if(node == null) return new Node(key, value, 1);
        int cmp = key.compareTo(node.key);
        if(cmp < 0) return node.left = put(node.left, key, value);
        else if(cmp > 0) return node.right = put(node.right, key, value);
        else node.value = value;
        node.N = size(node.left) + size(node.right) + 1;
        return node;
    }
```

### 最大键和最小键

如果一棵树的左子树为空，则最小键为根结点的键；如果左子树非空，那么最小键为左子树的最小键。

最大键类似，找右子树。

```java
    public Key min() {
        return min(root).key;
    }

    private Node min(Node node) {
        if(node.left == null) return node;
        return min(node.left);
    }

    public Key max() {
        return max(root).key;
    }

    private Node max(Node node) {
        if(node.right == null) return node;
        return max(node.right);
    }
```

### 向上取整和向下取整

如果给定的键小于根结点的键，那么向下取整一定存在于左子树；如果给定的键大于根结点，那么**只有当右子树存在小于它的结点时，向下取整才会出现在右子树，否则返回根结点**。

向上取整同理，将左换为右。

```java
    public Key floor(Key key) {
        Node node = floor(root, key);
        if(node == null) return null;
        return node.key;
    }

    // 向下取整，如果小于根结点，表示在左子树中；
    // 如果大于根结点，除非右子树中存在小于它的值，才找右子树，否则为根结点
    private Node floor(Node node, Key key) {
        if(node == null) return null;
        int cmp = key.compareTo(node.key);
        if(cmp == 0) return node;
        if(cmp < 0) return floor(node.left, key);
        Node t = floor(node.right, key); // 把node.right当做根结点找一遍，找不到返回null
        if(t != null) return t;
        else return node; // t为null，说明node.right（右子树）中没有比它小的，所以返回根结点
    }
```

### 选择

排名为k的键。如果k小于根结点的N，则排位为k的键在其左子树；如果大于根结点的N，则在其右子树查找排名为（N-k-1）的键。

```java
    public Key select(int k) {
        return select(root, k).key;
    }

    private Node select(Node node, int k) {
        if(node == null) return null;
        int t = size(node.left);
        if(t > k) return select(node.left, k);
        else if(t < k) return select(node.right, k - t - 1);
        else return node;
    }
```

### 排名

给定一个key返回它的排名。如果给定的键和根结点相等，则返回根结点左子树的根总数；如果小于根结点，则在左子树中查找；如果大于根结点，则返回（1+根结点左子树结点总数+它在右子树上的排名）。

```java
    public int rank(Key key) {
        return rank(key ,root);
    }

    private int rank(Key key, Node node) {
        if(node == null) return 0;
        int cmp = key.compareTo(node.key);
        if(cmp < 0) return rank(key, node.left);
        else if(cmp > 0) return 1 + size(node.left) + rank(key, node.right);
        else return size(node.left);
    }
```

### 删除最大键和删除最小键

删除最小键，如果根结点的左子树为空，则返回右子树。我们不断深入根结点的左子树，直到找到一个空链接，然后**将指向该结点的链接指向该结点的右子树**。并更新路径上的结点计数器的值。

删除最大键同理，将左换为右即可。

```java
    public void deleteMin() {
        root = deleteMin(root);
    }

    private Node deleteMin(Node node) {
        if(node.left == null) return node.right;
        node.left = deleteMin(node.left);
        node.N = size(node.left) + size(node.right) + 1;
        return node;
    }
```

### 删除

如果要删除的键小于根结点的键，则在左子树中继续查找；如果删除的键大于根结点的键，则在右子树中继续查找；如果找到了要删除的键，则在其**右子树**中找到一个最小值替换它的位置。

```java
    public void delete(Key key) {
        root = delete(root, key);
    }

    private Node delete(Node node, Key key) {
        if(node == null) return null;
        int cmp = key.compareTo(node.key);
        if(cmp < 0) node.left = delete(node.left, key);
        else if(cmp > 0) node.right = delete(node.right, key);
        else {
            Node t = node;
            node = min(t.right);
            node.left = t.left;
            node.right = deleteMin(t.right);
        }
        node.N = size(node.left) + size(node.right) + 1;
        return node;
    }
```

### 范围查找

keys()返回一个Iterable类型。我们可以通过**中序遍历**将每个结点的键放入一个实现了Iterable类的数据结构中。

```java
    public Iterable<Key> keys() {
        return keys(min(), max());
    }

    public Iterable<Key> keys(Key lo, Key hi) {
        Queue<Key> queue = new Queue<>();
        keys(root, queue, lo, hi);
        return queue;
    }

    private void keys(Node node, Queue<Key> queue, Key lo, Key hi) {
        if(node == null) return;
        int cmplo = lo.compareTo(node.key);
        int cmphi = hi.compareTo(node.key);
        if(cmplo < 0) keys(node.left, queue, lo, hi);
        if(cmplo <= 0 && cmphi >= 0) queue.enqueue(node.key);
        if(cmphi > 0) keys(node.right, queue, lo, hi);
    }
```

## 分析

> 在一棵二叉查找树中，所有操作在最坏的情况下所需要的时间和树的高度成正比。

# 平衡查找树（红黑树

> 这是一种能保证无论怎么构建它，它的运行时间都是对数级别的二分查找树。

## 2-3树

> 定义：
>
> - 2-结点，含有一个键（及其对应的值）和两条链接，左链接指向2-3树中的键都小于该结点，右链接指向的2-3树中的键都大于该结点。
> - 3-结点，含有两个键（及其对应的值）和三条链接，左链接指向2-3树中的键都小于该结点，中链接指向的键都位于该结点的两个键之间，右链接指向的2-3树中的键都大于该结点。

<img src="I:%5CJAVA%5CJAVAstudy%5Cnotes%5CAlgorithm%5Cpicture%5C2-3-%E7%BB%93%E7%82%B9-1584769617723.jpg" style="zoom:50%;" />

### 查找

首先和该结点对应的键相比较，如果相等查找命中，如果不等，那么就根据比较结果查找对应区间的链接所指向的子树，并在其子树中递归地继续查找。如果是空链接，则查找未命中。

### 向2-结点中插入新键

先进行一次未命中的查找，如果查找结束于一个2-结点，则把2-结点变成一个3-结点（把要插入的键保存在其中即可。

### 向3-结点中插入新键

#### 向一颗只有3-结点的树中插入新键

先临时将新键存入该结点中，使之成为一个4-结点。一个4-结点很容易转化为由3个2-结点组成的2-3树。

<img src="I:%5CJAVA%5CJAVAstudy%5Cnotes%5Cpicture%5C%E6%8F%92%E5%85%A5%E6%96%B0%E9%94%AE1.jpg" style="zoom:50%;" />

#### 向一个父结点为2-结点的3-结点中插入新键

构造一个临时的4-结点，此时不会为中键创造一个新结点，而是将其移动至原来的父结点中。将原3-结点的一条链接指向新的父结点中的原中键左右两边的两条链接，并分别指向两个新的2-结点。

<img src="I:%5CJAVA%5CJAVAstudy%5Cnotes%5Cpicture%5C%E6%8F%92%E5%85%A5%E6%96%B0%E9%94%AE2.jpg" style="zoom:50%;" />

#### 向一个父结点为3-结点的3-结点中插入新键

构造一个临时的4-结点，并分解4-结点，将它的中键插入父结点中。再次用这个中键创建一个4-结点，再次分解。

### 分解根结点

如果插入结点到根结点的路径上全是3-结点，则根结点最后是一个4-结点，分解4-结点，此时树高+1。

> 在一棵大小为N的2-3树中，查找和插入操作访问的结点必然不超过lgN个。

## 红黑二叉查找树

此为2-3树的实现方式，是一个数据结构。背后的思想是使用标准的二叉查找树和一些额外的信息来表示2-3树。我们就可以将二叉查找树高效的查找和2-3树高效的平衡插入算法结合起来。

将树中的链接分为两种：

- 红链接将两个2-结点连接起来成为一个3-结点
- 黑链接则是2-3树中的普通链接

<img src="I:%5CJAVA%5CJAVAstudy%5Cnotes%5Cpicture%5C%E7%BA%A2%E9%BB%91%E6%A0%91%E7%BB%93%E7%82%B9.jpg" style="zoom:50%;" />

### 定义

红黑树是指含有红黑链接并且满足以下条件的二叉查找树：

- 红链接均为左链接
- 没有任何一个结点同时和两条红链接相连
- 任意空链接到根结点的路径上的黑链接数量相同

### 颜色表示

每个结点都会有一条指向自己的链接（从它的父结点指向它），我们将链接的颜色保存在表示该结点的Node数据类型的boolean变量color中。如果指向它的链接是*红色*的则为true，*黑色*则是false。**约定空链接为黑色**。

```java
private static final boolean RED = true;
private static final boolean BLACK = false;

private class Node {
	Key key; // 键
	Value val; // 值
	Node left; // 左链接
	Node right; // 右链接
	int N; // 子树中的结点总数
	boolean color; // 指向该结点的链接颜色
	
	public Node(Key key, Value value, int N, boolean color) {
		this.key = key;
		this.val = value;
		this.N = N;
		this.color = color;
	}
}

private boolean isRed(Node node) {
	if(node == null) return false;
	return node.color == RED;
}
```

### 旋转

我们在实现的过程中可能会出现**红色的右链接**或者**两条连续的红链接**，但在操作完成之前都会进行**旋转**修复。**旋转操作会改变红链接的指向**。
首先假设有一条红色的右链接，需要被转化为左链接。这个操作叫做**左旋转**。
如图所示，这个操作就是将两个键中较大者变为根结点。

<img src="I:%5CJAVA%5CJAVAstudy%5Cnotes%5Cpicture%5C%E5%B7%A6%E6%97%8B%E8%BD%AC.jpg" style="zoom:67%;" />

```java
Node ratateLeft(Node h) {
	Node x = h.right;
	h.right = x.left;
	x.left = h;
	x.N = h.N;
	h.N = 1 + size(h.left) + size(h.right);
	x.color = h.color;
	h.color = RED;
	return x;
}
```

右旋转和左旋转一样，只需要将left和right互换即可。

### 向单个2-结点中插入新键

如果新键小于老键，只需要增加一个红色结点即可；如果新键大于老键，则需要进行左旋转将其修正。

### 向树底部的2-结点插入新键

和二叉查找树相同的方式向树插入结点，需要**总是用红链接将新结点和它的父结点相连**。

如果指向其新结点的链接是父结点的左链接，那么父结点就成为一个3-结点；如果指向其新结点的链接是父结点的右链接，则需要进行一次左旋转将其修正。

### 向一棵双键树（3-结点）树中插入新键

有三种情况：小于，介于，大于。

- 新键大于原树中的两个键，因此连接到3-结点中的右链接。此时需要**将两条链接变为黑色**，就得到了一棵由3个结点组成，高为2的平衡树，刚好对应一棵2-3树。
- 新键小于原树中的两个键，它会被连接到最左边的空链接，此时会产生两条连续的红链接。**这时我们需要将上层的红链接右旋转**，即可得到第一种情况。
- 新键介于原树中的两个键，此时会产生两条连续的红链接，一条左链接一条右链接，这时需要**将下层的右链接左旋转**即可得到第二种情况。

![](I:%5CJAVA%5CJAVAstudy%5Cnotes%5Cpicture%5C%E5%90%913-%E7%BB%93%E7%82%B9%E6%8F%92%E5%85%A5%E6%96%B0%E9%94%AE.jpg)

### 颜色转换

专门用一种方法flipColors()来转换一个结点的两个红色子结点的颜色。除了将子结点的颜色由红变黑，还将父结点的颜色由黑变红。

```java
void flipColors(Node h) {
	h.color = RED;
	h.left.color = BLACK;
	h.right.color = BLACK;
}
```

### 根结点总是黑色的

每次插入后都会将根结点设为黑色。当根结点由红色变为黑色时，树的高度就+1。

### 向树底部的3-结点插入新键

![](I:%5CJAVA%5CJAVAstudy%5Cnotes%5Cpicture%5C%E5%90%91%E6%A0%91%E5%BA%95%E9%83%A8%E6%8F%92%E5%85%A5%E6%96%B0%E9%94%AE.jpg)

### 插入部分总结

在沿着插入点到根结点的路径向上移动时在所经过的每个结点中顺序完成以下操作：

1. 如果右子结点是红色的而左子结点是黑色的，进行左旋转；
2. 如果左子结点是红色的且它的左子结点也是红色的，进行右旋转；
3. 如果左右子结点均为红色的，进行颜色转换。

## 插入部分实现

```java
public void put(Key key, Value val) {
	root = put(root, key, val);
	root.color = BLACK; // 根结点总是黑色
}

private Node put(Node h, Key key, Value val) {
	if(h == null) return new Node(key, val, 1, RED);
	int cmp = key.compareTo(h.key);
	if(cmp < 0) h.left = put(h.left, key, val);
	else if(cmp > 0) h.right = put(h.right, key, val);
	else h.val = val;
	
	if(isRed(h.right) && !isRed(h.left)) {
		h = rotateLeft(h);
	}
	if(isRed(h.left) && isRed(h.left.left)) {
		h = rotateRight(h);
	}
	if(isRed(h.left) && isRed(h.right)) {
		flipColors(h);
	}
	
	h.N = 1 + size(h.left) + size(h.right);
	return h;
}
```

## 删除操作

红黑树的删除操作也包括两部分工作：一查找目标结点；二删除后自平衡。

查找目标结点显然可以复用查找操作，当不存在目标结点时，忽略本次操作；当存在目标结点时，删除后就得做自平衡处理了。删除了结点后我们还需要找结点来替代删除结点的位置，不然子树跟父辈结点断开了，除非删除结点刚好没子结点，那么就不需要替代。

二叉树删除结点找替代结点有3种情情景：

- 情景1：若删除结点无子结点，直接删除
- 情景2：若删除结点只有一个子结点，用子结点替换删除结点
- 情景3：若删除结点有两个子结点，用后继结点（大于删除结点的最小结点）替换删除结点