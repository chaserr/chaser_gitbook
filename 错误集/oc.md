1. 在多线程的情况下，使用 strong 来修饰属性会出现什么情况？

```c
@interface ViewController ()
@property (nonatomic， strong) NSString *str;
@end

-----.m
for (int i = 0; i < 10000 ; i++) {
        dispatch_async(dispatch_async(dispatch_get_global_queue(0, DISPATCH_QUEUE_PRIORITY_DEFAULT), ^{
            self.str = [NSString stringWithFormat:@"Monkey-Sun %d",i];
                NSLog(@"%@   %lu", self.str, (unsigned long)self.str.retainCount);
        });
    }
```
> 这里会崩溃，因为，使用strong修饰的属性，实际上它的setter是这样的.

```c
-(void)setStr:(NSString *)str{
    [str retain];
    [_str release];
    _str = str;
}
```
在多线程下，线程1，如果执行到了release,同时线程2也执行到了release，那么_str所指向的对象就会过度释放。
解决方案：这里可以加一个同步互斥锁
```c
@synchronized (self) {
self.str = [NSString stringWithFormat:@"Monkey-Sun %d",i];
NSLog(@"%@ %lu", self.str, (unsigned long)self.str.retainCount);
}
```

```swift
fileprivate var str : String!
    override init() {
        super.init()
        
        let queue = DispatchQueue.global()
        
        for i in 0...10000{
            queue.async {
            
            /*swift的互斥锁
            objc_sync_enter(self)
                code...
            objc_sync_exit(self)
            */
                objc_sync_enter(self)
                self.str = String("\(i)")
                print(self.str)
                objc_sync_exit(self)
            }
        }
    }
```
可以理解，多线程中，并发的访问一个数据，就会导致数据出错(资源争夺)。不过这里可以看到出问题的原因之一。
>类似案例：JSONModel，就有资源争夺的问题，它的 set 方法的生成是在多线程中执行的，那么当一个 model 返回多个同一类型的数据，就会 crash



2. 当图片的宽高过大的时候，SDWebImage一次性加载很多图片会导致内存暴涨，crash,通过设置 SD 的 iamgeCache 的一些限制参数也无用，
最后解决：对图片的宽高进行裁剪，因为 iOS的 drawRect的画布大小有限制，具体带下未知，但是不超过2048；



