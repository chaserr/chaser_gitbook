# OC基础知识



## self.property和_property的区别
假如类classA有一个属性name.
关于那么关于在classA的init方法里使用`self.name`和`_name`的区别
- `self.name`会调用set方法。
- _name是c++的访问方式，直接对变量进行操作

eg:
``` objectivec

     // sample1
    NSMutableString *str = [[NSMutableString alloc] initWithString:@"demo1"];
    self.name = str;
    NSLog(@"%p, %p, %@", str, _name ,_name);
    [str appendString:@"demo2"];
    NSLog(@"%p, %p, %@", str, _name, _name);
    
    // output
//    2016-06-18 16:45:52.652 OCTest[3550:1466753] 0x7f8059c0f730, 0xa0000316f6d65645, demo1
//    2016-06-18 16:45:52.652 OCTest[3550:1466753] 0x7f8059c0f730, 0xa0000316f6d65645, demo1

    // simple2
    NSMutableString *str = [[NSMutableString alloc] initWithString:@"demo1"];
    _name = str;
    NSLog(@"%p, %p, %@", str, _name ,_name);
    [str appendString:@"demo2"];
    NSLog(@"%p, %p, %@", str, _name, _name);
    
    // output
//    2016-06-18 16:46:42.598 OCTest[3567:1467577] 0x7f935a7330b0, 0x7f935a7330b0, demo1
//    2016-06-18 16:46:42.598 OCTest[3567:1467577] 0x7f935a7330b0, 0x7f935a7330b0, demo1demo2

```

这里可以看到，采用不同的方式赋值，结果就会不一样。那是什么原因导致的呢：
1. 我们队NSString声明的方式是copy而不是retain或者其他，
2. self.name的访问方式会自动进行一次copy操作，所以在第一个例子中str和name的地址是不同的，而_name的操作是直接的对方赋值操作，不会有copy行为，只是引用计数的增加，所以内存地址一样。

在一般的赋值行为中，无论是self.name还是_name也是影响不大的。但是嘛，在一个类的init的方法中，是有区别的，具体可以看苹果的官方推荐：不建议使用`self.name`,而是使用`_name`

## init方法里的self.property和_property的区别

苹果官方建议，在任何的init方法中不要使用self.property的方式访问，因为如果子类中重写了setProperty方法，那么在父类的init函数中调用的就是子类的setProperty方法，这样可能会产生一些不容易预知的情况。
eg:

``` objectivec
@interface TXXView ()
@property (nonatomic, copy) NSString *name;
@end

@implementation TXXView

- (instancetype)initWithName:(NSString *)name
{
    self = [super init];
    if (self) {
      
        self.name = name;
        
    }
    return self;
}

- (void)setName:(NSString *)name{

    NSLog(@"%@", [[self class] description]);
}

@end
```

``` objectivec

// .h文件  继承TXXView
#import "TXXView.h"

@interface TXXView_Subview : TXXView

@end

// .m文件

```

然后在main函数或者viewcontroller里对子类实例化：
``` objectivec
    TXXView_Subview *subView = [[TXXView_Subview alloc] initWithName:@"TXX"];

```

在两个类的setName方法里打上断点可以得知，走的是子类的set方法、通过打印也可以看出
``` objectivec
2016-06-18 17:01:15.245 OCTest[3803:1486607] TXXView_Subview
```

`TXXView_Subview`是`TXXView`的子类，`TXXView_Subview`没有实现自己的初始化方法，实际是调用了父类的初始化方法，但是由于子类重写了setName的方法，导致父类的self.name ＝ name实际上是调用了子类中的setName方法。但这其实和我们之前说的是有矛盾的，当传入一个mutable对象的时候你不得不使用self.property的方式来存储变量，或者使用对_property进行copy或者mutablecopy的操作。我建议这种情况下还是使用后者吧！这也是苹果官方推荐的使用方法
``` objectivec
- (instancetype)initWithName:(NSString *)name
{
    self = [super init];
    if (self) {
      // 使用copy来存储
        _name = [name copy];
        
    }
    return self;
}

```


建议POST请求参数字典的写法,这样比较装逼~
```c
// NSDictionaryOfVariableBindings这个宏生成一个字典,这个宏可以生成一个变量名到变量值映射的Dictionary,比如:
NSNumber * packId=@(2);
NSNumber *userId=@(22);
NSNumber *proxyType=@(2);
NSDictionary *param=NSDictionaryOfVariableBindings(packId,userId,proxyType);
```

子类修改父类私有变量和方法
http://www.iosfly.com/2014/05/14/SubClass-modify-SupClass-Property-Protocol/


