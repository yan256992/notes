##  🐑Java集合概述

### Collection

![image-20210822165625028](https://gitee.com/yan256992/cloudimages/raw/master/img/image-20210822165625028.png)

#### Set

- TreeSet 内部基于红黑树实现，支持有序的操作，但是查找效率不如HashSet，HashSet的效率是O(1)，TreeSet是O(logN)
- HashSet 内部基于哈希表实现，支持快速查找，但不支持有序操作。
- LinkedHashSet 具有HashSet的查找效率并且内部使用双向链表维护元素的插入顺序

Set就相当于一个不存储Value的Map，存储的元素不重复，因此，`Set常被用于元素的去重`

`Set`用于存储不重复的元素集合，它主要提供以下几个方法：

- 将元素添加进`Set<E>`：`boolean add(E e)`
- 将元素从`Set<E>`删除：`boolean remove(Object e)`
- 判断是否包含元素：`boolean contains(Object e)`

因为放入的key和Map类似，因此也需要正确实现`equals()`和`hashCode()`方法

如果需要有序的集合就需要使用`TreeSet`

![image-20210831170058011](https://gitee.com/yan256992/cloudimages/raw/master/img/image-20210831170058011.png)

#### List





> List是一种有序列表，他的行为和数组几乎完全相同，每个元素可以通过索引获取自己的位置

- ArrayList 基于动态数组实现 支持随机访问
- Vector 和ArrayList类似 但是是线程安全的
- LinkedList 基于双向链表实现 只能顺序访问 但是可以快速的在链表中间插入和删除元素 ==LInkedList还可以用作栈 队列 和双向队列==

##### List接口方法

- 在末尾加一个元素：`boolean add(E e)`

- 在指定的索引添加元素：`boolean add(int index, E e)`

- 删除指定索引的元素：`E remove(int index)`

- 删除某个元素：`boolean remove(Object o)`

- 获取指定索引的元素：`E get(int index)`

- 获取链表的大小：`int size()`

- 转换成数组：`object[] toArray(T[] t)` 

- 是否包含某个元素：`boolean contains(Object o)`

- 返回某个元素的索引：`int indexOf(Object o)`

- 

  ```java
  List<Integer> list = List.of(12, 34, 56);
  Integer[] array = list.toArray(new Integer[3]);
  ```

List允许添加重复的元素，同时可以添加`null`

##### 遍历List

1. 采用for循环

   ```java
   public class Main {
       public static void main(String[] args) {
           List<String> list = List.of("apple", "pear", "banana");
           for (int i=0; i<list.size(); i++) {
               String s = list.get(i);
               System.out.println(s);
           }
       }
   }
   ```

   但是这种方式对于LinkedList来说并不高效，索引越大，访问速度越慢

   所以要使用迭代器`Iterator`来访问LIst，Iterator本身也是一个对象，但是他是在List嗲用iterator()方法的时候创建的

   ```java
   public class Main {
       public static void main(String[] args) {
           List<String> list = List.of("apple", "pear", "banana");
           for (Iterator<String> it = list.iterator(); it.hasNext(); ) {
               String s = it.next();
               System.out.println(s);
           }
       }
   }
   //通过Iterator遍历List永远是最高效的方式。并且，由于Iterator遍历是如此常用，所以，Java的for each循环本身就可以帮我们使用Iterator遍 历。把上面的代码再改写如下：
   public class Main {
       public static void main(String[] args) {
           List<String> list = List.of("apple", "pear", "banana");
           for (String s : list) {
               System.out.println(s);
           }
       }
   }
   ```

   

#### Queue

- LinkedList 可以用它实现双向队列
- PriorityQueue 基于对结构实现 可以用它实现优先队列

```Java
package swpu.集合;
import java.util.LinkedList;
import java.util.Queue;
/**
 * 功能描述：
 * @Author: ygq
 * @Date: 2021/8/31 17:02
 */
public class QueueTest {
    public static void main(String[] args) {
        Queue<Integer> queue = new LinkedList<>();
        //offer也可以 offer是Queue的方法
        queue.add(1);
        queue.add(2);
        queue.add(3);
        queue.add(4);
        queue.add(5);
        System.out.println(queue.element()); //获取队首元素 但是不删除 区别就是这个为空的话抛出异常
        System.out.println(queue.peek());    //同上
        System.out.println("size:"+queue.size());
        System.out.println(queue.poll());  //获取元素并删除
        System.out.println("size:"+queue.size());
    }
}
```

#### PriorityQueue优先队列

和普通的队列类似 但是不同的是每次出队的元素都是当前优先级最高的元素，因此，如果使用PriorityQueue的时候必须给每个元素定义一个优先级

```Java
public static void main(String[] args) {
    Queue<String> pq = new PriorityQueue<>();
    pq.offer("Apple");
    pq.offer("Pear");
    pq.offer("Banana");
    Iterator<String> iterator = pq.iterator();
    System.out.println(pq.poll());//Apple
    System.out.println(pq.poll());//Banana
    System.out.println(pq.poll());//Pear
    System.out.println(pq.poll());//null
}
```

应用

```java
package swpu.集合;
import java.util.*;
/**
 * 功能描述：
 * @Author: ygq
 * @Date: 2021/8/31 17:13
 */
public class PQTest {
    public static void main(String[] args) {
        Queue<Person> queue = new PriorityQueue<>(new MyComparable());
        queue.offer(new Person("张三","B1"));
        queue.offer(new Person("李四","A1"));
        queue.offer(new Person("王五","B2"));
        queue.offer(new Person("Tina","B2"));
        queue.offer(new Person("赵六","V1"));
        queue.offer(new Person("Tom","V5"));
        queue.offer(new Person("Alice","A3"));
        System.out.println(queue.poll());
        System.out.println(queue.poll());
        System.out.println(queue.poll());
        System.out.println(queue.poll());
        System.out.println(queue.poll());
        System.out.println(queue.poll());
        System.out.println(queue.poll());
//        Person{name='赵六', Level='V1'}
//        Person{name='Tom', Level='V5'}
//        Person{name='李四', Level='A1'}
//        Person{name='Alice', Level='A3'}
//        Person{name='张三', Level='B1'}
//        Person{name='王五', Level='B2'}
//        Person{name='Tina', Level='B2'}

    }
}
class MyComparable implements Comparator<Person> {

    @Override
    public int compare(Person o1, Person o2) {
          if(o1.Level.charAt(0)==o2.Level.charAt(0)){
              return o1.Level.compareTo(o2.Level);
          }
          if(o1.Level.charAt(0)=='V'){
              return -1;
          }
          else if (o2.Level.charAt(0)=='V'){
              return 1;
          }
          else
              return o1.Level.compareTo(o2.Level);
    }
}
class Person{
    public final String name;
    public final String Level;

    public Person(String name, String level) {
        this.name = name;
        Level = level;
    }

    @Override
    public String toString() {
        return "Person{" +
                "name='" + name + '\'' +
                ", Level='" + Level + '\'' +
                '}';
    }
}
```

#### DeQueue

双端队列，允许两边都进，两头都出，这种就叫双端队列Double Ended Queue

`Queue`和`Deque`出队和入队的方法：

|                    | Queue                  | Deque                           |
| :----------------- | :--------------------- | :------------------------------ |
| 添加元素到队尾     | add(E e) / offer(E e)  | addLast(E e) / offerLast(E e)   |
| 取队首元素并删除   | E remove() / E poll()  | E removeFirst() / E pollFirst() |
| 取队首元素但不删除 | E element() / E peek() | E getFirst() / E peekFirst()    |
| 添加元素到队首     | 无                     | addFirst(E e) / offerFirst(E e) |
| 取队尾元素并删除   | 无                     | E removeLast() / E pollLast()   |
| 取队尾元素但不删除 | 无                     | E getLast() / E peekLast()      |

```java
public class Main {
    public static void main(String[] args) {
        Deque<String> deque = new LinkedList<>();
        deque.offerLast("A"); // A
        deque.offerLast("B"); // A <- B
        deque.offerFirst("C"); // C <- A <- B
        System.out.println(deque.pollFirst()); // C, 剩下A <- B
        System.out.println(deque.pollLast()); // B, 剩下A
        System.out.println(deque.pollFirst()); // A
        System.out.println(deque.pollFirst()); // null
    }
}
```



### Map

![image-20210822170604968](https://gitee.com/yan256992/cloudimages/raw/master/img/image-20210822170604968.png)

- TreeMap 基于红黑树实现
- HashMap 基于哈希表实现
- HashTable 和HashMap类似 但是是线程安全的 这意味着当多个线程同时写入到HashTable不会导致数据不一致。但是现在都是使用COncurrentHashMap来支持线程安全 它的效率更高 因为引入了分段锁
- LinkedHashMap 使用双向链表来维护元素的顺序 顺序为插入顺序 或者最近最少使用

`Map<K, V>`是一种键-值映射表，当我们调用`put(K key, V value)`方法时，就把`key`和`value`做了映射并放入`Map`。当我们调用`V get(K key)`时，就可以通过`key`获取到对应的`value`。如果`key`不存在，则返回`null`。和`List`类似，`Map`也是一个接口，最常用的实现类是`HashMap`。

如果只是想查询某个`key`是否存在，可以调用`boolean containsKey(K key)`方法。

`put()`方法的签名是`V put(K key, V value)`，如果放入的`key`已经存在，`put()`方法会返回被删除的旧的`value`

:warning: Map中不存在重复的key，因为放入相同的key，只会把原有的key-value对应的value给替换掉。但是可以存在重复的value

#### 遍历Map

```Java
// 遍历keySet获取对应key的value
public static void main(String[] args) {
        Map<String,Integer> map = new HashMap<>();
        map.put("apple",1);
        map.put("pear",2);
        map.put("orange",3);
        map.put("banana",4);
        for (String key: map.keySet()){
            System.out.println(map.get(key));
        }
    }
//同时获取Key value
    public static void main(String[] args) {
        Map<String,Integer> map = new HashMap<>();
        map.put("apple",1);
        map.put("pear",2);
        map.put("orange",3);
        map.put("banana",4);
        for (Map.Entry<String,Integer> entry:map.entrySet()){
            System.out.println(entry.getKey()+"---->"+entry.getValue());
        }
//        orange---->3
//        banana---->4
//        apple---->1
//        pear---->2
    }
```

我们放入`Map`的`key`是字符串`"a"`，但是，当我们获取`Map`的`value`时，传入的变量不一定就是放入的那个`key`对象。

换句话讲，两个`key`应该是内容相同，但不一定是同一个对象。测试代码如下：

```java
public class Main {
    public static void main(String[] args) {
        String key1 = "a";
        Map<String, Integer> map = new HashMap<>();
        map.put(key1, 123);

        String key2 = new String("a");
        map.get(key2); // 123

        System.out.println(key1 == key2); // false
        System.out.println(key1.equals(key2)); // true
    }
}
```

在Map的内部，对key的比较是通过`equals`方法实现的，我们经常使用`String`作为`key`，因为`String`已经正确覆写了`equals()`方法。但如果我们放入的`key`是一个自己写的类，就必须保证正确覆写了`equals()`方法。

因此，正确使用`Map`必须保证：

1. 作为`key`的对象必须正确覆写`equals()`方法，相等的两个`key`实例调用`equals()`必须返回`true`；
2. 作为`key`的对象还必须正确覆写`hashCode()`方法，且`hashCode()`方法要严格遵循以下规范：

- 如果两个对象相等，则两个对象的`hashCode()`必须相等；
- 如果两个对象不相等，则两个对象的`hashCode()`尽量不要相等

#### TreeMap

我们知道Map的内部是无序的，也即遍历Map的时候顺序是不可测的。但是TreeMap在内部会对Key进行排序

![image-20210831165214407](https://gitee.com/yan256992/cloudimages/raw/master/img/image-20210831165214407.png)

使用TreeMap的时候放入的key必须实现Comparable接口。String、Integer这些已经实现了这个接口，因此可以直接作为key使用。如果自定义的Key没有实现则必须自定义实现排序规则

```Java
Map<Person, Integer> map = new TreeMap<>(new Comparator<Person>() {
    	public int compare(Person p1, Person p2) {
		return p1.name.compareTo(p2.name);
}
});
```

### 容器中的设计模式

#### 迭代器模式

![image-20210822171123886](https://gitee.com/yan256992/cloudimages/raw/master/img/image-20210822171123886.png)

Collection继承了Iterable接口 其中Iterator()方法能够产生一个Itertor对象，通过这个对象就可以迭代遍历Collection中的元素。从JDK1.5之后 可以使用foreach方法来遍历实现了Iterable接口的聚合对象

> List接口继承了Collection接口，Collection接口实现了Iterable接口。Iterable接口(Java.Lang)中有Iterator Itertor也是接口（Java.utils）

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

### 源码解析

#### ArrayList

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

#### CopyOnWriteArrayList

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

#### LinkedLIst

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

#### HashMap

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

### References

- 廖雪峰：https://www.liaoxuefeng.com/
- JavaGuide：https://snailclimb.gitee.io/javaguide/#/
- CS-WIKI：https://veal98.gitee.io/cs-wiki/#/

- CS-Notes：http://www.cyc2018.xyz/