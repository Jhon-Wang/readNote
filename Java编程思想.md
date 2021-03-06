# Java 编程思想
## 第一章 对象导论
### 1.10
- 使用对象最关键问题之一是对象的创建和销毁
	- c++的处理方式
		1. 具体处理方式：让程序员选择创建和销毁
		2. 出发点：最求最大的执行速度
		3. 方式：通过将对象置于堆栈或静态存储区域内来实现
		4. 优点：执行速度快
		5. 缺点：牺牲了灵活性，必须在编写程序时知道对象确切的数量、生命周期和类型。
	- Java处理方式
		1. 在被称为堆得内存池中动态创建对象
		2. 出发点：灵活性，不需要再编程时就确定对象的数量生命周期和类型。
		3. 方式：每当想要创建新对象时就在堆内存中动态创建新的对象实例。
		4. 优点：动态创建，带来了更大的灵活性，编译器可以自己确定对象存活时间并可以自动销毁，垃圾回收提供了避免内存泄漏的保障。
### 1.11 异常处理
- 异常的理解
	1. 异常是一种对象，从出错点被“抛出”，并被专门设计用来处理特定类型错误的相应的异常处理器“捕获”异常处理就像与程序正常路径并行的、在错误发生时执行的另一条路径。
	2. 异常的作用：异常提供了一种从错误状况进行可靠恢复的途径。现在不再是只能退出程序，你可以进行校正，并恢复程序的执行，有助于编写更健壮的程序。

### 1.12 并发编程
- 并发实质：同一时刻处理多个任务。
- 最初实现并发的方法：用有关机器底层的只是来编写中断服务程序，主进程的挂起是通过硬件中断来触发的。
	1. 缺点：
		- 实现难度大
		- 移植难度大
- 并发的应用场景：
	1. 处理时间性强的问题
	2. 将问题切分为多个可独立运行的部分，提高程序相应的能力
- 线程概念：线程只是一种为单一处理器分配执行时间的手段，但是如果操作系统支持多处理器，那么每个人物都可以被指派给不同的处理器，并且在真正的并行执行
- 语言多线程带来的便利性：程序员不用再操心机器是单处理器还是多处理器
- 并发处理可能引发的问题：共享资源，引入同步和锁问题。


## 第二章 一切都是对象
### 2.1用引用操作对象
- Java操作对象的方式
	1. Java操作的标识符是对一个对象的引用
	2. 引用和对象的关系，没有对象引用也能独立存在，有一个引用并不一定需要一个对象与它关联
	3. 字符串可以用带引号的文本初始化。
### 2.2必须由你创建所有的对象
#### 2.2.1 存储到什么地方
- 五个不同的存储数据的地方
	1. 寄存器 最快的存储器，位于处理器内部，数量及其有限，不能直接控制
	2. 堆栈 位于通用RAM中 通过堆栈指针能够从处理器哪里获得直接支持，若堆栈指针向下移动分配新内存，向上移动释放内存，速度次于寄存器，必须知道所有项的确切生命周期，这一特性限制了程序灵活性。对象引用存在这一区域中，但是Java对象不存在这一区域。
	3. 堆 不需要知道对象的生命周期 由此带来了灵活性 劣势：分配和清理比在堆栈中消耗更多的时间
	4. 常量存储 常量值通常存储在代码内部，因为他们永远不会被改变就看见
	5. 非RAM存储 如果数据完全存储在程序之外，那么他可以不受程序的控制，在程序不运行的时候也能存在。两个例子“流对象”和“持久化对象”。
		- 流对象 对象转化为字节流，通常被发送到其他机器。
		- 持久化对象 对象被存储在磁盘上，因此即使程序终止，他们仍然可以保留自己的状态。
#### 2.2.2基本类型
- 设置基本类型的出发点：new将对象存储在“堆”里，故用new创建一个对象--特别是小的简单的对象，往往不是很有效，所以不用new创建变量而是创建一个并非是引用的“自动”变量。这个变量直接存储值并置于堆栈中
- 所占存储空间大小不变性

	|基本类型|大小|最小值|最大值|包装器类型|
	|---|---|---|---|---|
	|boolean|-|-|-|Boolean|
	|char|16-bit|Unicode 0|Unicode 2^16^-1|Character|
	|byte|8 bits|-128|+127|Byte|
	|short|16 bits|-2^15^|+2^15^-1|Short|
	|int|32 bits|-2^31^|+2^31^-1|Integer|
	|long|64 bits|-2^63|+2^63-1|Long|
	|float|32 bits|IEE754|IEEE754|Float|
	|double|64 bits|IEE754|IEEE754|Double|
    |void|-|-|-|Void|
- 高精度数字
	1. BigInteger  
	2. BigDecimal
#### 2.2.3 Java中的数组
- c++中的数组 
	1. c c++ 中的数组就是一个内存块，如果一个程序要访问其自身以外的数组，或在初始化之前使用就会产生意料之外的错误，。
- Java 的数组
	1. 确保数组会被初始化
	2. 进行范围检查
	3. 劣势：少量的内存开销以及运行时的下标检查
	4. 优势：安全性和效率的提高
### 2.3 永远不需要销毁的对象
#### 2.3.1 作用域
- 作用域决定了变量名的可见性和生命周期
- 在作用域内定义的变量只能用于作用域结束之前。
#### 2.3.2对象的作用域
- Java对象不具备和基本类型一样的生命周期。当使用new创建一个对象是，它可以存在于作用域之外
```java
	String s = new String("a string");

```
s在作用域结束就消失了但是String对象仍然占据内存空间
- Java对象的存在如果不处理那么就会填满内存空间，Java通过垃圾回收期辨别出不引用的对象并将其回收；

### 2.4创建新类
#### 2.4.1字段和方法
- 可以在类中设置两种类型的元素，字段和方法
- 字段可以是基本类型也可以是引用类型
- 基本成员默认值
	|基本类型|默认值|
	|---|---|
	|boolean|false|
	|char|'\u0000'(null)|
	|byte|(byte)0|
	|short|(short)0|
	|int|0|
	|long|0L|
	|float|0.0F|
	|double|0.0D|

- Java的确保初始化并不适用于局部变量如果局部变量没有被赋值会被识别为错误。
### 2.5方法参数和返回值
- 方法：做某些事情的方式
- 返回类型：调用方法之后返回的值
- 参数列表：要传递给方法的信息的类型和名称
- 限制：Java中的方法只能作为类的一部分来构建，方法必须通过类来调用。
#### 2.5.1 参数列表
- 参数列表指定要传递给方法什么样的信息。这些参数采用的是对象形式对象列表指定了参数类型及名字。这里传递的其实是引用。- return 关键字的用法
	1. 已经做完离开此方法
	2. 此方法产生一个值

### 2.6 构建一个Java程序
#### 2.6.1 名字可见性
- 为了给一个类库生成不会与其他名字混淆的名字，Java希望程序员反过来使用自己的域名
- 整个包名都是小写
#### 2.6.2 运用其他构件
- 使用import 关键字告诉编译器想要什么类
#### 2.6.3 static 关键字
- 解决的问题：
	1. 只想为某特定域分配单一存储空间，而不去考虑创建多少对象，甚至根本就不创建对象。
	2. 不创建对象也能调用方法（包括但是不限于工具类）
- 非static 方法必须要知道他们一起运作的特定对象
- static 仍然可以通过对象来来访问

### 2.7 第一个Java程序



## 第三章 操作符
操作费
