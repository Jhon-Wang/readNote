# Java编程思想
## 11.持有对象
### 11.1 泛型和类型安全的容器
之前容器的缺点？ 编译器允许你插入不同类型的对象
使用泛型创建容器带来的好处？ 你插入的对象只能是声明的对象，取出对象的时候不需要执行转型，会进行自动转型

## 11.2 基本概念
Java容器类的用途是保存杜一箱，并将其划分为两个不同的概念
1、 Collection 单独元素的序列
2、 Map 保存键值对
使用接口来替代具体的对象在大多数情况下是可行的，除非执行具体对象特定的操作
### 11.3添加一组元素
Collection.addAll()方法，它接受的参数类型为
- Collection 对象
- collection&数组
- collection&逗号分割的列表
Collection.addAll(collection)
Collection.addAll(collection,10,11,12)
Collection.addAll(collection,{11,12,13,43})
建议使用构造不包含元素的Collection 并通过Collection.addAll()方法来添加元素

Collection.addAll() 成员方法只能接受另一个Collection不如Arrays.asList()或Collections.addAll()灵活这两个方法使用可变参数列表

ArrayLists.asList() 产生的输出底层表示是数组，不能调整尺寸 无法调用add（） 或delete（）

### 11.4 容器的打印

容器打印不需要任何帮助
- ArrayList和LinkedList 是按照插入的顺序保存元素，两者的不同在于执行某些特定操作时的性能
LinkedList 包含的操作也多余ArrayList

HashLSet TreeSet和LinkedHashSet 都是Set类型，输出显示在Set中，每个相同的项只能保存一次。HashSet 有较快的访问速度，没有固定的存储顺序。如果要有存储顺序那么可以使用TreeSet，按照比较结果的升序进行排序，或者使用LinkedHashSet，它按照添加顺序添保存对象

### 11.5 List
List 类型
- ArrayList，优点是随机访问速度快 缺点是插入和移除元素时比较慢
- LinkedList 有点是插入和删除操作速度快，缺点是随机访问相对较慢， 相对于ArrayList 支持更多操作

特性集
- contains 确定某个元素在容器中
- remove 删除某个元素
- indexOf 对象在容器中的索引位置
 这三个方法都会使用equals方法来判断操作的对象是否正确
 - subList 从较大的累彪中创建出一个片段
 - containsAll 是否包含参数中的所有元素顺序不重要
 - removeAll 从List中移除参数List 中所有的元素
 - set 根据位置设置值
 - replace 更具位置设置值
 - addAll 可以在中间插入一个List
 - isEmpty 是否为空
 - clear 清除

### 11.6 迭代器
Java 中的迭代器是单向的

迭代器的作用
- 使用方法iterator要求容器返回一个Iterator。Iterator 将准备好返回序列的第一个元素
- 使用next()获取序列的下一个元素
- 使用hasNext（）家产徐类中是否还有元素
- 使用remove（）将迭代器心境返回的元素删除
在remove 之前必须要先调用next（）

#### ListIterator
此接口是Iterator 的子接口只能用于List类的访问
支持双向操作

- 如何创建 调用listIterator() 方法产生一个指向List开始出的ListIterator，也可以通过调用listIterator(n)的方法创建指向n的元素处的ListIterator

### 11.7 LinkedList
特性集
- 插入和移除对象比ArrayList对更加高效
- 随机访问能力比较弱
- 提供了可以使其用作栈队列 双端队列的方法

相似方法
- getFirst() 和 element() 完全相同 就是名字不同如果List为空抛出NoSuchElementException peek方法只有些许差异，列表为空返回null
- removeFirst 和remove也是完全相同的，移除并返回列表的头，在为空是抛出NoSuchElementException poll稍有不同，为空时返回null
- addFirst（） 和add() 和addLast（）相同，都是讲某个元素插入列表的尾（端）部
- removeLast() 移除并返回列表的最后一个元素

### 11.8 Stack
栈的特性
- 后进先出
- LinkedList 基友能够自己嗯实现栈的所有功能
在java.util中没有任何公共的Stack咩有任何公共的Stack接口，LinkedList可以产生更好的Stack

### 11.9 Set
set特性：不保存重复元素
TreeSet能按照插入顺序保存元素

### 11.10 Map

### 11.11 Queue
特性：先进先出
- LinkedList提供了方法以支持队列的行为，实现了Queue接口 可以作为Queue的一种实现

#### 11.11.1 PrioityQueue
使用offer（）方法添加元素这个元素会在队列中被排序，默认的排序将使用自然排序使用peek（） poll() remove() 方法时将是队列中优先级最高的元素

### 11.12 Collection和Iterator

### 11.13 Foreach与迭代器
 foreach 语句可以用于实现了iterable接口的类

 Collection类都是Iterable类型（但是不包括各种Map）
 #### 11.13.1 适配器方法惯用法

 如何实现多种在foreach语句中使用这个类的方法
 

 ## 第15章 泛型
 用于编写比接口更加通用的代码
 泛型的概念：实现了参数化类型使得代码适用于多种类型

 ### 15.1与C++的比较

 ### 15.2 简单泛型
泛型的主要目的之一就是用来指定容器要持有什么å类型的对象，而且由编译器来保证类型的正确性


Java 泛型的核心概念：告诉编译器想使用什么类型，然后编译器帮你处理一切细节
#### 15.2.1一个元组类库
元组的概念：它将一组对象直接打包存储与气宗的一个单一对象，这个容器对象允许读取其中元素，但是不允许想起中存放新的对象
元组可以具有任何长度，同时元祖的对象可以是任意不同的类型

持有多个对象的元组

有了泛型可以很容易的创建元组

#### 15.2.2一个堆栈类

### 泛型接口
使用泛型定义接口
在子类集成泛型接口时候必须指定泛型

### 泛型方法
定义泛型方法，只需要将泛型参数类标志置于返回值前

## 18章 Java I/O系统
### 18.1 File 类
File 文件的含义:既能表示一个特定文件的名称,又能代表一个目录下的一组文件的名称
#### 18.1.1 目录列表器
