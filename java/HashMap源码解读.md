# 🌱HashMap源码解读

### 属性字段

~~~java
public class HashMap<K,V> extends AbstractMap<K,V>
    implements Map<K,V>, Cloneable, Serializable { //实现了两次Map接口（没啥用） 泛型类型推断 也就是new HashMap<>()中<>里面可以不用加数据类型了
    //默认装填因子
    static final float DEFAULT_LOAD_FACTOR = 0.75f;
    //装填因子
    final float loadFactor;
    //数组扩容的阈值
    int threshold;
~~~

### 构造器

```java
//空构造器   
public HashMap() {
    this.loadFactor = DEFAULT_LOAD_FACTOR; // all other fields defaulted        
}
//自定义长度
public HashMap(int initialCapacity) {
    this(initialCapacity, DEFAULT_LOAD_FACTOR);
}
//有参构造
public HashMap(int initialCapacity, float loadFactor) {、
    //健壮性考虑
    if (initialCapacity < 0)throw new IllegalArgumentException("Illegal initial capacity: " +initialCapacity);
    
    if (initialCapacity > MAXIMUM_CAPACITY)
        initialCapacity = MAXIMUM_CAPACITY;
    if (loadFactor <= 0 || Float.isNaN(loadFactor))
        throw new IllegalArgumentException("Illegal load factor: " +loadFactor);
    this.loadFactor = loadFactor;
    this.threshold = tableSizeFor(initialCapacity);
}
//计算阈值 比cap大的最小的2的n次幂 例如cap传入3 返回4
 static final int tableSizeFor(int cap) {
    int n = cap - 1;
    n |= n >>> 1;
    n |= n >>> 2;
    n |= n >>> 4;
    n |= n >>> 8;
    n |= n >>> 16;
    return (n < 0) ? 1 : (n >= MAXIMUM_CAPACITY) ? MAXIMUM_CAPACITY : n + 1;
}   
```

### putVal方法

```java
   //put方法
    public V put(K key, V value) {return putVal(hash(key), key, value, false, true);}
    //put实际调用方法
    final V putVal(int hash, K key, V value, boolean onlyIfAbsent,boolean evict) {
        Node<K,V>[] tab; //哈希表的数组 
        Node<K,V> p;
        int n, i;//n表示数组的长度 i代表路由寻址结果
        //如果主数组为空，也就是说只有当第一次进行插入的时候才会初始化这个数组 延迟初始化 避免造成内存空间的浪费
        if ((tab = table) == null || (n = tab.length) == 0)  n = (tab = resize()).length;
       
        //计算在数组中位置，如果该位置没有元素
        if ((p = tab[i = (n - 1) & hash]) == null)
            //将这个节点的数据封装到一个Node中，并且将next置为null
            tab[i] = newNode(hash, key, value, null);
        
        else {//如果当前的位置有值了
            Node<K,V> e; K k;
            //表示桶位中的元素与插入的元素的key完全一致 就表示后续需要进行替换操作
            if (p.hash == hash && //首先判断的是key值 这里比较的是地址 如果为true就不经过equals方法
                ((k = p.key) == key || (key != null && key.equals(k))))
                e = p;
            //如果是树形的结构就插入到红黑树中
            else if (p instanceof TreeNode)
                e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
            //走到这里说明不是红黑树 而是插入到链表
            else { //链表的情况 并且链表的头元素与要插入的元素的key不一致
                for (int binCount = 0; ; ++binCount) {
                    //迭代到末尾 没有与要插入的key一致的node 就将这个节点插入到链表的尾部
                    if ((e = p.next) == null) {
                        p.next = newNode(hash, key, value, null);
                        //如果插入之后 达到树化的条件 就进行树化 跳出循环
                        if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
                            treeifyBin(tab, hash);
                        break;
                    }
                    //找到与之完全一致的节点 就跳出循环
                    if (e.hash == hash &&
                        ((k = e.key) == key || (key != null && key.equals(k))))
                        break;
                    p = e;
                }
            }
            //如果节点不是null 说明找到了与插入元素key完全一致的数据 需要进行替换 返回老值的value
            if (e != null) { // existing mapping for key
                V oldValue = e.value;
                if (!onlyIfAbsent || oldValue == null)
                    e.value = value;
                afterNodeAccess(e);
                return oldValue;
            }
        }
        //表示散列表结构被修改次数 替换value不算
        ++modCount;
        //如果加入数据之后大于阈值 就扩容
        if (++size > threshold) resize();
        afterNodeInsertion(evict);
        //插入元素 返回null
        return null;
    }

```
### 扰动函数

> 让key的hash值得高16位也参与运算 高16位与低十六位进行异或运算

```java
static final int hash(Object key) {
    int h;
    //key如果是null的时候hashcode为0 否则就把这个对象的hashcode的高16与低16位进行异或运算，结果位该对象的hashcode
    return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
}
```
### resize()方法

为了解决hash冲突导致链化影响查询效率的问题，扩容可以缓解该问题

```java
 final Node<K,V>[] resize() {
        //扩容前的哈希表
        Node<K,V>[] oldTab = table;
        //扩容前的哈希表的数组长度
        int oldCap = (oldTab == null) ? 0 : oldTab.length;
        //扩容前的阈值
        int oldThr = threshold;
        int newCap, newThr = 0; //新的capacity与阈值
     
        //说明散列表已经初始化过了，是一次正常的扩容
        if (oldCap > 0) {
            if (oldCap >= MAXIMUM_CAPACITY) {//如果数组的长度已经比最大的长度大 就将阈值设置为int的最大值
                threshold = Integer.MAX_VALUE;
                return oldTab;
            }
            //这里才是正常的情况 将原来的数组的长度左移一位（翻倍）并且赋值给newCap 如果小于最大值 并且扩容前的最大长度大于16 就把阈值设置为原来的两倍
            else if ((newCap = oldCap << 1) < MAXIMUM_CAPACITY &&
                     oldCap >= DEFAULT_INITIAL_CAPACITY)
                newThr = oldThr << 1; // double threshold
        }
        
        //这两个else都是当数组为空的时候 
        //这种就是初始化的时候传入了参数 或者构造的时候传入了一个map并且map中有值 所以导致数组为空但是有阈值
        else if (oldThr > 0) // initial capacity was placed in threshold
            newCap = oldThr;
        else {               // 初始化什么都没有设置
            newCap = DEFAULT_INITIAL_CAPACITY;
            newThr = (int)(DEFAULT_LOAD_FACTOR * DEFAULT_INITIAL_CAPACITY);
        }
        // 如果newThr没有赋值就重新进行计算     
        if (newThr == 0) {
            float ft = (float)newCap * loadFactor;
            newThr = (newCap < MAXIMUM_CAPACITY && ft < (float)MAXIMUM_CAPACITY ?
                      (int)ft : Integer.MAX_VALUE);
        }
        threshold = newThr;
        //扩容完成之后就需要将原来数组中的元素移动到新的数组中
        @SuppressWarnings({"rawtypes","unchecked"})
        Node<K,V>[] newTab = (Node<K,V>[])new Node[newCap];
        table = newTab;
       //说明这个数组中已经存在元素了 需要转移数据
        if (oldTab != null) {
            for (int j = 0; j < oldCap; ++j) {
                Node<K,V> e; //临时变量 表示当前的元素节点
                if ((e = oldTab[j]) != null) {
                    //方便JVM回收内存
                    oldTab[j] = null;
                    //第一种情况 当前的桶位中只有一个元素 因此直接进行存放 采用原来的方法计算新的数组下标位置
                    if (e.next == null)
                        newTab[e.hash & (newCap - 1)] = e;
                    //如果是树化的节点 走这个
                    else if (e instanceof TreeNode)
                        ((TreeNode<K,V>)e).split(this, newTab, j, oldCap);
                    //桶位已经形成链表
                    else { // preserve order
                        //低位表：存放在扩容之后的数组下标位置与当前数组链表的下标位置一致
                        Node<K,V> loHead = null, loTail = null;
                        //高位链表：存放在扩容之后的数组的下标位置位 当前数组下标位置+扩容前的数组的长度
                        Node<K,V> hiHead = null, hiTail = null;
                        Node<K,V> next;
                        do {
                            next = e.next;
                            if ((e.hash & oldCap) == 0) {
                                if (loTail == null)
                                    loHead = e;
                                else
                                    loTail.next = e;
                                loTail = e;
                            }
                            else {
                                if (hiTail == null)
                                    hiHead = e;
                                else
                                    hiTail.next = e;
                                hiTail = e;
                            }
                        } while ((e = next) != null);
                        if (loTail != null) {
                            loTail.next = null;
                            newTab[j] = loHead;
                        }
                        if (hiTail != null) {
                            hiTail.next = null;
                            newTab[j + oldCap] = hiHead;
                        }
                    }
                }
            }
        }
        return newTab;
    }
    final void treeifyBin(Node<K,V>[] tab, int hash) {
        int n, index; Node<K,V> e;
        //如果数组的长度小于64 就扩容 
        if (tab == null || (n = tab.length) < MIN_TREEIFY_CAPACITY)
            resize();
        //只有链表的大小大于8 并且数组的长度大于64才会进行树化
        else if ((e = tab[index = (n - 1) & hash]) != null) {
            TreeNode<K,V> hd = null, tl = null;
            do {
                TreeNode<K,V> p = replacementTreeNode(e, null);
                if (tl == null)
                    hd = p;
                else {
                    p.prev = tl;
                    tl.next = p;
                }
                tl = p;
            } while ((e = e.next) != null);
            if ((tab[index] = hd) != null)
                hd.treeify(tab);
        }
    }
```

### get函数

```java
public V get(Object key) {
    Node<K,V> e;
    return (e = getNode(hash(key), key)) == null ? null : e.value;
}
final Node<K,V> getNode(int hash, Object key) {
    //first ：桶位中的头元素
    Node<K,V>[] tab; Node<K,V> first, e; int n; K k;
    if ((tab = table) != null && (n = tab.length) > 0 &&
        (first = tab[(n - 1) & hash]) != null) {  
        
        if (first.hash == hash && // 定位出的元素就是要get的数据
            ((k = first.key) == key || (key != null && key.equals(k))))
            return first;
        //如果第一个元素不是 就往下查找
        if ((e = first.next) != null) {
            if (first instanceof TreeNode)
                return ((TreeNode<K,V>)first).getTreeNode(hash, key);
            do {
                if (e.hash == hash &&
                    ((k = e.key) == key || (key != null && key.equals(k))))
                    return e;
            } while ((e = e.next) != null);
        }
    }
    return null;
}
```

### remove方法

```java
public V remove(Object key) {
    Node<K,V> e;
    return (e = removeNode(hash(key), key, null, false, true)) == null ?
        null : e.value;
}
final Node<K,V> removeNode(int hash, Object key, Object value,
                           boolean matchValue, boolean movable) {
    Node<K,V>[] tab; Node<K,V> p; int n, index;
    //先判断元素是否存在
    if ((tab = table) != null && (n = tab.length) > 0 &&
        (p = tab[index = (n - 1) & hash]) != null) {
        Node<K,V> node = null, e; K k; V v;
        //第一种情况 当前的元素就是要删除的元素
        if (p.hash == hash &&
            ((k = p.key) == key || (key != null && key.equals(k))))
            node = p;
        
        else if ((e = p.next) != null) {
            //判断是不是红黑树
            if (p instanceof TreeNode)
                node = ((TreeNode<K,V>)p).getTreeNode(hash, key);
            //走链表的操作
            else {
                do { //找到符合条件的这个节点 直到e！=null
                    if (e.hash == hash &&
                        ((k = e.key) == key ||
                         (key != null && key.equals(k)))) {
                        node = e;
                        break;
                    }
                    p = e;
                } while ((e = e.next) != null);
            }
        }
        //如果node不为空说明找到了元素 说明找到了删除的元素
        if (node != null && (!matchValue || (v = node.value) == value ||
                             (value != null && value.equals(v)))) {
            if (node instanceof TreeNode) //删除树节点
                ((TreeNode<K,V>)node).removeTreeNode(this, tab, movable);
            else if (node == p) //桶位元素即为查找结果 将该元素的下一个元素放到桶位中
                tab[index] = node.next;
            else         
                p.next = node.next;
            ++modCount;
            --size;
            afterNodeRemoval(node);
            return node;
        }
    }
    return null;
}
```

