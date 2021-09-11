## Java多线程

> 进程：是操作系统对程序资源进行统一管理的基本单元
>
> 线程：是进程进行服务提供的基本单元

```Java
package Java基础.并发编程.多线程基础;
/**
 * 功能描述：线程使用案例：龟兔赛跑
 * 赛跑比赛是比较两者之间的速度谁更快
 * 方案一:
 *      乌龟先跑 然后计时 得到乌龟的时间
 *      兔子先跑 然后计时 得到兔子的时间
 *方案二:
 *      两个同时跑 看谁先跑完全程
 * @Author: ygq
 * @Date: 2021/9/10 20:17
 */
public class UseThread {
    public static void main(String[] args) {
        //开辟两个线程 乌龟和兔子
        Runner runner = new Runner("乌龟");
        Runner runner1 = new Runner("兔子");
        runner.start();
        runner1.start();
    }
}
class Runner extends Thread{
    private String name;
    public Runner(String name){
        this.name = name;
    }
    public Runner() {
    }
    @Override
    public void run() {
        for (int i = 0; i <=100 ; i++) {
            System.out.println(name+"跑了"+i+"米");
        }
    }
}
```

### Thread的常用方法

> thread类是java提供的线程的实现类，其底层已经实现了线程的创建和开启的功能

**方法:**

- run():线程的任务方法，线程会自动调用该方法
- start()：线程的创建方法，调用此方法之后，线程被创建并且进入就绪状态
- currentThread（）：该方法返回当前创建的线程
- getName（）：返回当前线程的名字
- getPriority()：返回当前线程的优先级，优先级越大并不是说肯定会获取到CPU，只是获得的概率相对较大,CPU执行权和很多的原因有关

### Runable实现多线程

```java
package Java基础.并发编程.多线程基础;

/**
 * 功能描述：Thread也是实现了这个接口
 *
 * @Author: ygq
 * @Date: 2021/9/10 20:39
 */
public class RunableTest {
    public static void main(String[] args) {
         Thread thread = new Thread(new threadRunable(),"乌龟");
         Thread thread1 = new Thread(new threadRunable(),"兔子");
         thread.start();
         thread1.start();
         //所以匿名内部类
        new Thread(new Runnable() {
            @Override
            public void run() {
                for (int i = 0; i <10 ; i++) {
                    System.out.println("匿名内部类实现");
                }
            }
        }).start();
//        使用lambda表达式
        new Thread(()->{
            for (int i = 0; i < 5; i++) {
                System.out.println("lambda表达式");
            }
        }).start();
    }
}
class  threadRunable implements Runnable{
    //声明线程的任务 相当于将线程的任务单独的抽离出来
    @Override
    public void run() {
        for (int i = 0; i < 100; i++) {
            System.out.println(Thread.currentThread().getName()+"跑了"+i+"米");
        }
    }
}
```

### 使用Callable接口

> JDk1.5之后产生的方法。该方法的可以有返回值，并且可以抛出检查异常