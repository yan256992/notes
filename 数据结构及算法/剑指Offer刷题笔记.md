##  😋  剑指Offer刷题笔记

#### 翻转链表

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
  #### 删除链表中倒数第K个节点
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
 #### LRU缓存的设计
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
#### 合并两个有序链表
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

#### [56 - I. 数组中数字出现的次数 （中等）](https://leetcode-cn.com/problems/shu-zu-zhong-shu-zi-chu-xian-de-ci-shu-lcof)

> 一个整型数组 nums 里除两个数字之外，其他数字都出现了两次。请写程序找出这两个只出现一次的数字。要求时间复杂度是O(n)，空间复杂度是O(1)。  	  	输入：nums = [4,1,4,6]
>     	 输出：[1,6] 或 [6,1]

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

#### 剑指 Offer 56 - II. 数组中数字出现的次数 II

> 在一个数组 nums 中除一个数字只出现一次之外，其他数字都出现了三次。请找出那个只出现一次的数字。
>
> 示例 1：
>
> 输入：nums = [3,4,3,3]
>         输出：4
>         链接：https://leetcode-cn.com/problems/shu-zu-zhong-shu-zi-chu-xian-de-ci-shu-ii-lcof

~~~java
class Solution {
    public int singleNumber(int[] nums) {
        Map<Integer,Integer> map = new HashMap<>();
        for (int i = 0; i <nums.length ; i++) {
            if(map.containsKey(nums[i])) {
                int val = map.get(nums[i]);
                val++;
                map.put(nums[i],val);
            }
            else
                map.put(nums[i],1);
        }
        for (Map.Entry<Integer,Integer> entry : map.entrySet()){
            if (entry.getValue()==1){
                return entry.getKey();
            }
        }
        return -1;
    }
}
~~~

#### [剑指 Offer 45. 把数组排成最小的数](https://leetcode-cn.com/problems/ba-shu-zu-pai-cheng-zui-xiao-de-shu-lcof/)

> 输入一个非负整数数组，把数组里所有数字拼接起来排成一个数，打印能拼接出的所有数字中最小的一个。
>
> 示例 1:
>
> 输入: [10,2]
>         输出: "102"
>         示例 2:
>
> 输入: [3,30,34,5,9]
>         输出: "3033459"
>         链接：https://leetcode-cn.com/problems/ba-shu-zu-pai-cheng-zui-xiao-de-shu-lcof

~~~java
  public String minNumber(int[] nums) {
      //定义一个小根堆 比较的规则就是交换位置后两个数的大小 这里的泛型要定义为String 不然是integer的话比较久没有意义
    Queue<String> queue = new PriorityQueue<String>(new Comparator<String>() {
        @Override
        public int compare(String o1, String o2) {
            return (o1+o2).compareTo(o2+o1);
        }
    });
    //遍历数组中的数据 然后加入到优先队列 由于有限队列中存放的是String所以这里要加上""
    for (int num:nums){
        queue.add(num+"");
    }
    StringBuilder sb = new StringBuilder();
    while (!queue.isEmpty()){
        sb.append(queue.poll());
    }
    return sb.toString();
}
~~~

#### [剑指 Offer 47. 礼物的最大价值](https://leetcode-cn.com/problems/li-wu-de-zui-da-jie-zhi-lcof/)

> 在一个 m*n 的棋盘的每一格都放有一个礼物，每个礼物都有一定的价值（价值大于 0）。你可以从棋盘的左上角开始拿格子里的礼物，并每次向右或者向   下移动一格、直到到达棋盘的右下角。给定一个棋盘及其上面的礼物的价值，请计算你最多能拿到多少价值的礼物？ 
>
> 示例 1:
>
> 输入: 
>    [
>    [1,3,1],
>    [1,5,1],
>    [4,2,1]
>  ]
>   输出: 12
>   解释: 路径 1→3→5→2→1 可以拿到最多价值的礼物。

~~~java
class Solution {
    //思路分析
    //要获得价值的最大值 可以考虑使用动态规划 由于走的方向只有向右和向下 所以 最左边的一列和最上面的一行的数据时直接可以通过初始化的数组得到答案
    //其他的包含在中间的位置的最大路径可以通过两个方向得到 首先是通过它上面的位置往下移动 或者是通过左边的位置向右移动 选择二者之间较大的那一个 
    //每次选取的都是较大值 那么最后得到的就是最大的
    public int maxValue(int[][] grid) {
        int m =  grid.length;
        int n = grid[0].length;
        int[][] dp = new int[m][n];
        dp[0][0] = grid[0][0];
        //竖排的值初始化
        for (int i = 1; i <m ; i++) {
            dp[i][0] = dp[i-1][0]+grid[i][0];
        }
        //横排的初始化
        for (int i = 1; i < n; i++) {
            dp[0][i] = dp[0][i-1]+grid[0][i];
        }
        for (int i = 1; i <m ; i++) {
            for (int j = 1; j <n ; j++) {
                dp[i][j] = Math.max(dp[i-1][j],dp[i][j-1])+grid[i][j];
            }
        }
        return dp[m-1][n-1];
    }
}
~~~

#### [剑指 Offer 63. 股票的最大利润](https://leetcode-cn.com/problems/gu-piao-de-zui-da-li-run-lcof/)

> 假设把某股票的价格按照时间先后顺序存储在数组中，请问买卖该股票一次可能获得的最大利润是多少？
>
> 示例 1:
>
> 输入: [7,1,5,3,6,4]
>         输出: 5
>         解释: 在第 2 天（股票价格 = 1）的时候买入，在第 5 天（股票价格 = 6）的时候卖出，最大利润 = 6-1 = 5 。
>         注意利润不能是 7-1 = 6, 因为卖出价格需要大于买入价格。

```java
class Solution {
    // 定义一个dp数组 数组dp[i]表示的是以prices[i]结尾的数的最大利润
    //最大利润 就是当前这一天的价值减去前面的最小值 和 前一天的最大利润 中的最大的那一个
    public int maxProfit(int[] prices) {
        //如果长度小于等于1 利润为0
        if(prices.length <= 1) return 0;
        //初始化最小值就是第一个数
        int min = prices[0];
        //定义同样大小的数组代表每一天的最大利润
        int[] dp = new int[prices.length];
        //初始化第一天的利润为0
        dp[0] = 0;
        //遍历数组 不断的更行最小值和dp[i]
        for(int i =1;i<prices.length;i++){
            min = Math.min(prices[i],min); //最小值更新
            dp[i] = Math.max(prices[i]-min,dp[i-1]); //更新每一天的最大值 从当前这天的值减去最小值和 前一天的利润中找最大的那一个
        }
        return dp[prices.length-1];
    }
}
```

#### [剑指 Offer 12. 矩阵中的路径](https://leetcode-cn.com/problems/ju-zhen-zhong-de-lu-jing-lcof/)

![image-20210910155247353](https://gitee.com/yan256992/cloudimages/raw/master/img/image-20210910155247353.png)

```java
class Solution {
    public boolean exist(char[][] board, String word) {
        //将整个二维数组的行列求出来
        int i = board.length;
        int j = board[0].length;
        for (int k = 0; k < i ; k++) {
            for (int l = 0; l <j ; l++) {
                //遍历整个二维数组，从0,0出开始 如果从某一个位置开始遍历过后返回为true说明有路径 就直接返回 否则的话 就继续在下一个位置开始搜索
                if(dfs(board,k,l,word,0)) return true;
            }
        }
        return false;
    }
    //回溯搜索
    //传入的参数包括有 字符数组 以及当前的位置（行，列） 字符串 字符串的字符索引位置
    private boolean dfs(char[][] board, int i, int j, String word, int k) {
            char[] ans = word.toCharArray();
            //先判断边界条件 什么时候这个函数会返回false 具体而言就是数组的下标越界或者不符合规定  或者是当前位置时候 字符串的字符和字符数组中的值          不相同
           if(i>=board.length || i<0 || j>=board[0].length || j<0 || board[i][j]!=ans[k]) return false;
           //如果各方面符合 就判断当前这个位置的索引是否已经是字符串的最后一个位置 如果是就返回true 说明存在路径
           if(k ==  word.length()-1) return true;
           //如果还是不符合 就继续向周围扩散搜索 这时候由于这个位置已经搜索过了 所以要把这个位置设置为访问了 并且要保存这个位置的字符 
           // 因为这次搜索不成功的话 不能影响下一次的搜索 所以在返回之前 要把这个值设置回来 
           char temp = board[i][j];
           board[i][j] = '\0';
           boolean res = dfs(board,i-1,j,word,k+1)||dfs(board,i+1,j,word,k+1)||dfs(board,i,j-1,word,k+1)||dfs(board,i,j+1,word,k+1);
           board[i][j] = temp;
           return res;
    }
}
```

#### [剑指 Offer 35. 复杂链表的复制](https://leetcode-cn.com/problems/fu-za-lian-biao-de-fu-zhi-lcof/)

> 请实现 copyRandomList 函数，复制一个复杂链表。在复杂链表中，每个节点除了有一个 next 指针指向下一个节点，还有一个 random 指针指向链表中的任意节点或者 null。
>

![image-20210912102953537](https://gitee.com/yan256992/cloudimages/raw/master/img/image-20210912102953537.png)

```java
class Solution {
    public Node copyRandomList(Node head) {
        if(head==null) return null;
        //声明一个工作指针
        Node cur = head;
        //map用来保存这个链表节点 单纯的实现链表节点的复制就是靠map
        Map<Node,Node> map = new HashMap<>();
        //当节点不为空的时候就不断进行插入 实现链表节点的复制
        while(cur!=null){
            map.put(cur,new Node(cur.val));
            cur=cur.next;
        }
        cur = head; //将工作指针恢复到head的状态
        while(cur!=null){
            //将原节点的next和random给新链表
            map.get(cur).next = map.get(cur.next);
            map.get(cur).random = map.get(cur.random);
            cur = cur.next;
        }
        return map.get(head);
        
    }
}
```

#### [剑指 Offer 36. 二叉搜索树与双向链表](https://leetcode-cn.com/problems/er-cha-sou-suo-shu-yu-shuang-xiang-lian-biao-lcof/)

> 输入一棵二叉搜索树，将该二叉搜索树转换成一个排序的循环双向链表。要求不能创建任何新的节点，只能调整树中节点指针的指向。

<img src="https://gitee.com/yan256992/cloudimages/raw/master/img/image-20210912103535811.png" alt="image-20210912103535811" style="zoom: 50%;" /><img src="https://gitee.com/yan256992/cloudimages/raw/master/img/image-20210912103549435.png" alt="image-20210912103549435" style="zoom: 50%;" />

```java
class Solution {
    Node head, pre;
    public Node treeToDoublyList(Node root) {
        if(root==null) return null;
        dfs(root);

        pre.right = head;
        head.left =pre;//进行头节点和尾节点的相互指向，这两句的顺序也是可以颠倒的

        return head;

    }

    public void dfs(Node cur){
        if(cur==null) return;
        dfs(cur.left);

        //pre用于记录双向链表中位于cur左侧的节点，即上一次迭代中的cur,当pre==null时，cur左侧没有节点,即此时cur为双向链表中的头节点
        if(pre==null) head = cur;
        //反之，pre!=null时，cur左侧存在节点pre，需要进行pre.right=cur的操作。
        else pre.right = cur;
       
        cur.left = pre;//pre是否为null对这句没有影响,且这句放在上面两句if else之前也是可以的。

        pre = cur;//pre指向当前的cur
        dfs(cur.right);//全部迭代完成后，pre指向双向链表中的尾节点
    }
}
```

#### [剑指 Offer 32 - III. 从上到下打印二叉树 III](https://leetcode-cn.com/problems/cong-shang-dao-xia-da-yin-er-cha-shu-iii-lcof/)

> 请实现一个函数按照之字形顺序打印二叉树，即第一行按照从左到右的顺序打印，第二层按照从右到左的顺序打印，第三行再按照从左到右的顺序打印，其他行以此类推。

![image-20210912150331446](https://gitee.com/yan256992/cloudimages/raw/master/img/image-20210912150331446.png)

```java
class Solution {
    public List<List<Integer>> levelOrder(TreeNode root) {
        Queue<TreeNode> queue = new LinkedList<>();
        List<List<Integer>> res = new ArrayList<>();
        if(root!=null){
              queue.add(root);
        }
        int k = 1;
        while(!queue.isEmpty()){
            //这里使用了双端队列 也就是说可以在两边进行插入删除操作 这样就可以实现奇数偶数行的不同方向
            //如果是奇数行就顺序插入 也就是插入到后面 如果是偶数的话就从头插入即可
            LinkedList<Integer> temp  = new LinkedList<>();
            //队列里面的都是当前这一行的所有元素 遍历这一行的所有元素 
            for(int i=queue.size();i > 0 ;i --){
                TreeNode node = queue.poll();
                if(k%2==1) { //判断奇偶
                    temp.addLast(node.val);
                }
                else temp.addFirst(node.val);
                if(node.left!=null) queue.add(node.left);
                if(node.right!=null) queue.add(node.right);
            }
            res.add(temp);
            k++;
        }
        return res;

    }
}
```

#### [剑指 Offer 38. 字符串的排列](https://leetcode-cn.com/problems/zi-fu-chuan-de-pai-lie-lcof/)

> 输入一个字符串，打印出该字符串中字符的所有排列。
>
> 你可以以任意顺序返回这个字符串数组，但里面不能有重复元素
>
> 示例:
>
> 输入：s = "abc"
>         输出：["abc","acb","bac","bca","cab","cba"]

由于是不限制输出顺序的 所结果集可以使用Set来进行存储

```java
class Solution {
    public  String[] permutation(String s) {
        //来标识字符是否被访问过
        boolean[] visited = new boolean[s.length()];
        //初始的时候全部未访问
        Arrays.fill(visited,false);
        //将字符串转换为字符数组
        char[] chars = s.toCharArray();、
        //存储每一次的结果
        String temp ="";
        //存储最后返回的结果 
        Set<String> res = new HashSet<>();
        backtrack(chars,temp,visited,res);
        //将Set转换为数组 数组的大小为res的size
        return res.toArray(new String[res.size()]);
    }

    private  void backtrack(char[] chars, String temp, boolean[] visited, Set<String> res) {
        //结束条件 如果存储临时变量的数组大小和原数组一样 就结束
        if(chars.length == temp.length()){
            res.add(temp);
            return;
        }
        //遍历整个数组
        for (int i = 0; i < chars.length; i++) {
            //如果被访问过 就继续下一次循环
            if(visited[i]) continue;
            //将当前的位置设置为访问过
            visited[i]=true;
            //继续下一次的回溯
            backtrack(chars,temp+chars[i],visited,res);
            //回溯结束 撤销选择 设置为未访问
            visited[i]=false;
        }
    }
}
```

**回溯法模板**

```java
private void backtrack("原始参数") {
    //终止条件(递归必须要有终止条件)
    if ("终止条件") {
        //一些逻辑操作（可有可无，视情况而定）
        return;
    }

    for (int i = "for循环开始的参数"; i < "for循环结束的参数"; i++) {
        //一些逻辑操作（可有可无，视情况而定）

        //做出选择

        //递归
        backtrack("新的参数");
        //一些逻辑操作（可有可无，视情况而定）

        //撤销选择
    }
}
作者：sdwwld
链接：https://leetcode-cn.com/problems/zi-fu-chuan-de-pai-lie-lcof/solution/shu-ju-jie-gou-he-suan-fa-hui-su-suan-fa-11gk/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

