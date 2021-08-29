# 🍊Java异常与反射

### Java中的异常

![image-20210826174419778](https://gitee.com/yan256992/cloudimages/raw/master/img/image-20210826174419778.png)

Java使用异常来表示错误，并通过`try ... catch`捕获异常；

Java的异常是`class`，并且从`Throwable`继承；

`Error`是无需捕获的严重错误，`Exception`是应该捕获的可处理的错误；

`RuntimeException`无需强制捕获，非`RuntimeException`（Checked Exception）需强制捕获，或者用`throws`声明；

不推荐捕获了异常但不进行任何处理。



使用`try ... catch ... finally`时：

- 多个`catch`语句的匹配顺序非常重要，子类必须放在前面；
- `finally`语句保证了有无异常都会执行，它是可选的；
- 一个`catch`语句也可以匹配多个非继承关系的异常。

### 反射

> 反射就是Reflection。反射是指在程序的运行期间可以拿到一个对象的所有信息
>
> - 在运行时（动态编译）获知任意一个对象所属的类。
> - 在运行时构造任意一个类的对象。
> - 在运行时获知任意一个类所具有的成员变量和方法。
> - 在运行时调用任意一个对象的方法和属性。

反射的执行过程

<img src="https://gitee.com/yan256992/cloudimages/raw/master/img/image-20210827214412011.png" alt="image-20210827214412011" style="zoom:80%;" />

例如，如下的实例，我们要调用一个对象的方法，那么必须要传入对象的实例

~~~java
public class Main{
    String getName(Person p){
        return p.getName();
    }
}
~~~

思考：假设没有这个对象的实例怎么办，此时只有一个Object实例

```java
String getName(Object obj) {
    return ???
}
```

这时候可以使用强转型，但是还是需要使用Person这个类。所以，反射解决的是在运行期间。对某个实例一无所知的情况下，如何调用其方法

* **CLass类**

除开基本数据类型之外，其他的类型全部是class。而`class`是在JVM执行过程中动态加载的。JVM在第一次读取到一种class类型时，将其加载到内存。

每加载一种class，JVM就为其创建一个CLass类型的实例，并关联起来

```java
public final class Class {
    private Class() {}
}
```

```
Class cls = new Class(String);
```

这个`Class`实例是JVM内部创建的，如果我们查看JDK源码，可以发现`Class`类的构造方法是`private`，只有JVM能创建`Class`实例，我们自己的Java程序是无法创建`Class`实例的。JVM持有的每一个Class实例都指向一个数据类型，一个实例中包含了这个class类中的所有信息。

<img src="https://gitee.com/yan256992/cloudimages/raw/master/img/image-20210827161954902.png" alt="image-20210827161954902"  />

因为在JVM中为每个加载的`class`类以及接口`interface`创建自己的Class实例对象。并且这个实例对象中包含这个类的所有信息，因此我们可以通过这个`Class`实例对象获取到这个类的方法等信息。

:pushpin:这种通过类的CLass实例对象来获取class类信息的方法就称为`反射`

**获取类的实例对象的方法**

1. 通过一个类的静态变量class获取

   ~~~java
   Class strClass = String.class
   ~~~

2. 通过实例变量的getClass()方法获取

   ```java
   String str = "Hello World";
   Class  cls =  str.getClass();
   ```

3. 如果知道类的完整类名，那么可以通过Class.forName(“类路径”)获取

   ```java
   Class cls = Class.forName("java.lang.String");
   ```

   

**通过Class实例获取Field对象**

```java
package Java基础;
import java.lang.reflect.Field;
/**
 * 功能描述：
 * @Author: ygq
 * @Date: 2021/8/27 16:33
 */
public class ReflectTest {
    public static void main(String[] args) throws NoSuchFieldException, IllegalAccessException {
        person p1 = new person("zs",12);
        Class cls = p1.getClass();   //获取Class实例
        System.out.println(cls.getClassLoader()); //sun.misc.Launcher$AppClassLoader@18b4aac2
        System.out.println(cls.getClass());       //class java.lang.Class

        Field field = cls.getDeclaredField("name");  //通过字段名获取声明的public的field
        System.out.println(field.get(p1));        //zs 
       //上述代码先获取Field实例，然后，用Field.get(Object)获取指定实例的指定字段的值。
        Field[] fields = cls.getFields();  //获取所有public的field
        for(Field f : fields){
            System.out.println(f);
        }
    }
}
class person{
   public String name;
    private int age;

    public person(String name, int age) {
        this.name = name;
        this.age = age;
    }
    public person(){

    }
    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
    public int getAge() {
        return age;
    }
    public void setAge(int age) {
        this.age = age;
    }
}

```

**通过Class实例获取所有的Method信息**

- `Method getMethod(name, Class...)`：获取某个`public`的`Method`（包括父类）
- `Method getDeclaredMethod(name, Class...)`：获取当前类的某个`Method`（不包括父类）
- `Method[] getMethods()`：获取所有`public`的`Method`（包括父类）
- `Method[] getDeclaredMethods()`：获取当前类的所有`Method`（不包括父类）

一个`Method`对象主要包括以下信息

- getName(): 返回方法的名称
- getReturnType():  返回方法的返回值类型，也是一个Class实例
- getParameter():  返回方法的参数类型

`对于Field和Method来说，如果是getxxx()代表的是获取public修饰的方法，如果是getDeclaredXXX就是获取所有的，如果想调用private的字段或方法，需要调用xx.setAccessible(true)表示可以访问`

**调用方法**

当我们获取到一个Method对象之后，就可以对其进行调用

```java
String s = "hello world";
String str = s.substring(6);  //“world”
```

如果使用反射来调用substring方法

```java
String s = "hello world";
//获取类的实例对象
Class cs = s.getClass();
//获取名叫substring 返回值为int的方法
Method method = cs.getMethod("substring", int.class);
//在s对象上调用该方法获取结果
String o = (String) method.invoke(s, 6);
System.out.println(o); //world
```

对`Method`实例调用`invoke`方法就相当于调用该方法，`invoke`方法的第一个参数是对象实例，`即在那个对象上调用该方法`，后面的参数要与方法的参数一致。

1. 调用静态方法，由于静态方法无需指定对象实例，所以第一个参数传null
2. 调用非oublic方法，和Field类似，对于非public方法，我们虽然可以通过`Class.getDeclaredMethod()`获取该方法实例，但直接对其调用将得到一个`IllegalAccessException`。为了调用非public方法，我们通过`Method.setAccessible(true)`允许其调用：

- **调用构造方法**

可以使用Class.newInstance()方法创建一个对象，但是只能调用无参构造方法

为了能调用任意的构造方法，可以使用Constructor对象，他包含了一个构造方法的所有信息，可以创建一个实例，它和Method类似

```java
public class Main {
    public static void main(String[] args) throws Exception {
        // 获取构造方法Integer(int):
        Constructor cons1 = Integer.class.getConstructor(int.class);
        // 调用构造方法:
        Integer n1 = (Integer) cons1.newInstance(123);
        System.out.println(n1);

        // 获取构造方法Integer(String)
        Constructor cons2 = Integer.class.getConstructor(String.class);
        Integer n2 = (Integer) cons2.newInstance("456");
        System.out.println(n2);
    }
}
```

- **反射的应用场景**

- 动态代理

- 连接数据库

  ```java
  public static void main(String[] args) throws Exception {  
          Connection con = null; // 数据库的连接对象  
          // 1. 通过反射加载驱动程序
          Class.forName("com.mysql.jdbc.Driver"); 
          // 2. 连接数据库  
          con = DriverManager.getConnection(
              "jdbc:mysql://localhost:3306/test","root","root"); 
          // 3. 关闭数据库连接
          con.close(); 
  }
  ```

- Spring框架

  ```java
  // 获取 Spring 的 IoC 容器，并根据 id 获取对象
  public static void main(String[] args) {
      // 1.使用 ApplicationContext 接口加载配置文件，获取 spring 容器
      ApplicationContext ac = new ClassPathXmlApplicationContext("spring.xml");
      // 2. 使用反射机制，根据这个字符串获得某个类的 Class 实例
      IAccountService aService = (IAccountService) ac.getBean("accountServiceImpl");
      System.out.println(aService);
  }
  ```

### 🚩References 

- 廖雪峰官方网站: https://www.liaoxuefeng.com/wiki/1252599548343744/1255945147512512
- CS-WIKI: https://veal98.gitee.io/cs-wiki
- https://blog.csdn.net/sinat_38259539/article/details/71799078