title: java Throwable/Error/Exception
date: 2019-12-26 20:33:09
---
## Exception 与 Error

总结：
1. Error：影响jvm运行的问题，程序不需要捕获，也无法解决
2. Exception: 程序可以捕获和处理的异常
3. UncheckedException: 运行时异常，可以通过编码避免的异常，如空指针/数组越界
4. CheckedException: 程序必须捕获，并提供异常处理逻辑，如IO异常/网络连接异常
5. 不要把异常用于流程控制，会导致代码不易维护，同时影响性能
6. 不要用try块包裹大代码块，会影响jvm代码优化
7. throw early, catch later原则

Exception和Error都继承自Throwable对象，在Java中只有Throwable对象可以被抛出(throw)和捕捉(catch)，它是异常处理机制的实现基础。

![Throwable类图](/images/posts/java-error.png)

#### Error

Error类javaDoc

``` java
 * An {@code Error} is a subclass of {@code Throwable}
 * that indicates serious problems that a reasonable application
 * should not try to catch（一个设计合理的应用不应当捕捉的严重问题）. Most such errors are abnormal conditions.
 * The {@code ThreadDeath} error, though a "normal" condition,
 * is also a subclass of {@code Error} because most applications
 * should not try to catch it.
 * <p>
 * A method is not required to declare in its {@code throws}
 * clause any subclasses of {@code Error} that might be thrown
 * during the execution of the method but not caught, since these
 * errors are abnormal conditions that should never occur.（方法不应捕捉Error和它的子类，因为这类异常是不应该出现的非正常情况）
 *
 * That is, {@code Error} and its subclasses are regarded as unchecked
 * exceptions for the purposes of compile-time checking of exceptions.
```
从以上可以看出，Error指的是极端情况下出现的异常，一般这类异常出现后，JVM已经处于非正常状态，即使捕捉到异常程序也没有办法作出补救，比如上图中的OOMError/StackOverflowError.


#### Exception

```java
 * The class {@code Exception} and its subclasses are a form of
 * {@code Throwable} that indicates conditions that a reasonable
 * application might want to catch.(设计合理的应用也许会捕捉Exception和它的子类)
 *
 * <p>The class {@code Exception} and any subclasses that are not also
 * subclasses of {@link RuntimeException} are <em>checked
 * exceptions</em>（所有Exception子类，如果不是RuntimeException的子类，都属于CheckedException）.  Checked exceptions need to be declared in a
 * method or constructor's {@code throws} clause if they can be thrown
 * by the execution of the method or constructor and propagate outside
 * the method or constructor boundary.（checkedException必须在方法中throws部分显示声明，并向外层调用传递）
```
Exception是Java允许程序主动捕获/传递的异常。分为CheckedException和UncheckedException两部分。

##### UncheckedException
RuntimeException及其子类均属于UncheckedException，又称为运行时异常。该类异常一般认为可以通过合理编码避免的异常，如NullPointerException/ArrayIndexOutOfBoundsException，这些异常都可以通过一些边界检查代码避免，Java不会在编译期强制要求程序捕捉和处理。

##### CheckedException
CheckedException是必须在程序中显式的捕捉和处理的一类异常。此类异常一般不是可以通过编码规避的，程序必须负责提供异常处理逻辑。如IOException/ConnectionException等外部环境导致的异常。

##### 异常处理建议

第一，不要用抛出异常来控制代码流程，由于java实例化一个异常时需要对栈进行快照，这是一个相对比较重的操作。
第二，不要用try块包裹一大块代码，尽量哪里抛异常包裹哪里。try块会影响jvm对代码的优化。
第三，不要生吞异常，在catch块中把异常忽略掉，不做处理。此类情况往往是基于假设这类异常绝对不会发生。
第四，不要捕捉Error，更不要捕捉Throwable，尽量明确捕捉一个特定异常，多个异常多个catch块处理。
第五，throw early, catch later原则。有可能抛出异常的代码尽量提早执行。
第六，finally块不要处理需要return的对象，否则会影响到try块或catch块中return的对象
