## Java核心编程

**Java异常**

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