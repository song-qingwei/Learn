[TOC]

#### Java 异常

***

异常是指不期而至的各种状况，如：文件找不到、网络连接失败、非法参数等。异常是一个事件，它发生在程序运行期间，干扰了正常的指令流程。Java 中通过 API 中`java.lang.Throwable`类的众多子类描述各种不同的异常。因而，Java 异常都是对象，是 `Throwable`子类的实例，描述了出现在一段编码中的错误条件。当条件触发时，错误将引发异常。下图为 Java 异常类层次结构图：

![Java 异常类层次结构图](F:\面试准备\截图\技术篇\Throwable\Java异常类层次结构图.jpg)

在 Java 中，所有异常的超类为 `Throwable`。

##### `Throwable`

`Throwable`：有两个重要的子类：Exception（异常）和 Error（错误）。

###### `Error（错误）`

是程序无法处理的错误。表示运行应用程序中较严重的问题。大多数错误与代码编写者执行无关，而表示代码运行时 JVM 出现问题。例如：Java 虚拟机运行错误（`VirtulMachineError`），当 JVM 不再有继续执行操作所需要的内存资源时，将出现内存溢出错误（`OutOfMemoryError`）。这些异常发生时，Java 虚拟机一般会选择终止线程。

###### `Exception（异常）`

是程序本身可以处理的异常，一般是程序员逻辑错误或不严谨导致的。`Exception` 有一个重要的子类 `RuntimeException`。`RuntimeException`及其子类表示 “JVM 常用操作” 引发的错误。例如：试图使用空值对象引用、除数为零或数组越界等，则分别引发运行时异常（`NullPointerException`、`ArithmeticException`、`ArrayIndexOutOfBoundException`）。

##### 受检异常（checked exceptions）与非受检异常（unchecked exceptions）

通常，Java 的异常（包括 Error 与 Exception）可分为<font color='#FF3399'>受检异常</font>和<font color='#FF3399'>非受检异常</font>。

###### 受检异常（checked exceptions）

编译器要求必须处理的异常。程序在运行中，很容易出现的、清理可容的异常。可查异常虽然属于异常状况，但在一定程度上它的发生是可预计的，而且一旦发生这种异常，就必须采取某种方式进行处理。

除了`RuntimeException`及其子类以外，其他的 Exception 类及其子类都属于可查异常。这种异常的特点是 Java 编译器会检查它，也就是说，当程序中可能出现这类异常，要么用`try-catch`语句捕获它，要么用`throws`子句声明抛出它，否则编译不会通过。

###### 非受检异常（unchecked exceptions）

编译器不要求强制处置的异常。包括运行时异常（`RuntimeException`与其子类）和错误（Error）。

##### 运行时异常与非运行时异常

Exception 这种异常分两大类运行时异常和非运行时异常(编译异常)。程序中应当尽可能去处理这些异常。

###### 运行时异常

都是`RuntimeException`类及其子类异常，如`NullPointerException`(空指针异常)、`IndexOutOfBoundsException`(下标越界异常)等，这些异常是不检查异常，程序中可以选择捕获处理，也可以不处理。这些异常一般是由程序逻辑错误引起的，程序应该从逻辑角度尽可能避免这类异常的发生。

运行时异常的特点是 Java 编译器不会检查它，也就是说，当程序中可能出现这类异常，即使没有用`try-catch`语句捕获它，也没有用`throws`子句声明抛出它，也会编译通过。

###### 非运行时异常

是`RuntimeException`以外的异常，类型上都属于 Exception 类及其子类。从程序语法角度讲是必须进行处理的异常，如果不处理，程序就不能编译通过。如`IOException`、`SQLException`等以及用户自定义的Exception异常，<font color='#ff5500'>一般情况下不自定义检查异常</font>。