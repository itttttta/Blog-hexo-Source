title: block写递归
date: 2015-08-20 15:34:42
type: "tags"
tags: Block

---
```
static int (^sumBlock)(int) = ^ (int num) {
    if (num == 0) {
        return num;
    }
    return num + sumBlock(num - 1);
};
```
**注意，要做到自己调用自己，需要能够准确的在内存中找到 block 的函数入口，因此需要使用 static 修饰符号，其他就没啥了**

* 每调用一次自己，系统都会开辟一个栈桢记录临时变量和参数
* 递归次数过多，会出现栈溢出错误
* 移动开发中不建议使用递归算法，现在主线程栈区只有 512K
