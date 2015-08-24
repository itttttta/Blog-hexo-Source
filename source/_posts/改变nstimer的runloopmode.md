title: 改变nstimer的runloopmode
date: 2015-08-21 17:41:03
categories: NSRunLoop
tags: NSRunLoop

---
```
   NSRunLoop *rp  = [NSRunLoop currentRunLoop];
   [rp addTimer:self.topAdView.timer forMode:NSRunLoopCommonModes];
```
改变nstimer的runloop模式,防止uiscrollow滚动时影响定时器的正常使用