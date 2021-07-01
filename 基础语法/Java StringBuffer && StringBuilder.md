# Java StringBuffer 和 StringBuilder 类

当对字符串进行修改的时候，需要使用 StringBuffer 和 StringBuilder 类。

跟String 类不同的是，StringBuffer 和 StringBuilder 类的对象能够被多次的修改，并且不产生新的未使用对象。

在使用 StringBuffer 类时，每次都会对 StringBuffer 对象本身进行操作，而不是生成新的对象，所以如果需要对字符串进行修改推荐使用 StringBuffer。

StringBuilder 类和 StringBuffer 之间的最大不同在于 **StringBuilder 的方法不是线程安全的（不能同步访问）**。**StringBuffer 是线程安全**的，能够被同时访问。

由于 **StringBuilder 相较于 StringBuffer 有速度优势**，**所以多数情况下建议使用 StringBuilder 类**。

# String知识点

在java中String属于对象，java提供了String类来创建和操作字符串，可以使用关键字和构造方法来创建String对象。String创建的字符串存储在公共池中，而new创建的字符串对象在堆中。

**注意：**

```java
String 类是不可改变的，所以你一旦创建了 String 对象，那它的值就无法改变了
```

String类提供了两个连接字符串的方法 concat()，但是常用的是“+”。

### String方法

下面提供了String类支持的方法，更多详细，参看[Java String API](https://www.runoob.com/manual/jdk1.6/java/lang/String.html)，[String方法](https://www.runoob.com/java/java-string.html)文档
