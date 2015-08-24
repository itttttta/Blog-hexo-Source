title: IOS-CoreMotion
date: 2015-08-24 09:04:52
tags: IOS

---
```
#import "ViewController.h"
#import <CoreMotion/CoreMotion.h>

@interface ViewController ()
@property (nonatomic, strong) CMMotionManager *mgr;
@end

@implementation ViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    
    // 1.创建coreMotion管理者
    self.mgr = [[CMMotionManager alloc] init];
    
     if (self.mgr.isAccelerometerAvailable) {
          // 3.开始采样
         [self.mgr startAccelerometerUpdates]; // pull
     }else
     {
         NSLog(@"加速计不可用");
     }

}

- (void)touchesBegan:(NSSet *)touches withEvent:(UIEvent *)event
{
    CMAcceleration acceleration = self.mgr.accelerometerData.acceleration;
    
     NSLog(@"x = %f y = %f z = %f", acceleration.x, acceleration.y , acceleration.z);
}

- (void)push
{
    // 1.创建coreMotion管理者
    //    CMMotionManager *mgr = [[CMMotionManager alloc] init];
    self.mgr = [[CMMotionManager alloc] init];
    
    // 2.判断加速计是否可用
    if (self.mgr.isAccelerometerAvailable) {
        /*
         isAccelerometerActive 是否正在采集
         accelerometerData 采集到得数据
         startAccelerometerUpdates  pull
         startAccelerometerUpdatesToQueue  push
         stopAccelerometerUpdates 停止采集
         accelerometerUpdateInterval 采样时间
         */
        // 3.设置采样时间
        self.mgr.accelerometerUpdateInterval = 1 / 30.0;
        // 4.开始采样
        
        [self.mgr startAccelerometerUpdatesToQueue:[NSOperationQueue mainQueue] withHandler:^(CMAccelerometerData *accelerometerData, NSError *error) {
            // 这个block是采集到数据时就会调用
            if (error) return ;
            CMAcceleration acceleration = accelerometerData.acceleration;
            NSLog(@"x = %f y = %f z = %f", acceleration.x, acceleration.y , acceleration.z);
        }];
    }else
    {
        NSLog(@"加速计不可用");
    }
}





@end

```
