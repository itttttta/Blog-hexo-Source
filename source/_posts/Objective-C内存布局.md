title: Objective-C内存布局
date: 2015-08-17 18:06:49
type: "tags"
tags: Objective-C
## Objective-C内存布局

---
对象(object)即一块内存，本文要探讨的是一个Objective-C对象在内存的布局(layout)问题，水果的官方文档有说，一个类(class)如果不需要从NSObject继承其某些特定的行为是不用继承NSObject的，这里我将讨论限制在继承了NSObject的类的对象范围内。

### 首先来看一下，NSObject的定义：
	@interface NSObject <NSObject> {	
         Class    isa;                      
     } 
     
(由于我们讨论的是内存布局，因此将其方法的定义撇开)

**在Objective-C中，@interface关键字可以看着是C语言中的struct关键字的别名，当然他还会有一些其它功能，比如说让编译器知道@interface后后面的是一个Objective-C的类的名字等。但就我们研究其内存布局来说，我们简单地将其替换为struct，并将protocal定义去掉。因此，NSObject的定义就是样：**
struct NSObject{
 　　Class isa;
}
