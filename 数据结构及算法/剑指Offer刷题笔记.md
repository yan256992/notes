##  😋  剑指Offer刷题笔记

### 翻转链表

1.利用栈的先进后出 先将所有的节点加入到栈中 然后一个一个弹出

~~~java
class Solution {
    public ListNode reverseList(ListNode head) {
        if(head==null) return null;
        Stack<ListNode> stack = new Stack<>();
        ListNode cur = new ListNode();
        cur = head;
        while(cur!=null){
            stack.push(cur);
            cur = cur.next;
        }
        //因为之前已经判断了head不会为null 所以栈中一定有数据 所以可以直接取数据
        ListNode temp =  stack.pop();
        //把当前顶端的这个节点的地址赋值给head 后面直接返回head就可以了
        //temp此时就是工作指针了
        head = temp;
        while(!stack.isEmpty()){
            //如果栈不为空 就不断的弹出栈 将这个node加载到temp的后面
            ListNode node =  stack.pop();
            temp.next = node;
            temp = temp.next;
        }
        //最后一定要记得加上这个next =null 代表链表的尾部 否则会报错
        temp.next = null;
        return head;
    }
}
~~~
2. 迭代
~~~java
public ListNode reverseList(ListNode head) {
    //首先定义prev指向当前的节点的上一个节点
    //定义当前的节点指向head cur为工作指针
    ListNode prev = null;
    ListNode cur = head;
    while(cur!=null){
        //如果工作指针不为空 那么就将cur.next 保存下来 
        //因为后面需要把cur的next反向 所以下保存下来
        ListNode temp = cur.next;
        //将当前节点的next指向前一个节点 完成翻转
        cur.next = prev;
        //当前节点设置为prev
        prev = cur;
        //保存的下一个节点的值设置为cur
        cur = temp;
    }
    //最后跳出循环的时候是因为cur=null 所以返回的值是cur的上一个节点 也就是prev
    return prev;
}
~~~
  ### 删除链表中倒数第K个节点
  ~~~java
  public ListNode removeNthFromEnd(ListNode head, int n) {
      if(head == null) return null;
      ListNode fast = new ListNode();
      ListNode slow = new ListNode();
      slow = head;
      fast = slow;
      for(int i=0;i<n;i++){
          // if(fast==null) return null;
          fast = fast.next;
      }
      //如果是null则说明往后面移动了链表长度这么多 则说明倒数第n个刚好就是头结点 就是说删除头结点
      if(fast==null) return head.next;// 说明删除的是头结点
      while(fast.next!=null){ //这里一定要考虑fast.next是否为空 防止产生空指针异常
          fast =fast.next;
          slow = slow.next;
      }
      slow.next = slow.next.next;
      return  head;
  }
  ~~~
 ### LRU缓存的设计
 1. 自己实现双向链表和哈希表
 ~~~java
class LRUCache {
    //双向链表的节点定义 包括节点的k v 还有指向前一个节点和后一个节点的next 和prev
    class Node{
        Node next,prev;
        int key,value;
        public Node(int key,int value)
        {
            this.key = key;
            this.value = value;
        }
    }
    //双向链表定义
    class DoubleList{
        //记录链表大小的size 以及链表的头部和尾部 节点都是在头部和尾部中间的
        int size;
        Node head = new Node(0,0);
        Node tail = new Node(0,0);
        public DoubleList(){
            size = 0;
            head.next = tail;
            tail.prev = head;
        }
        //添加到头部
        public void addFirst(Node n){
            Node headNext = head.next;
            head.next = n;
            headNext.prev = n;
            n.prev = head;
            n.next = headNext;
            size++;
        }
        //删除最后一个元素
        public Node RemoveLast(){
            Node last = tail.prev;
            Remove(last);
            return last;
        }
        //删除元素
        public void Remove(Node node){
            node.prev.next = node.next;
            node.next.prev = node.prev;
            size --;
        }
        public int size(){
            return size;
        }
    }
    //HashMap可以加快查找的速度 K是node中的key value是一个链表中的节点
    private HashMap<Integer,Node> map;
    private DoubleList list;
    private int capacity; 
    public LRUCache(int capacity) {
        this.capacity = capacity;
        map = new HashMap<>();
        list = new DoubleList();
    }   
    public int get(int key) {
        //首先看是否存在map中 如果不存在说明没有
        //如果存在的话就返回这个值 并且插入到头部中 表示最近被访问过
        if(!map.containsKey(key)) return -1;
        int val =  map.get(key).value;
        put(key,val);
        return val;
    } 
    //对双向链表进行删除和增加都要记得将map中的key重新更新
    public void put(int key, int value) {
        // 先把新节点 x 做出来
        Node x = new Node(key, value);
        //如果存在Key    
        if (map.containsKey(key)) {
            // 删除旧的节点，新的插到头部
            list.Remove(map.get(key));
            list.addFirst(x);
            // 更新 map 中对应的数据
            map.put(key, x);
        } else { //不存在的时候要考虑是否超过了容量 超的话需要移除最后一个节点
            if (capacity == list.size()) {
                // 删除链表最后一个数据
                Node last = list.RemoveLast();
                map.remove(last.key);
            }
            // 直接添加到头部
            list.addFirst(x);
            map.put(key, x);
        }
    }
}
 ~~~
### 合并两个有序链表
~~~java
public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
    if(l1==null) return l2;
    else if (l2==null) return l1;
    else if(l1.val<l2.val) {
        l1.next = mergeTwoLists(l1.next,l2);
        return l1;
    }
    else{
        l2.next =  mergeTwoLists(l1,l2.next);
        return l2;
    }   
}
~~~

#### 56 - I. 数组中数字出现的次数 （中等）

> 一个整型数组 nums 里除两个数字之外，其他数字都出现了两次。请写程序找出这两个只出现一次的数字。要求时间复杂度是O(n)，空间复杂度是O(1)。
>
> 输入：nums = [4,1,4,6]
>         输出：[1,6] 或 [6,1]
>
> 来源：力扣（LeetCode）
>         链接：https://leetcode-cn.com/problems/shu-zu-zhong-shu-zi-chu-xian-de-ci-shu-lcof

```JAva
class Solution {
    //如果数组中只存在一个不相同的数 那么直接依次做与运算 就可以得到结果
    //但是这里有两个数字 那么可以按照一定的规则分成两部分 这个规则要求将这两个不同的数字分到两边
    public int[] singleNumbers(int[] nums) {
       //异或也存在交换率 也就是说 即使位置不相邻 相同的数最后还是会归为0
       int temp=0;
       for(int num : nums){
             temp^=num;
       }
       //根据上面的一轮异或操作之后 temp中存的值就是两个不相同的数字的异或结果
       //这个结果肯定是有一位为1的，在为1的这一位中 两个不相同的数字 一个为0 一个为1 （在当前这个位置上的是时候）
       //那么就可以以这个位置为一个标准 对数组进行划分 这两个数字肯定会被划分到两边 同时 剩下的数字都是相同的 那么肯定会被分到同一边去
       //在分割的两边分别进行异或操作 最后得到两个结果 就是两个不相同的值
       int mask = 1;
       //找到最低为的那个1 也就是这两个数字在该位开始不一样了 那么就可以以该为为标准做&运算
       while((temp&mask)==0){ 
           mask<<=1;//从1开始依次左移
       }

       int a=0,b=0;
       for(int num : nums){
           if((mask&num)==0){ //&为0 说明这个元素在那个位置上是0 就分到一边 下面说明这个位置是1 分到另一边
               a^=num;
           }
           else
             b^=num;
       }
       return new int[]{a,b};
    }
}
```

