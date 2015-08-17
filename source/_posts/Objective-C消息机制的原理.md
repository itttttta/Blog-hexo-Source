title: Objective-C消息机制的原理
date: 2015-08-17 08:55:11
type: "tags"
tags: Objective-C
## Objective-C消息机制的原理

---
### 在Objective-C中，message与方法的真正实现是在执行阶段绑定的，而非编译阶段。

* 编译器会将消息发送转换成对objc_msgSend方法的调用。
* objc_msgSend方法含两个必要参数：receiver、方法名（即：selector），如：
* [receiver message]，将被转换为：objc_msgSend(receiver, selector)
* objc_msgSend方法也能hold住message的参数，如：
* objc_msgSend(receiver, selector, arg1, arg2, …);

### objc_msgSend方法会做按照顺序进行以下操作，以完成动态绑定	

1. 查找selector所指代的程序（方法的真正实现）。因为不同类对同一方法有不同的实现，所以对方法的真正实现的查找依赖于receiver的类
2. 调用该实现，并将一系列参数传递过去
3. 将该实现的返回值作为自己的返回值，返回之

#### 消息传递的关键是，编译器构建每个类和对象时所采用的数据结构。每个类都包含以下两个必要元素：

* 一个指向父类的指针
* 一个调度表（dispatch table）。该调度表将类的selector与方法的实际内存地址关联起来
* 每个对象都有一个指向所属类的指针isa。通过该指针，对象可以找到它所属的类，也就找到了其全部父类，如下图所示：
![ISA](http://dangpu-wordpress.stor.sinaapp.com/uploads/2012/07/iPhone_Message.png)

当向一个对象发送消息时，objc_msgSend方法根据对象的isa指针找到对象的类，然后在类的调度表（dispatch table）中查找selector。如果无法找到selector，objc_msgSend通过指向父类的指针找到父类，并在父类的调度表（dispatch table）中查找selector，以此类推直到NSObject类。一旦查找到selector，objc_msgSend方法根据调度表的内存地址调用该实现。 通过这种方式，message与方法的真正实现在执行阶段才绑定。
 
为了保证消息发送与执行的效率，系统会将全部selector和使用过的方法的内存地址缓存起来。每个类都有一个独立的缓存，缓存包含有当前类自己的 selector以及继承自父类的selector。查找调度表（dispatch table）前，消息发送系统首先检查receiver对象的缓存。缓存命中的情况下，消息发送（messaging）比直接调用方法（function call）只慢一点点点点。
  
#### 关于Selector，什么 是Selector，Selector就是一个字符串，用来表示一个方法。

在Objective-C中，消息是直到运行的时候才和方法实现绑定的。编译器会把一个消息表达式，

`[receiver message]`

转换成一个对消息函数objc_msgSend的调用。该函数有两个主要参数：消息接收者和消息对应的方法名字——也就是方法选标：


`objc_msgSend(receiver, selector)
`

同时接收消息中的任意数目的参数：

`objc_msgSend(receiver, selector, arg1, arg2, ...)
`
该消息函数做了动态绑定所需要的一切：

**它首先找到选标所对应的方法实现。因为不同的类对同一方法可能会有不同的实现，所以找到的方法实现依赖于消息接收者的类型。**

**然后将消息接收者对象（指向消息接收者对象的指针）以及方法中指定的参数传给找到的方法实现。**

**最后，将方法实现的返回值作为该函数的返回值返回。**

**注意：编译器将自动插入调用该消息函数的代码。您无须在代码中显示调用该消息函数。**





