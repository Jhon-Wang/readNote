# Spring framework 
## Core Technologies
这部分参考文档完整介绍了spring framework 的所有的技术。

其中最重要的是spring framework 的控制反转（Inversion of Control，Ioc）容器。在介绍了spring framework 容器之后，紧随其后我们介绍了Aspect-Oriented Programming（AOP）技术。Spring Framework 拥有它自己的AOP 框架，这个在概念上比较容易理解，并且成功的解决了java 企业开发中的80%的AOP需求。
Spring 框架同样提供了和AspectJ的整合。

### The IoC container Ioc 容器
#### 1.1 Spring 的IoC容器和beans 介绍
这个章节覆盖了Spring 框架中Ioc 的实现原理。Ioc 可叫做DI(依赖注入)。这是一个对象定义他们依赖关系的过程，和他们一起工作的其他的对象，仅仅通过构造参数，传入工厂方法的参数或对象上设置的属性