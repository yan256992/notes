# 🍄Java基础知识

### 🌻Java程序基础

- #### 基本数据类型

byte          1字节        short         2字节     int             4字节    long          8字节

float          4字节        double      8字节     boolean                   char           2字节

- #### 浮点型

浮点类型的数就是小数，因为小数用科学计数法表示的时候，小数点是可以浮动的。

~~~~java
float f1 = 3.14f;
float f2 = 3.14e38f; // 科学计数法表示的3.14x10^38
double d = 1.79e308;
double d2 = -1.79e308;
double d3 = 4.9e-324; // 科学计数法表示的4.9x10^-324
~~~~

对于`float`类型的小数需要在后面加上`f`后缀

- #### 引用类型

除开上述的几种基本数据类型的变量，剩下的都是引用类型。例如常见的`String`字符串。引用类型类似于C语言的指针，它的内部存储了一个“地址”

。指向某个对象在内存中的位置

- #### 移位运算

在计算机中，整数总是以二进制的形式表示。可以对整数进行移位运算

```java
//左移运算
int n = 7;       // 00000000 00000000 00000000 00000111 = 7
int a = n << 1;  // 00000000 00000000 00000000 00001110 = 14
int b = n << 2;  // 00000000 00000000 00000000 00011100 = 28
//右移运算
int n = 7;       // 00000000 00000000 00000000 00000111 = 7
int a = n >> 1;  // 00000000 00000000 00000000 00000011 = 3
int b = n >> 2;  // 00000000 00000000 00000000 00000001 = 1
```

- #### 位运算

​     位运算主要是指与`&` 、或`|`、非`~`、异或`^`运算

1. 与运算 两个数同时为1才为1
2. 或运算 只要有一个1就是1
3. 异或运算 两个数不同就为1

- #### 字符和字符串

字符类型用`char`表示,因为Java在内存中总是使用Unicode编码表示字符，所以，一个英文和一个中文字符都可以用char表示，他们都占两个字节

**String字符串不可变**

~~~java
public class Main {
    public static void main(String[] args) {
        String s = "hello";
        System.out.println(s); // 显示 hello
        s = "world";
        System.out.println(s); // 显示 world
    }
}
~~~

这里实际上是创建了两个新的字符串

![image-20210826164529819](https://gitee.com/yan256992/cloudimages/raw/master/img/202108261645118.png)

:warning:null和“”不相等，nul表示不存在，即该变量不指向任何对象。而空字符串是一个实际存在的有效对象，它不等于null

###  🐈面向对象

:warning:一个Java源文件可以包含多个类的定义，但是只能定义一个public类，且public类名必须与文件名一致

`protected`修饰的类，只有自己的子类可以访问。默认的`Default`就是当前包下面的类可以访问

- **参数绑定**

  对于基本类型参数的传递。是调用方值得复制，双方各自的修改不会影响别人

  对于引用数据类参数的传递，二者指向的是同一个对象，双方在任意时刻对于对象的改变都会影响别人（因为指向的是用一个对象）

- **继承和组合**

  如果两个是is关系那么就用继承，如果是has则使用组合。例如Person和Book就应该使用组合，因为二者属于has关系

- **抽象类**

  如果父类的方法没什么作用，仅仅是为了子类去重写实现，那么就可以将父类的方法申明为抽象方法，拥有抽象方法的类就是抽象类。

  `抽象类不能被实例化`。抽象类中的抽象方法子类必须实现

- **接口**

  如果一个抽象类中所有的方法都没有具体实现，那么就可以将这个抽象类声明为一个接口，一个`interface`可以继承自另一个`interface`。`interface`继承自`interface`使用`extends`，它相当于扩展了接口的方法。

- **静态字段**

  在一个class中定义的字段称为实例字段，实例字段的特点是每个实例是独有的字段，各个实例的同名字段互不干扰。还有一种字段，是用`static`修饰的字段。称之为静态字段。静态字段是一个空间，所有的实例都会共享该字段

  可以直接通过类名.方法名来访问静态字段

- **局部变量**

  在方法内部定义的变量是局部变量。局部变量从方法声明出开始到对应的块结束，方法参数也是局部变量

  