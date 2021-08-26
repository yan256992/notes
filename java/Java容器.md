# Java容器

## Collection

![image-20210822165625028](https://gitee.com/yan256992/cloudimages/raw/master/img/image-20210822165625028.png)

### Set

- TreeSet 内部基于红黑树实现，支持有序的操作，但是查找效率不如HashSet，HashSet的效率是O(1)，TreeSet是O(logN)
- HashSet 内部基于哈希表实现，支持快速查找，但不支持有序操作。
- LinkedHashSet 具有HashSet的查找效率并且内部使用双向链表维护元素的插入顺序

### List

- ArrayList 基于动态数组实现 支持随机访问
- Vector 和ArrayList类似 但是是线程安全的
- LinkedList 基于双向链表实现 只能顺序访问 但是可以快速的在链表中间插入和删除元素 ==LInkedList还可以用作栈 队列 和双向队列==

### Queue

- LinkedList 可以用它实现双向队列
- PriorityQueue 基于对结构实现 可以用它实现优先队列

## Map

![image-20210822170604968](https://gitee.com/yan256992/cloudimages/raw/master/img/image-20210822170604968.png)

- TreeMap 基于红黑树实现
- HashMap 基于哈希表实现
- HashTable 和HashMap类似 但是是线程安全的 这意味着当多个线程同时写入到HashTable不会导致数据不一致。但是现在都是使用COncurrentHashMap来支持线程安全 它的效率更高 因为引入了分段锁
- LinkedHashMap 使用双向链表来维护元素的顺序 顺序为插入顺序 或者最近最少使用

## 容器中的设计模式

#### 迭代器模式

![image-20210822171123886](https://gitee.com/yan256992/cloudimages/raw/master/img/image-20210822171123886.png)

Collection继承了Iterable接口 其中Iterator()方法能够产生一个Itertor对象，通过这个对象就可以迭代遍历Collection中的元素。从JDK1.5之后 可以使用foreach方法来遍历实现了Iterable接口的聚合对象

> List接口继承了Collection接口，Collection接口实现了Iterable接口。Iterable接口(Java.Lang)中有Iterator Itertor也是接口（Java.utils）

1. Iterator接口方法

![image-20210822172913688](https://gitee.com/yan256992/cloudimages/raw/master/img/image-20210822172913688.png)

2. Collection接口的方法

![image-20210822173125243](https://gitee.com/yan256992/cloudimages/raw/master/img/image-20210822173125243.png)

3. List接口方法

![image-20210822173356452](https://gitee.com/yan256992/cloudimages/raw/master/img/image-20210822173356452.png)

4. Set接口方法

![image-20210822173645109](https://gitee.com/yan256992/cloudimages/raw/master/img/image-20210822173645109.png)

#### 适配器模式

Java.util.ArrayList() asList可以将数组类型转换为List类型

```java
@SafeVarargs
public static <T> List<T> asList(T... a)
```

asList的参数为泛型的变长参数 不能使用基本数据类型作为参数 只能使用对应的包装类作为参数

```java
Integer[] arr = {1, 2, 3};
List list = Arrays.asList(arr);
```

或者使用如下方法

```java
List list = Arrays.asList(1, 2, 3);
```

## 源码解析

### ArrayList

1. 概览

基于数组实现的，所以支持随机访问，RandomAccess接口标识该类支持随机访问

```java
public class ArrayList<E> extends AbstractList<E>
        implements List<E>, RandomAccess, Cloneable, java.io.Serializable
```

数组的默认大小是10

```java
private static final int DEFAULT_CAPACITY = 10;
```

![image-20210822175722140](https://gitee.com/yan256992/cloudimages/raw/master/img/image-20210822175722140.png)

- 构造函数

  开始时候如果指定了数组的大小 那么就分配对应大小的数组 没有指定的分配一个空的对象数组

- add操作

  添加数据的时候会先执行ensureCapacityInternal方法保证容量足够，如果不够的话就进行扩容执行grow方法，新的数组的大小为原数组的1.5倍，执行的是newCapacity = oldCapacity+（oldcapacity>>1）.如果数组的旧的大小是偶数，则为1.5倍。否则为1.5倍-0.5

  数组扩容的时候会调用~~~Arrays.copyOf() 把原数组整个复制到新的数组中 这个操作的代价很高 因此最好在创建对象的时候就指定数组的大小 减少扩容的次数

  > https://www.cnblogs.com/baichunyu/p/12965241.html

  ~~~java
  public boolean add(E e) {
      //首先确保数组的大小足够 执行这个方法 默认的int值是0 所以开始插入的时候传入的参数是1
      ensureCapacityInternal(size + 1);  // Increments modCount!!
      elementData[size++] = e;
      return true;
  }
  
  private void ensureCapacityInternal(int minCapacity) {
      //首先看数组是否是空数组 如果是空的数组 那么参数就变成了默认的大小10 
      //否则就是传入的原参数
      //然后执行 ensureExplicitCapacity(minCapacity)
      if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA) {
          minCapacity = Math.max(DEFAULT_CAPACITY, minCapacity);
      }
      //如果不是第一次传参 就会直接执行这个方法
      ensureExplicitCapacity(minCapacity);
  }
  
  private void ensureExplicitCapacity(int minCapacity) {
      modCount++;
      // overflow-conscious code
      //这里的length不是size 所以在添加第一个数据的时候 必定会进入这个方法
      //但是添加2 3 4.。。10个数据的时候不会执行 因为此时的length是10 小于0 因此不会触发扩容机制
      if (minCapacity - elementData.length > 0)
          grow(minCapacity);
  }
  
  private void grow(int minCapacity) {
      // overflow-conscious code
      //数组扩容 第一次的时候 会触发第一个if判断条件
      int oldCapacity = elementData.length;
      int newCapacity = oldCapacity + (oldCapacity >> 1);
      if (newCapacity - minCapacity < 0)
          newCapacity = minCapacity;
      if (newCapacity - MAX_ARRAY_SIZE > 0)
          newCapacity = hugeCapacity(minCapacity);
      // minCapacity is usually close to size, so this is a win:
      elementData = Arrays.copyOf(elementData, newCapacity);
  }
  ~~~

  1. java中的length是针对数组来说的 查看数组的大小用length
  2. java中的length()是针对字符串的
  3. java中size（）方法是针对集合的

​      **System.arraycopy()和Arrays.copyOf()**

​         在ArrayList中大量使用了这个方法  包括扩容以及add(int index,E element)、toArray()方法

```java
     /**
 * 在此列表中的指定位置插入指定的元素。
 *先调用 rangeCheckForAdd 对index进行界限检查；然后调用 ensureCapacityInternal 方法保证capacity足够大；
 *再将从index开始之后的所有成员后移一个位置；将element插入index位置；最后size加1。
 */
public void add(int index, E element) {
    rangeCheckForAdd(index);

    ensureCapacityInternal(size + 1);  // Increments modCount!!
    //arraycopy()方法实现数组自己复制自己
    //elementData:源数组;index:源数组中的起始位置;elementData：目标数组；index + 1：目标数组中的起始位置； size - index：要复制的数组元素的数量；
    System.arraycopy(elementData, index, elementData, index + 1, size - index);
    elementData[index] = element;
    size++;
}
```

​           在ArrayList的remove方法中 是将index后面的数组元素都复制到index位置开始时间复杂度为O（N）符合数组的特性 删除和插入元素时间复杂度为O(N)

### CopyOnWriteArrayList

1. 读写分离

​    写操作是在一个复制的数组上进行的。读操作还是在原来的数组中进行。读写分离。互不影响

​    写操作需要加锁，防止并发写入的时候导致写入数据不安全

​    写操作结束的时候需要将原始数组指向新的复制数组

```java
public boolean add(E e) {
    final ReentrantLock lock = this.lock;
    //执行写操作的时候先加锁
    lock.lock();
    try {
        //先获取原数组 以及大小 然后将原数组复制到一个新的数组中 对新数组进行操作 执行完成之后在将新数组写入代替原数组
        Object[] elements = getArray();
        int len = elements.length;
        Object[] newElements = Arrays.copyOf(elements, len + 1);
        newElements[len] = e;
        setArray(newElements);
        return true;
    } finally {
        lock.unlock();
    }
}

final void setArray(Object[] a) {
    array = a;
}
```

- 为什么要复制一个数组进行写入操作

因为对于数组的操作不只是写入 还有读取 如果不复制的话容易导致并发读写问题 因为读的时候不需获取锁

如果不复制 那么读取的时候也需要加锁 此时就退化成了SyncronizedList

 **好处**

1. 写时加锁，不会产生并发读写问题，保证了写操作的安全性
2. 实际的写操作是在复制的新数组中进行的。而同一时刻的读操作 是在原数组进行的。所以读写不会产生问题
3. 新数组操作完成之后 将原数组替换 这里是通过volatiole关键字保证新数组的内存可见性

但是 CopyOnWriteArrayList 有其**缺陷**：

- 内存占用：在写操作时需要复制一个新的数组，使得内存占用为原来的两倍左右；
- 数据不一致：读操作不能读取实时性的数据，因为部分写操作的数据还未同步到读数组中。

所以 CopyOnWriteArrayList 不适合内存敏感以及对实时性要求很高的场景。

### LinkedLIst

1. 概览

基于双向链表实现，使用Node存储链表的节点信息

~~~java
private static class Node<E> {
    E item;
    Node<E> next;
    Node<E> prev;
}
~~~

每个链表存储了first 和last指针

```java
/**
 * Pointer to first node.
 * Invariant: (first == null && last == null) ||
 *            (first.prev == null && first.item != null)
 */
transient Node<E> first;

/**
 * Pointer to last node.
 * Invariant: (first == null && last == null) ||
 *            (last.next == null && last.item != null)
 */
transient Node<E> last;
```

![image-20210823120835967](https://gitee.com/yan256992/cloudimages/raw/master/img/image-20210823120835967.png)

2. 与ArrayList的区别

   一个是基于数组实现的，一个是基于链表实现的，数组对应的就是查找快 但是删除和插入时间复杂度较大

   链表不支持随机访问，插入删除只需要改变指针

### HashMap

**简介**

HashMap主要用来存储键值对，它是基于哈希表的Map接口实现的。是非线程安全的。

==HashMap存储null的键和值==，但是作为键只能存储一个。null作为值可以存储多个

JDK1.8之前由数组+链表组成，数组是HashMap的主体，链表则是为了解决Hash冲突存在的（拉链法）。1.8之后解决Hash冲突有了较大的变化。当链表的长度大于8的时候就会将链表转化为红黑树（转化为红黑树之前会==判断数组的长度==是否大于64 如果不大于 会先进行数组的扩容 而不是转换成红黑树），以减少搜索时间 

HashMap的默认初始化大小为16，之后每次扩充，容量变为原来的2倍，并且HashMap总是使用2的幂作为哈希表的大小

**扰动函数**

在HashMap存放元素的时候会有以下函数处理哈希值，这是JDK8的散列值扰动函数，用于优化散列效果

```java
static final int hash(Object key) {
    int h;
    return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);  //将高16位与低16位做异或运算
}
```

**为什么要使用扰动函数**

理论上来说hashcode的值是int 可以直接作为数组下标 并且不回出现碰撞 但是值的范围太大 初始化不可能这么大。初始化的大小是16 ，所以获取的hash不能直接作为下标，需要与数组的长度取模得到一个下标值。

那么，hashMap源码这里不只是直接获取哈希值，还进行了一次扰动计算，`(h = key.hashCode()) ^ (h >>> 16)`。把哈希值右移16位，也就正好是自己长度的一半，之后与原哈希值做异或运算，这样就混合了原哈希值中的高位和低位，增大了**随机性**。计算方式如下图

![image-20210823131023964](https://gitee.com/yan256992/cloudimages/raw/master/img/image-20210823131023964.png)

==扰动函数的存在就是为了增加随机性，让数据元素更加均衡的散列，减少碰撞==

**负载因子**

负载因子用来控制数据的疏密程度，太小导致数组的利用率低，存放的数据会很分散。0.75是官方给出的一个比较好的临界值

默认容量是16，当超过16*0.75=12个数据的时候就需要对数据进行扩容 扩容涉及到了rehash 复制数据等操作 所以非常消耗性能