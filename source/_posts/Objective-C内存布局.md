title: Objective-C内存布局(转)
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

在Objective-C中，@interface关键字可以看着是C语言中的struct关键字的别名，当然他还会有一些其它功能，比如说让编译器知道@interface后后面的是一个Objective-C的类的名字等。但就我们研究其内存布局来说，我们简单地将其替换为struct，并将protocal定义去掉。因此，NSObject的定义就是样：

 	struct NSObject{
 		Class isa;
 	}
 那个这个Class又是什么呢？在objc.h中我们发现其仅仅是一个结构(struct)指针的typedef定义:
	
	typedefstruct objc_class *Class;
 因此，NSObject的定义就像这个样子：
	
	struct NSObject{
		objc_class *isa
	}
	
	
isa就是“isa”，对于所有继承了NSObject的类其对象也都有一个isa指针。这个isa指针指向的东西(先这样称呼它吧)就是关于这个对象所属的类的定义。

　　刨根问底是我们程序员的天性：那object_class的定义是什么呢？由于水果公司现在将这个定义隐藏起来了，不过我依然有办法，用XCode随便建一个工程，在某个变量定义处打个debug断点，然后通过XCode的GUI或者用gdb的p命令查看其结构，这里我使用gdb打印一个UINavigationController变量，我们看到只是一个指针而已：
	
	(gdb) p dialUNC
	$1 = (UINavigationController *) 0x8e8be80
对指针解引用再打印，我们发现里面有很多很多东西，大致如下(由于gdb打印出来内容太多，省略号表示省略了一些内容)：

```
(gdb) p *dialUNC
$1 = {
  <UIViewController> = {
    <UIResponder> = {
      <NSObject> = {
        isa = 0x1bebc1c
      }, <No data fields>}, 
members of UIViewController: 
    _view = 0xd5dab60, 
    _tabBarItem = 0x0, 
    _navigationItem = 0x0, 
    _toolbarItems = 0x0, 
    _title = 0x0, 
    _nibName = 0x0, 
    ......(此处省略若干成员，课蜜黄蜂注)
  }, 
members of UINavigationController: 
  _containerView = 0xd5dab60, 
  _navigationBar = 0xd5dad40, 
  _navigationBarClass = 0x1beb4d8, 
  _toolbar = 0x0, 
  _navigationTransitionView = 0xd5d2f10, 
  _currentScrollContentInsetDelta = {
    top = 0, 
    left = 0, 
    bottom = 0, 
    right = 0
  }, 
  _previousScrollContentInsetDelta = {
    top = 0, 
    left = 0, 
    bottom = 0, 
    right = 0
  }, 
  ......（此处省略若干成员，课蜜黄蜂注） 
  }
}
```
注意gdb打印结果中的黑体字，从中我们可以看到，UINavigationController内存中先是存放了父类的实例变量再存放子类的实例变量。最前面的那个isa指针就是在NSObject中所定义的。由于Objective-C中没有多继承，因此其内存布局还是很简单的，就是：最前面有个isa指针，然后父类的实例变量存放在子类的成员变量之前，so easy!!!但还有一个问题，我们很好奇，这个isa是什么呢？对它解引用再打印内容大致如下：

```
(gdb) p *dialUNC->isa
$2 = {
    isa = 0x1bebc30, 
    super_class = 0x1bebba4, 
    name = 0xd5dd8d0 "?", 
    version = 45024840, 
    info = 223886032, 
    instance_size = 43102048, 
    ivars = 0x1bebb7c, 
    methodLists = 0xd5dab10, 
    cache = 0x2af0648, 
    protocols = 0xd584050
}
```
这就是一个Class或者说objc_class结构在内存中的样子。其实在Objective-C2.0之前这个结构的定义是暴露给用户的，但在Objective-C2.0中，水果公司将它隐藏起来了。经过在网上的查找，发现在Objective-C2.0之前其定义大致如下：

```
struct objc_class {
    Class isa;
    
    Class super_class;
    
    const char *name;
    
    long version;
    long info;
    
    long instance_size;
    struct objc_ivar_list *ivars;
    struct objc_method_list **methodLists; 
    
    struct objc_cache *cache;
    struct objc_protocol_list *protocols;   
}
```
因此简单地说，一个objc_class对象包括一个类的：父类定义(super_class), 变量列表，方法列表，还有实现了哪些协议(Protocal)等等。

　　"等一下"，有人要喊了，"我们刚才在说一个对象里面有一个isa指针，这个指针的定义是objc_class,肿么这个objc-class中还有一个isa？"

　　在这里有必要跟大家啰嗦一大段文字了：在Objective-C中任何的类定义都是对象。即在程序启动的时候任何类定义都对应于一块内存。在编译的时候，编译器会给每一个类生成一个且只生成一个”描述其定义的对象”,也就是水果公司说的类对象(class object),他是一个单例(singleton), 而我们在C++等语言中所谓的对象，叫做实例对象(instance object)。对于实例对象我们不难理解，但类对象(class object)是干什么吃的呢？我们知道Objective-C是门很动态的语言，因此程序里的所有实例对象(instace objec)都是在运行时由Objective-C的运行时库生成的，而这个类对象(class object)就是运行时库用来创建实例对象(instance object)的依据。

　　让我们来理一下，到目前为止，我们知道了：任何直接或间接继承了NSObject的类，它的实例对象(instacne objec)中都有一个isa指针，指向它的类对象(class object)。这个类对象(class object)中存储了关于这个实例对象(instace object)所属的类的定义的一切：包括变量，方法，遵守的协议等等。

　　再回到之前的问题，肿么这个实例对象(instance object)的isa指针指向的类对象(class object)里面还有一个isa呢？

　　这个类对象(class objec)的isa指向的依然是一个objc-class，它就是“元类对象”(metaclass object)，它和类对象(class object)的关系是这样的:  类对象(class object)中包含了类的实例变量，实例方法的定义，而元类对象(metaclass object)中包括了类的类方法(也就是C++中的静态方法)的定义。类对象和元类对象中水果公司当然还会包含一些其它的东西，以后也可能添加其它的内容，但对于我们了解其内存布局来说，只需要记住：类对象存的是关于实例对象的信息(变量，实例方法等)，而元类对象(metaclass object)中存储的是关于类的信息(类的版本，名字，类方法等)。要注意的是，类对象(class object)和元类对象(metaclass object)的定义都是objc_class结构，其不同仅仅是在用途上，比如其中的方法列表在类对象(instance object)中保存的是实例方法(instance method)，而在元类对象(metaclass object)中则保存的是类方法(class method)。关于元类对象水果官方文档" The Objective-‐C Programming Language "P29页顶部描述如下：

　　Note: The compiler also builds a metaclass object for each class. It describes the class object just as the class object describes instances of the class. But while you can send messages to instances and to the class object, the metaclass object is used only internally by the runtime system. 

　　这一大段文字好像有点绕，那我们来看一个例子。下面我以一个有4层继承关系的类的实例变量的内存布局为例。　继承关系如下：
　　
	![id](https://app.yinxiang.com/shard/s39/res/c2c0004f-7702-4978-b2d2-c41482f75344.jpg?resizeSmall&width=846)
　　
　　通过打印D3类的一个实例变量并将那些isa,super_class的地地址记录下来整理得到的关系如下图:

![id](https://app.yinxiang.com/shard/s39/res/2eaaccde-e5d6-4635-a3c5-982bfaaa0892.png?resizeSmall&width=846)

在这里对上图进行一下解释： 矩形表示对象(object),即一块内存;箭头表示指针，isa即isa指针，super表示super_class指针，这些指针是箭头尾部对象(object)的成员变量，除了“D3实例对象”(最左边的对象)，其它对象都是在程序一启动就创建在在内存中的了而且都是单例(singleton)，类对象(class object)和元类对象(metaclass object)只是用途不一样，其定义都为objc_class结构


　　D3对象的内存布局为:从前往后为isa,D1的实例变量,D2的实例变量,D3的实例变量。而isa指针指向的内容就是上图中的“D3类对象”。对于上图，任何类C如果直接或间接继承NSObject 或者其就是NSObject,则有如下结论：

　　**1. 类C的类对象(class object)的super_class都指向了类C父类的类对象(class object), NSObject的类对像的super_class指向0x0**

**　　2. 类C的类对象(class object)的isa指针都指向他的元类对象(metaclass object)
**

**　　3. 类C的元类对象(metaclass object)的super_class指针指向父类的元类对象(metaclass object), 例外：NSObject的元类对象(metaclass object)的super_class指向NSObject的类对象(class object).
**

**　　4. 类C的元类对象(metaclass object)的isa指针指都指向NSObject的元类对象(metaclass object)
**
　　NSObject的实例对象(虽然它没有实例变量和实例方法但这个对象仍然存在)其super_class指向地址0x0，因为NSObject没有父类, 这满足上面的结论1。　
　　NSObject的实例对象的isa指向了NSObject的元类对象(metaclass object)，这满足上面结论2。　　
　　NSObject的元类对象(metaclass object)指向了自己，这也满足上面结论4。　　
　　但NSObject的元类对象(metaclass object)的super_class指向了NSObject的类对象(class object)，我没有看出什么规律可言或者苹果为什么要这样做，我只能说“Apple just do this, I don't know why”（如果有人知道，麻烦告诉我一下，多谢）。我认为水果的工程师们只是简单地又将它指向NSObject的类对象(class object)，其实我认为这个super_class指针赋0x0也未尝不可(这样就满足上面的结论3， 因为NSObject没有父类，所以它的metaclass object的super_class指向0x0，我觉得这样更统一。当然只是我的yy罢了)。 

`原文地址:http://www.cnblogs.com/csutanyu/archive/2011/12/12/Objective-C_memor_layout.html　`

　　