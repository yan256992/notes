## 🐾  Java常用类

### 内部类

> 在一个类的内部再定义一个完整的类

1. 成员内部类
2. 静态内部类
3. 局部内部类
4. 匿名内部类

**特点**

- 编译之后可以生成独立的字节码文件.class
- 可以访问外部的私有成员变量，而不破坏封装
- 可以为外部类提供必要的功能组件

#### 成员内部类

> 在类的内部定义，与成员变量，实例方法同级别的类
>
> 外部类的一个实例部分，创建内部类对象时，必须依赖外部类对象

```java
Outer out = new Outer();
Inner in = out.new Inner();
```

内部类与外部类的成员属性重名的时候 优先访问内部类的变量，如果想要在内部类中访问外部类的变零需要加`外部类.this.变量名`

:warning: 成员内部类不能定义静态成员 但是可以定义静态常量 (加了final)

#### 静态内部类

不依赖外部对象，可直接创建或通过类访问

```Java
public class 内部类 {
        private  String name = "zhangsan";
        //静态内部类 和外部类相同
        static class  Inner{
            private String Address = "chengdu";
            private static int count = 1000;
            public void show(){
                //访问外部类的属性
                内部类 内部类 = new 内部类();
                System.out.println(内部类.name);
                System.out.println(Inner.count);
            }
        }
    public static void main(String[] args) {
        //创建静态内部类
        内部类.Inner inner = new 内部类.Inner();
        inner.show();
    }
}
```

#### 局部内部类

定义在外部类的方法中 作用范围仅限于当前的方法中使用，并且不能加任何的访问修饰符 直接在方法中写class 类名{   }；

```Java
package Java基础.常用类;
/**
 * 功能描述：
 * @Author: ygq
 * @Date: 2021/8/30 16:13
 */
public class Outer {
    private String name = "蜡笔小旧";
    public void show(){
        String Address = "成都";
        class Inner{
            private String pwd = "aaa";
            public void show2(){
                //访问外部类的属性
                System.out.println(Outer.this.name);
                //访问局部变量 1.7之后变量必须要求是常量 1.8自动加final
                System.out.println(Address);
                //访问内部类的属性
                System.out.println(this.pwd);
            }
        }
        //创建局部类对象 并调用其方法
        Inner inner = new Inner();
        inner.show2();
    }

    public static void main(String[] args) {
        Outer outer = new Outer();
        outer.show();
    }
}
```

#### 匿名内部类

没有类名的局部内部类（一切特征与局部内部类相同）

必须继承父类或实现一个接口

定义类、实现类、创建对象的语法合并、只能创建一个该类的对象

减少代码量 但是可读性较差

```java
// 使用匿名内部类优化（相当于创建了一个局部内部类）
Usb usb = new Usb(){ // Usb为一个接口
  @Override
  public void service(){
    sout("连接电脑成功，fan开始工作")
  }
};
usb.service();
```

### 包装类

> 基本数据类型所对应的引用数据类型 

```Java
public static void main(String[] args){
  // 装箱， 基本类型 → 引用类型
  // 基本类型
  int num1 = 18;
  // 使用Integer类创建对象
  Integer integer1 = new Integer(num1);
  Integer integer2 = Integer.valueOf(num1);
  
  // 拆箱， 引用类型 → 基本类型
  Integer integer3 = new Integer(100);
  int num2 = integer3.intValue();
  
  // 上述为jdk1.5之前方法，之后提供了自动装箱拆箱
  int age = 30;
  // 自动装箱
  Integer integer4 = age;
  // 自动拆箱
  int age2 = integer4;
  
  // 基本类型和字符串之间转换
  // 1. 基本类型转成字符串
  int n1 = 100;
  // 1.1 使用+号
  String s1 = n1 + "";
  // 1.2 使用Integer中的toString()方法
  String s2 = Integer.toString(n1);
  String s2 = Integer.toString(n1, x); // x为进制要求
  
  // 2. 字符串转成基本类型
  String str = "150";
  // 使用Integer.parseXXX();
  int n2 = Integer.parseInt(str);
  
  // boolean 字符串形式转成基本类型，"true" ---> true 非“true ———> false
  String str2 = "true";
  boolean b1 = Boolean.parseBoolean(str2);
}
```

:taco: Java预先对-128~127创建了整数的缓冲池 当直接使用Integer声明一个对象的时候 这两个对象的地址是一样的Integer ii = 100这种方式

```Java
public static void main(String[] args){
  // 面试题
  Integer integer1 = new Integer(100);
  Integer integer2 = new Integer(100);
  sout(integer1 == integer2); // false
  
  Integer integer3 = new Integer(100);// 自动装箱
  // 相当于调用 Integer.valueOf(100);
  Integer integer4 = new Integer(100);
  sout(integer3 == integer4); // true
  
  Integer integer5 = new Integer(200);// 自动装箱
  Integer integer6 = new Integer(200);
  sout(integer5 == integer6); // false
  
  // 因为缓存区数组 [-128, 127] 在这之内地址一样
}
```

### String类

> String类不可以被继承，底层是一个不可变的数组

字符串是常量，创建之后不再改变，存储在字符串常量池中，可以共享

- `String s = "Hello";`产生一个对象，字符串池中存储
- `String s = new String("Hello");` 产生两个对象，**堆、池**各一个

**euqals方法**

```Java
 public boolean equals(Object anObject) {
        if (this == anObject) { //首先比较两个对象的地址指向相同的时候 直接返回 true
            //自己和自己比较的时候也返回true
            return true;
        }
        if (anObject instanceof String) { //先看看传进来的对象是不是String类型
            String anotherString = (String)anObject;
            int n = value.length;
            if (n == anotherString.value.length) {
                char v1[] = value;
                char v2[] = anotherString.value;
                int i = 0;
                while (n-- != 0) {
                    if (v1[i] != v2[i])
                        return false;
                    i++;
                }
                return true;
            }
        }
        return false;
    }

```

**compareTo**

String类实现了Compareble，里面有一个抽象方法叫compareTo，所以String中一定要对这个方法进行重写

```java
 public int compareTo(String anotherString) {
        int len1 = value.length;
        int len2 = anotherString.value.length;
        int lim = Math.min(len1, len2);
        char v1[] = value;
        char v2[] = anotherString.value;

        int k = 0;
        while (k < lim) {
            char c1 = v1[k];
            char c2 = v2[k];
            if (c1 != c2) {
                return c1 - c2;
            }
            k++;
        }
        return len1 - len2;
    }

```



```java
public class StringTest {
    public static void main(String[] args) {
        String str = "a"+"b"+"c";
        String str1 = "a"+"bc";
        String str2 = "ab"+"c";
        String str3 = "abc";
        String str4 = "abc"+"";
        //上述几个字符串通过反编译之后都是同一个字符串"abc"
    }
}
```



<img src="https://gitee.com/yan256992/cloudimages/raw/master/img/image-20210912203338351.png" alt="image-20210912203338351" style="zoom:50%;" />

常用方法

```java
String str = "abcdef";
System.out.println(str.isEmpty());//底层就是判断这个value数组的长度是否=0
System.out.println(str.charAt(2));
System.out.println(str.indexOf("abc"));
String str1 =   str.concat("yyy");
System.out.println(str1);


// 1. length(); 返回字符串长度
// 2. charAt(int index); 返回某个位置的字符
// 3. contains(String str); 判断是否包含某个字符串
String content = "java是最好的语言, java no1";
sout(content.length()); // 10
sout(content.charAt(content.length() - 1)); // 言
sout(content.contains("java")); // true
// 4. toCharArray(); 返回字符串对应数组 
// 5. indexOf(); 返回子字符串首次出现的位置
// 6. lastIndexOf(); 返回字符串最后一次出现的位置
sout(content.toCharArray());
sout(content.indexOf"java")); // 0
sout(content.indexOf("java", 4)); // 从索引4开始找 返回12
sout(content.lastIndexOf("java")); // 12
// 7. trim(); //去掉字符串前后空格
// 8. toUpperCase(); toLowerCase(); 转换大小写
// 9. endWith(str); startWith(str);  判断是否以str 结尾、开头
String ct = " hello world ";
sout(ct.trim()); // "hello world"
sout(ct.toUpperCase()); // HELLO WORLD
sout(ct.toLowerCase()); // hello world
sout(ct.endWith("world")); // true
sout(ct.startWith("hello")) // true
// 10. replace(char old, char new); 用心的字符或字符串替换旧的字符或字符串
// 11. split(); 对字符串拆分
// 补充两个equals/compareTo();比较大小
String s1 = "hello";
String s2 = "HELLO";
sout(s1.equalsIgnoreCase(s2));// 忽略大小写比较true

// compareTo(); 两字符不同时比较字符字典序的ascii码
// 字符相同时比较长度 返回差值

```

### StringBuilder类

![image-20210912212515219](https://gitee.com/yan256992/cloudimages/raw/master/img/image-20210912212515219.png)



```java
public StringBuilder(String str) {
    super(str.length() + 16);
    append(str);
}
StringBuilder sb = new StringBuilder();//表面上是调用空构造器 实际上是对value数组初始化 初始化的长度为16 ，同时可以自己指定数组的长度
```



### BigDecimal类

位于math包中，主要用于精确计算浮点数

```Java
BigDecimal bigDecimal = new BigDecimal("1.0");
BigDecimal bigDecimal1 = new BigDecimal("0.9");
//减法
System.out.println(bigDecimal.subtract(bigDecimal1));
//加法
System.out.println(bigDecimal.add(bigDecimal1));
//乘法
System.out.println(bigDecimal.multiply(bigDecimal1));
```

### Calendar类

- Calendar提供了获取或设置各种日历字段的方法
- 构造方法 `protected Calendar();` 由于是protected 所以无法直接创建
- 其他方法

| 方法名                                                       | 说明                                       |
| :----------------------------------------------------------- | :----------------------------------------- |
| static Calendar getInstance()                                | 使用默认时区和区域获取日历                 |
| void set(int year, int month, int date, int hourofday, int minute, int second) | 设置日历的年、月、日、时、分、秒           |
| int get(int field)                                           | 返回给定日历字段的值。字段比如年、月、日   |
| void setTime(Date date)                                      | 用给定的date设置此日历时间                 |
| Date getTime()                                               | 返回一个date表示此日历的时间               |
| void add(int field, int amount)                              | 按照日历的规则，给指定字段添加或减少时间量 |
| long getTimeInMilles()                                       | 毫秒为单位返回该日历的时间值               |

```Java

public static void main(String[] args) throws ParseException {
    //calendar是一个抽象类 不可以直接创造对象
    Calendar calendar = Calendar.getInstance();

    System.out.println(calendar.get(1)); //2021
    System.out.println(calendar.get(Calendar.MONTH)); // 8 从0开始
    System.out.println(calendar.get(Calendar.WEEK_OF_MONTH)); //3

    calendar.set(Calendar.YEAR,1998);
    System.out.println(calendar.get(1)); //1998

}

psvm(String[] args){
  // 1. 创建 Calendar 对象
  Calendar calendar = Calendar.getInstance();
  sout(calendar.getTime().toLocaleString());
  // 2. 获取时间信息
  // 获取年
  int year = calendar.get(Calendar.YEAR);
  // 获取月 从 0 - 11
  int month = calendar.get(Calendar.MONTH);
  // 日
  int month = calendar.get(Calendar.DAY_OF_MONTH);
  // 小时
  int hour = calendar.get(Calendar.HOUR_OF_DAY);
  // 分钟
  int minute = calendar.get(Calendar.MINUTE);
  // 秒
  int second = calendar.get(Calendar.SECOND);
  // 3. 修改时间
  Calendar calendar2 = Calendar.getInstance();
  calendar2.set(Calendar.DAY_OF_MONTH, x);
  // 4. add修改时间
  calendar2.add(Calendar.HOUR, x); // x为正就加 负就减
  // 5. 补充方法
  int max = calendar2.getActualMaximum(Calendar.DAY_OF_MONTH);// 月数最大天数
  int min = calendar2.getActualMinimum(Calendar.DAY_OF_MONTH);
}
```

### SimpleDateFormat类

将字符串类型与Date()类型相互转换

- SimpleDateFormat是一个以与语言环境有关的方式来格式化和解析日期的具体类
- 进行格式化（日期→文本）、解析（文本→日期）
- 常用的时间模式字母

| 字母 | 日期或时间         | 示例 |
| :--- | :----------------- | :--- |
| y    | 年                 | 2019 |
| M    | 年中月份           | 08   |
| d    | 月中天数           | 10   |
| H    | 一天中小时（0-23） | 22   |
| m    | 分钟               | 16   |
| s    | 秒                 | 59   |
| S    | 毫秒               | 356  |

```Java
package Java基础.常用类;
import java.text.ParseException;
import java.text.SimpleDateFormat;
import java.util.Date;
/**
 * 功能描述：
 * @Author: ygq
 * @Date: 2021/8/30 18:04
 */
public class test {
    public static void main(String[] args) throws ParseException {
        //自定义字符串的模式 看前端界面传递过来的字符串是什么样的
        DateFormat df = new SimpleDateFormat("yyyy/MM/dd HH:mm:ss");
        //string->date
        Date date = df.parse("2019/12/12 12:12:12");
        System.out.println(date); //Thu Dec 12 12:12:12 CST 2019
        //date->string
        Date date1 = new Date();
        System.out.println(df.format(date1)); //2021/09/12 16:40:37
        
        
       
        
        Date date = new Date();
        SimpleDateFormat sdf = new SimpleDateFormat("yyyy年MM月dd日 HH时mm分ss秒");
        System.out.println(sdf.format(date));//2021年08月30日 18时06分08秒
//        这里转换的时候需要和上面的输入格式一致 否则会报错
        Date date1 = sdf.parse("2021年01月11日 12时12分12秒");
        System.out.println(date1); //Mon Jan 11 12:12:12 CST 2021

    }
}
```

### Math类

> Java.Lang包下面的类 直接进行使用 不用导包

```Java
public static void main(String[] args) throws ParseException {
    System.out.println(Math.PI);
    System.out.println(Math.random());//[0,1)
    System.out.println(Math.abs(-80)); //绝对值
    System.out.println(Math.ceil(9.1));//向上取值
    System.out.println(Math.floor(9.1));//向下取值
    System.out.println(Math.round(4.6));//四舍五入
}
```

### Random类

```java
public class DateTest {
    public static void main(String[] args) throws ParseException {
        Random random =new Random(); //底层还是调用有参构造
        Random random1 = new Random(System.currentTimeMillis());//有参构造 指定种子
        System.out.println(random.nextInt(12)); //指定生成的数字的范围
    }
}
```

