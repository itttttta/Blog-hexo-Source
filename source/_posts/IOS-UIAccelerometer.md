title: IOS-UIAccelerometer
date: 2015-08-24 09:01:57
tags: IOS

---
```
#import "ViewController.h"
#import "UIView+Extension.h"

@interface ViewController ()<UIAccelerometerDelegate>
/**
 *  小球
 */
@property (weak, nonatomic) IBOutlet UIImageView *imageBall;
/**
 *  保存速度
 */
@property (nonatomic, assign) CGPoint  velocity;
@end

@implementation ViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    // Do any additional setup after loading the view, typically from a nib.
    
    // 1.利用单利获取采集对象
    UIAccelerometer *acc = [UIAccelerometer sharedAccelerometer];
    // 2.设置代理
    acc.delegate = self;
    // 3.设置采样时间
    acc.updateInterval = 1 / 30;

}
#pragma mark -UIAccelerometerDelegate
// 4.实现代理方法
/**
 *  只要采集到数据就会调用(调用频率非常高)
 *
 *  @param accelerometer 触发事件的对象
 *  @param acceleration  获取到得数据
 */
- (void)accelerometer:(UIAccelerometer *)accelerometer didAccelerate:(UIAcceleration *)acceleration
{
    NSLog(@"x = %f / y = %f / z = %f", acceleration.x, acceleration.y, acceleration.z);
    
    /*
     速度 = 加速度 * 时间
     V = at;  ==  a * t1 + a * t2 + a * t3 ....;
     */
    // 不能直接修改对象的结构体属性的成员
//    self.velocity.x += acceleration.x;
    _velocity.x += acceleration.x;
    // -=的原因是因为获取到得Y轴的加速度和UIKit的坐标系的Y的值是相反的, 而我们将来想让小球往加速度的反方向运动, 所以 -=;
    _velocity.y -= acceleration.y;
    
    /*
     
    移动的距离 = 速度 * 时间
    S = vt; == v * t1 + v * t2 + v * t3 ....;
     */
    self.imageBall.x += _velocity.x;
    self.imageBall.y += _velocity.y;
    
    // 边界检测
    if (self.imageBall.x <= 0) {
        // 矫正小球当前的位置
        self.imageBall.x = 0;
        // 超出了屏幕的左边
        _velocity.x *= -0.5;
    }
    if (self.imageBall.y <= 0) {
        // 矫正小球当前的位置
        self.imageBall.y = 0;
        // 超出屏幕的顶部
        _velocity.y *= -0.5;
    }
    
    if (CGRectGetMaxY(self.imageBall.frame) >= self.view.height) {
        // 矫正小球当前的位置
        self.imageBall.y = self.view.height - self.imageBall.height;
        // 查出屏幕的底部
        _velocity.y *= -0.5;
    }
    
    if (CGRectGetMaxX(self.imageBall.frame) >= self.view.width) {
        // 矫正小球当前的位置
        self.imageBall.x = self.view.width - self.imageBall.width;
        // 查出屏幕的右边
        _velocity.x *= -0.5;
    }
    
}
@end

```
