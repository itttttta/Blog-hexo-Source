title: IOS距离传感器
date: 2015-08-24 08:59:42
tags: IOS

---
```

#import "ViewController.h"

@interface ViewController ()

@end

@implementation ViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    // Do any additional setup after loading the view, typically from a nib.
    
    // 1.开启距离传感器(注意: 默认情况距离传感器是关闭的)
//    [UIApplication sharedApplication].proximitySensingEnabled = YES;
    // 只要开启之后, 就开始实时监听
    [UIDevice currentDevice].proximityMonitoringEnabled = YES;
    
    // 2.当监听到有物体靠近设备时系统会发出通知
    [[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(proximityStateDidChange:) name:UIDeviceProximityStateDidChangeNotification object:nil];
    
}
- (void)dealloc
{
    [[NSNotificationCenter defaultCenter] removeObserver:self];
}

// 当监听到有物体靠近设备时调用
- (void)proximityStateDidChange:(NSNotification *)note
{
//    NSLog(@"%@", note);
   if( [UIDevice currentDevice].proximityState)
   {
       NSLog(@"有物体靠近");
   }else
   {
       NSLog(@"物体离开");
   }
}




@end
```
