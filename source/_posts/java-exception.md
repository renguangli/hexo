---
title: java 异常处理机制
tags: Java
link_refer:
  - url: https://www.oschina.net/translate/10-exception-handling-best-practices-in-java-programming
    title: https://www.oschina.net/translate/10-exception-handling-best-practices-in-java-programming
date: 2019-01-02 13:36:35
---

异常是程序中的一些错误，但并不是所有的错误都是异常，有些错误有时候是可以避免的。

<!-- more -->

# 异常分类

## Error 和 Exception。

Error 和 Exception 都是 Throwable 的子类，Error 表示程序无法解决的错误
常见的有 `OutOfMemoryError`、`StackOverflowError`

Exception 表示程序执行时发生的异常，可以被处理。
常见的有  `NullPointerException`、`ClassNotFoundException`、`IndexOutOfBoundsException`

系统一旦出现 Error 错误，那么整个系统将无法继续运行。但是系统出现异常，只要处理得当，不会对系统造成多大影响。

![](https://img-blog.csdn.net/20180920165502957?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21pY2hhZWxnbw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
## 受检异常和运行时异常。

受检异常可以在编译时检测到，必须进行手动处理，否则无法通过编译。常见的有 `IOException`，`FileNotFoundException`

运行时异常属于代码逻辑性异常，需要在运行期出现，可以不进行处理，同样可以通过编译，但是在运行时一旦出现该异常，则当前线程停止执行。一般情况下我们都会对运行时异常进行捕捉处理，以保证程序的正常运行，对于无关紧要的部分可以直接catch住，但对于影响当前线程后续执行的异常必须立即抛出来结束线程执行，这样能有效保护数据，不会因为异常而产生错误数据，一般我们会在事务中执行，一旦抛出异常，执行回滚，保证数据安全。常见的有 `NullPointerException`.

# 异常处理框架

## try catch

通常使用 `try catch` 来捕获异常

```java
try {
    int[] a = {1, 2};
    int x = a[0] / 0;
} catch (ArithmeticException e) {
    e.printStackTrace();
}
```

多重捕获块

```java
try {
    int[] a = {1, 2};
    int x = a[0] / 0;
    int y = a[2];
} catch (ArithmeticException e) {
    e.printStackTrace();
} catch (ArrayIndexOutOfBoundsException e) {
    e.printStackTrace();
}
```
或者这样

```java
try {
    int[] a = {1, 2};
    int x = a[0] / 0;
    int y = a[2];
} catch (ArithmeticException | ArrayIndexOutOfBoundsException e) {
    e.printStackTrace();
}
```

## finally

finally 不管有没有发生异常都会执行，除非退出 Java 虚拟机。通常用来用来关闭连接或者释放资源。

```java
OutputStream os = null;
try {
    os = new FileOutputStream("D:/a.txt");
    os.write("hello world".getBytes());
    os.flush();
} catch (IOException e) {
    e.printStackTrace();
} finally {
    try {
        if (os != null) {
            os.close();
        }
    } catch (IOException e) {
        e.printStackTrace();
    }
}
```
## try-with-resource

Jdk1.7 中新增了 `try-with-resource` 语法，当一个外部资源的对象实现了 `AutoCloseable` 接口，Jdk1.7 中便可以利用 `try-with-resource` 语法更优雅的关闭资源。

```
try (OutputStream os = new FileOutputStream("D:/a.txt");){
    os.write("hello world".getBytes());
    os.flush();
} catch (IOException e) {
    e.printStackTrace();
}
```

## throw throws

throw 用于手动抛出异常

throws 用于声明方法可能会抛出的异常

```java
public void throwsThrow(String str) throws Exception{
    if (str == null) {
        throw new Exception("parameter str not be null");
    }
}
```

# 异常处理的 10 个最佳实践

这里是我收集的 10 个Java编程中进行异常处理的10最佳实践。在 Java 编程中对于检查异常有褒有贬，强制处理异常是一门语言的功能。在本文中，我们将尽量减少使用检查型异常，同时学会在 Java 编程中使用检查型VS非检查型异常。

## **1）为可恢复的错误使用检查型异常，为编程错误使用非检查型错误。**

选择检查型还是非检查型异常，对于Java编程人员来说，总是让人感到困惑。检查型异常保证你对错误条件提供异常处理代码，这是一种从语言到强制你编写健壮的代码的一种方式，但同时会引入大量杂乱的代码并导致其不可读。当然，如果你有替代品和恢复策略的话，捕捉异常并做些什么看起来似乎也在理。在Java编程中选择检查型异常还是运行时异常，更多信息参考[ checked vs unchecked exceptions](http://javarevisited.blogspot.com/2011/12/checked-vs-unchecked-exception-in-java.html)。

## **2）在finally程序块中关闭或者释放资源**

这在Java编程中，是一个广为人知的最佳实践，在处理网络和IO类的时候，相当于一个标准。在finally块中关闭资源， 在正常和异常执行的情况下，保证之前和稀缺资源的合理释放，这由y finally块保证。从Java7开始，该语言有了一项更有趣的功能：[资源管理自动化或者ARM块](http://javarevisited.blogspot.com/2011/09/arm-automatic-resource-management-in.html)能实现这一功能。尽管如此，我们仍然要记住在finally块中关闭资源，这是对于释放像FileDescriptors这类，应用在socket和文件编程的情况下的有限资源很重要的。

## **3）在堆栈跟踪中包含引起异常的原因**

很多时候，当一个由另一个异常导致的异常被抛出的时候，Java库和开放源代码会将一种异常包装成另一种异常。日志记录和打印根异常就变得非常重要。Java 异常类提供了 getCause() 方法来检索导致异常的原因，这些（原因）可以对异常的根层次的原因提供更多的信息。该 Java 实践对在进行调试或排除故障大有帮助。时刻记住，如果你将一个异常包装成另一种异常时，构造一个新异常要传递源异常。

## **4）始终提供关于异常的有意义的完整的信息**

异常信息是最重要的地方，因为这是程序员首先看到的第一个地方，这里你能找到问题产生的根本原因。这里始终提供精确的真实的信息。例如，对比 IllegalArgumentException 异常的两条异常信息：

消息 1: "Incorrect argument for method"

消息 2: "Illegal value for ${argument}: ${value}

第一条消息仅说明了参数是非法的或者不正确，但第二条消息包括了参数名和非法值，而这对于找到错误的原因是很重要的。在用Java编程中编写异常处理代码的时候，始终遵循该 Java 最佳实践。

## **5）避免过度使用检查型异常**

检查型异常在强制执行方面有一定的优势，但同时它也破坏了代码，通过掩盖业务逻辑使代码可读性降低。只要你不过度使用检查型异常，你可以最大限度的减少这类情况，这样做的结果是你会得到更清洁的代码。你同样可以使用 Java7 的新功能，像 [one catch block for multiple exceptions 和 automatic resource management](http://javarevisited.blogspot.com/2011/07/jdk7-multi-cache-block-example-tutorial.html)以移除重复项。

## **6）将检查型异常转为运行时异常**

这是在像 Spring 之类的多数框架中用来限制使用检查型异常的技术之一，大部分出自于 JDBC 的检查型异常，都被包装进 DataAccessException 中，而（DataAccessException）异常是一种非检查型异常。这是Java最佳实践带来的好处，特定的异常限制到特定的模块，像 SQLException 放到 DAO 层，将意思明确的运行时异常抛到客户层。

## **7）记住对性能而言，异常代价高昂**

需要记住的一件事是异常代价高昂，同时让你的代码运行缓慢。假如你有方法从 ResultSet（结果集）中进行读取，这时常会抛出 SQLException 异常而不会移到下一元素，这将会比不抛出异常的正常代码执行的慢的多。因此最大限度的减少不必要的异常捕捉和移动，那里没有什么固定的原因。不要仅仅是抛出和捕捉异常，如果你能使用boolean变量去表示执行结果，可能会得到更整洁，更高性能的解决方案。修正错误的根源，避免不必须要的异常捕捉。

## **8）避免 catch 块为空**

没有什么比空的 catch 块更糟糕的了，因为它不仅隐藏了错误和异常，同时可能导致你的[对象](http://javarevisited.blogspot.com/2012/12/what-is-object-in-java-or-oops-example.html)处于不可使用或者脏的状态。空的catch块只能变得无意义，如果你非常肯定异常不会继续以任何方式影响对象状态，但在程序执行期间，用日志记录错误依然是最好的（方法）。对于在Java编程中编写异常处理代码，这不仅仅是一个 Java 最佳实践，而是一个最通用的实践。

## **9）使用标准异常**

我们的第九条最佳实践建议使用标准和内置的 Java 异常。使用标准异常而不是每次创建我们自己的异常，对于维护性和一致性，不管是现在还是以后，都是最好的选择。重用标准异常使代码更具可读性，因为大部分 Java 开发人员对标准的像源自于 JDK 的 RuntimeException 异常，IllegalStateException 异常，IllegalArgumentException 异常或者 [NullPointerException ](http://javarevisited.blogspot.com/2012/06/common-cause-of-javalangnullpointerexce.html)异常，（开发者）他们能一眼就知道每种异常的目的，而不是在代码里查找或者在文档里查找用户定义的异常的目的。

## **10）记录任何方法抛出的异常**

Java提供了 [throw和throws](http://java67.blogspot.com/2012/10/difference-between-throw-vs-throws-in.html) 关键字来抛出异常，在 javadoc 中用 @throw 记录任何方法可能会抛出的异常。如果你编写API或者公共接口，这就变得非常重要。任何方法抛出的异常都有相应的文档记录，这样你就能下意识的提醒任何使用（该方法）的人。

这些就是所有在 Java 编程中在处理异常的时候需要遵循的最佳实践。让我们知道了什么是在 Java 编程中编写异常处理代码时需要遵循的实践。