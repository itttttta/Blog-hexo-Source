title: ImageWithColor
date: 2015-08-19 16:23:27
type: "tags"
tags: 实用小工具

---
```
@interface UIImage (Color)  
  
+ (UIImage *)imageWithColor:(UIColor *)color size:(CGSize)size;  
  
@end  
  
@implementation UIImage (Color)  
  
+ (UIImage *)imageWithColor:(UIColor *)color size:(CGSize)size  
{  
    CGRect rect = CGRectMake(0, 0, size.width, size.height);  
    UIGraphicsBeginImageContext(rect.size);  
    CGContextRef context = UIGraphicsGetCurrentContext();  
    CGContextSetFillColorWithColor(context,color.CGColor);  
    CGContextFillRect(context, rect);  
    UIImage *img = UIGraphicsGetImageFromCurrentImageContext();  
    UIGraphicsEndImageContext();  
      
    return img;  
}  
@end 
```
