#获取Cocoa类实例中任意类型的Ivar
直接用object_getIvar是获取不到基本数据类型的Ivar的，应该通过class_getInstanceVariable拿到Ivar之后，再用ivar_getOffset获取Ivar相对于实例本身的偏移。

```ObjectiveC
#import <objc/runtime.h>

template <typename IVAR>
__attribute__((always_inline))
auto getIvar(NSObject * object, const char * name) -> IVAR& {
    Ivar ivar = class_getInstanceVariable(object_getClass(object), name);
    if (ivar != NULL) {
        return *((IVAR *)(((long)object) + ivar_getOffset(ivar)));
    }

    NSException * exc = [NSException exceptionWithName:@"Cannot get ivar" reason:[NSString stringWithFormat:@"Cannot get ivar named '%s' from %@", name, [object description]] userInfo:nil];
    [exc raise];

    // just fool compiler
    // it's unsafe if we really dereference nullptr
    return *((IVAR *)0);
}
```
需要使用ObjectiveC++混编，实际使用的例子如下

```ObjectiveC

@interface Data : NSObject {
@private
    int32_t b_int32;
    CGSize _size;
    CGSize _another_size;
}

@end

@implementation Data

- (instancetype)init {
    self = [super init];
    if (self) {
        self->b_int32 = 233;
        self->_size = CGSizeMake(0.233, 0.2333);
        self->_another_size = CGSizeMake(233, 2333);
    }
    return self;
}

@end

@implementation ViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    Data * data = [[Data alloc] init];

    @try {
        // get
        NSLog(@"data->b_int32: %d", getIvar<int>(data, "b_int32"));

        // modify
        getIvar<int>(data, "b_int32") = 234;
        NSLog(@"data->b_int32: %d", getIvar<int>(data, "b_int32"));

        // get struct
        NSLog(@"data->_size.height: %lf", getIvar<CGSize>(data, "_size").height);
        NSLog(@"data->_size.width: %lf", getIvar<CGSize>(data, "_size").width);
        NSLog(@"data->_another_size.height: %lf", getIvar<CGSize>(data, "_another_size").height);
        NSLog(@"data->_another_size.width: %lf", getIvar<CGSize>(data, "_another_size").width);

        // get some non-exist ivar
        // throws
        getIvar<int>(data, "_int32");
    } @catch (NSException *exception) {
        NSLog(@"exception: %@", [exception reason]);
    } @finally {

    };
}
```