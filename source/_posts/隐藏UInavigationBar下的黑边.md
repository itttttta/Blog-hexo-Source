title: 隐藏UInavigationBar下的黑边
date: 2015-08-19 16:55:22
type: "tags"
tags: 实用小工具

---
本文参考[StackOverFlow](http://stackoverflow.com/questions/19226965/how-to-hide-ios7-uinavigationbar-1px-bottom-line)
### 在基类中
```
   UINavigationBar *navigationBar = self.navigationController.navigationBar;
    [navigationBar setBackgroundImage:[UIImage imageWithColor:KTHEME_COLOR size:CGSizeMake(SCREEN_WIDTH, 1)]
                       forBarPosition:UIBarPositionAny
                           barMetrics:UIBarMetricsDefault];
    [navigationBar setShadowImage:[UIImage new]];
    
```
imageWithColor 是一个分类在前边文章中出现过