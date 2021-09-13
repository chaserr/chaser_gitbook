# 宏定义和\_\_attribute\_\_

在学习开源代码的时候，经常会遇到`__attribute__`的相关使用，但是在使用的时候又会忘记，所以用这篇文章总结下iOS开发中经常使用的attribute。  
`__attribute__`其实是GNU C的一种机制，本质是一个编译器的指令，在声明的时候可以提供一些属性，在编译阶段起作用，来做多样化的错误检查和高级优化。可以分别用于设置函数属性、变量属性和类型属性，语法一般为: `__attribute__(xx)`。下面分别介绍下这些属性。

## 函数属性 <a id="&#x51FD;&#x6570;&#x5C5E;&#x6027;"></a>

函数属性可以帮助开发者把一些特性添加到函数声明中，从而可以使编译器在错误检查方面的功能更强大。

**format**  
语法为`__attribute__((format(__NSString__, F, A)))`，可以给被声明的函数加上类似printf或者scanf的特征，它可以使编译器检查函数声明和函数实际调用参数之间的格式化字符串是否匹配。`format (archetype, m, n)`，第一个参数传递archetype指定为哪种类型，string-index指定格式化字符串的位置，n指定可变参数检查开始的位置。例如:  
\#define NS\_FORMAT\_FUNCTION\(F,A\) \_\_attribute\_\_\(\(format\(\_\_NSString\_\_, F, A\)\)\)  FOUNDATION\_EXPORT void NSLog\(NSString \*format, ...\) NS\_FORMAT\_FUNCTION\(1,2\);extern void myPrint\(const char \*format,...\)\_\_attribute\_\_\(\(format\(printf,1,2\)\)\);

**objc\_designated\_initializer**  
指定内部实现的初始化方法，系统宏`NS_DESIGNATED_INITIALIZER`展开即为该指令，语法为`__attribute__((objc_designated_initializer))`。例如：  
- \(instancetype\)initNoDesignated ;- \(instancetype\)initNoDesignated1 NS\_DESIGNATED\_INITIALIZER;

当一个类存在方法带有NS\_DESIGNATED\_INITIALIZER属性时，它的NS\_DESIGNATED\_INITIALIZER方法必须调用super的NS\_DESIGNATED\_INITIALIZER方法。它的其他方法（非NS\_DESIGNATED\_INITIALIZER）只能调用self的方法初始化。

**objc\_requires\_super**  
表示子类重写当前类的方法时，必须要调用super函数，否则会有警告。语法为`__attribute__((objc_requires_super))`，例如：  
`- (void)fatherMethod __attribute__((objc_requires_super));`

**constructor/destructor**  
表明该函数在mian函数被调用前/后调用，语法为`__attribute__((constructor/destructor))`。例如：  
static \_\_attribute\_\_\(\(constructor\)\) void before\(\) {    printf\("Hello"\);}static \_\_attribute\_\_\(\(destructor\)\) void after\(\) {    printf\(" World!"\);}int main\(int argc, const char \* argv\[\]\) {    printf\("1"\);    return 0;}

constructor和`load`方法都是在main函数执行前调用，但是`load`更早一些。

**warn\_unused\_result**  
指明一定要使用函数的返回值，否则会有警告，语法为`__attribute__((warn_unused_result))`。例如：  
- \(int\)mustUseReturnValue \_\_attribute\_\_\(\(warn\_unused\_result\)\) {    return 0;}

**nonnull**  
编译器对参数进行NULL检查，参数类型必须是指针类型，语法为`__attribute__((nonnull (x)))`，x指第x参数不能为空。例如：  
- \(int\)addNum1:\(int \*\)num1 num2:\(int \*\)num2  \_\_attribute\_\_\(\(nonnull \(1\)\)\){    return  \*num1 + \*num2;}

类似于iOS系统宏NS\_ASSUME\_NONNULL\(参考最后\)。

**sentinel**  
指明变参函数需要nil作为边界，语法为`__attribute__((sentinel))`，例如：  
- \(void\)endWithNil:\(nonnull id\)first, ... \_\_attribute\_\_\(\(sentinel\)\);

**noreturn**  
通知编译器当前函数允许不返回值。当遇到类似函数还未运行到return语句就需要退出来的情况，该属性可以避免出现错误信息。语法为`__attribute__((noreturn))`。  
例如：  
//AFURLConnectionOperation.m+ \(void\) \_\_attribute\_\_\(\(noreturn\)\) networkRequestThreadEntryPoint:\(id\)\_\_unused object {    do {        @autoreleasepool {            \[\[NSRunLoop currentRunLoop\] run\];        }    } while \(YES\);}

**always\_inline/noinline**  
表示函数强制内联，语法为`__attribute__((always_inline/noinline))`。例如：`void test(int a) __attribute__((always_inline));`。  
编译器编译内联函数时会直接将函数插入到调用的地方，所以没有普通函数调用时的额外开销\(压栈/跳转/返回\)。但是是否形成内联函数，需要看编译器对该函数定义的具体处理，编译器可能会拒绝内联请求。

**unavailable**  
指明该方法不可用，系统宏`NS_UNAVAILABLE`的展开即为该指令，语法为`__attribute__((unavailable))`。例如：  
\#define NS\_AUTOMATED\_REFCOUNT\_UNAVAILABLE \_\_attribute\_\_\(\(unavailable\("not available in automatic reference counting mode"\)\)\)  - \(instancetype\)init NS\_UNAVAILABLE;- \(void\)method11 \_\_attribute\_\_\(\(unavailable\("不可使用"\)\)\);

**deprecated**  
表示当前方法/属性不推荐使用，编译时会报过时警告，系统宏`DEPRECATED_ATTRIBUTE`、`NS_DEPRECATED`的展开即为该指令，语法为`__attribute__((deprecated))`，deprecated后可跟注释。例如：  
- \(void\)method1 \_\_attribute\_\_\(\(deprecated\("使用\#method2"\)\)\);- \(void\)method2 DEPRECATED\_ATTRIBUTE; //DEPRECATED\_ATTRIBUTE是系统的宏

**enable\_if**  
用来实现C函数参数的静态检查，例如：  
static void printValidAge\(int age\)\_\_attribute\_\_\(\(enable\_if\(age &gt; 0, "当前参数有问题～"\)\)\) {    printf\("%d", age\);}

在函数声明中，可以同时使用多个属性，用`,`隔开即可。

## 类型/变量属性 <a id="&#x7C7B;&#x578B;-&#x53D8;&#x91CF;&#x5C5E;&#x6027;"></a>

**objc\_subclassing\_restricted**  
指明当前类型不能有子类，相当于final关键字，语法为`__attribute__((objc_subclassing_restricted))`。例如：  
\_\_attribute\_\_\(\(objc\_subclassing\_restricted\)\)@interface FinalObject : NSObject

**objc\_root\_class**  
表示当前类是一个根类，例如NSObject，NSProxy等。例如：  
//NSObject\_\_OSX\_AVAILABLE\_STARTING\(\_\_MAC\_10\_0, \_\_IPHONE\_2\_0\)OBJC\_ROOT\_CLASS@interface NSObject &lt;NSObject&gt; {    Class isa  OBJC\_ISA\_AVAILABILITY;}

**NSObject**  
CFDictionaryRef属于CoreFoundation框架的,也就是非OC对象,加上attribute\(\(NSObject\)\)后,myDictionary的内存管理会被当做OC对象来对待。例如：  
`@property (nonatomic,strong) __attribute__((NSObject)) CFDictionaryRef myDictionary;`

**objc\_runtime\_name**  
可以修改类/协议在runtime中的名称，语法为`__attribute__((objc_runtime_name("xxx")))`，可以使用该特性做代码混淆。例如：  
\_\_attribute\_\_\(\(objc\_runtime\_name\("NewFather"\)\)\)@interface Father : NSObject@end

**unused**  
表示变量即使不使用，也不会产生警告，语法为`__attribute__((unused))`。例如：`NSInteger i __attribute__((unused)) = 10;`。

**objc\_boxable**  
可以将struct/union类型包装成NSValue对象，语法为`__attribute__((objc_boxable))`。例如：  
typedef struct \_\_attribute\_\_\(\(objc\_boxable\)\) {    CGFloat x, y, width, height;} XXRect;

这样就可以这样使用结构体了：`XXRect rect2 = {1, 2, 3, 4}; NSValue *value2 = @(rect2);`。

**aligned**  
表示所作用的结构陈冠对齐在n字节自然边界上，语法为`__attribute__((aligned(x)))`。例如：  
typedef struct{    char  member1;    int   member2;    short member3;}\_\_attribute\_\_ \(\(aligned \(8\)\)\) Family;

**packed**  
指定的结构结构体按照一字节对齐，语法为`__attribute__((packed(x)))`。例如：  
123456typedef struct {    char    version;    int16\_t sid;    int32\_t len;    int64\_t time;}\_\_attribute\_\_ \(\(packed\)\) Header;

常用语与协议相关的网络传输中。

**cleanup**  
作用于变量，当变量的作用域结束是，会调用指定的函数，语法为`__attribute__((cleanup))`。该属性可以作用于变量，block，例如：  
- \(void\)createXcode{    NSObject \*xcode \_\_attribute\_\_\(\(cleanup\(xcodeCleanUp\)\)\) = \[NSObject new\];    NSLog\(@"%@",xcode\);}// 指定一个cleanup方法，注意入参是所修饰变量的地址，类型要一样// 对于指向objc对象的指针\(id \*\)，如果不强制声明\_\_strong默认是\_\_autoreleasing，会造成类型不匹配  static void xcodeCleanUp\(\_\_strong NSObject \*\*xcode\){    NSLog\(@"cleanUp call %@",\*xcode\);}

相关的示例，可以看下[黑魔法attribute\(\(cleanup\)\)](http://blog.sunnyxx.com/2014/09/15/objc-attribute-cleanup/)

## Clang Attributes <a id="Clang-Attributes"></a>

Clang Attributes是Clang提供的一种源码注解，方便开发者向编译器表达某种要求，

**availability**  
指明API\(变量/方法\)版本的变更，语法为`__attribute__((availability(macosx,introduced=m,deprecated=n)))`，m为引入的版本，n为废弃版本，在Cocoa的头文件中可以经常见到。例如：  
1\#define CF\_DEPRECATED\_IOS\(\_iosIntro, \_iosDep, ...\)    \_\_attribute\_\_\(\(availability\(ios,unavailable,introduced=\_iosIntro,deprecated=\_iosDep,message="" \_\_VA\_ARGS\_\_\)\)\)

其他的参数为: obsoleted为移除的版本，unavailable为不可用，message为警告信息。系统宏`NS_AVAILABLE`、`NS_CLASS_AVAILABLE`的展开即为该属性。

**overloadable**  
在C的函数上实现像java一样方法重载，语法为`__attribute__((overloadable))`，例如：  
\_\_attribute\_\_\(\(overloadable\)\) void add\(int num\){    NSLog\(@"Add Int %i",num\);}\_\_attribute\_\_\(\(overloadable\)\) void add\(NSString \* num\){    NSLog\(@"Add NSString %@",num\);}\_\_attribute\_\_\(\(overloadable\)\) void add\(NSNumber \* num\){    NSLog\(@"Add NSNumber %@",num\);}

## iOS中的宏 <a id="iOS&#x4E2D;&#x7684;&#x5B8F;"></a>

### **系统宏** <a id="&#x7CFB;&#x7EDF;&#x5B8F;"></a>

a. NS\_ASSUME\_NONNULL  
iOS的系统宏`NS_ASSUME_NONNULL_BEGIN`,`NS_ASSUME_NONNULL_END`，用该宏把整个类包起来，表明当前类的所有指针类型不能显示的置为nil。  
NS\_ASSUME\_NONNULL\_BEGIN@interface Food : NSObject@property \(nonatomic, copy\) NSString \*name;+ \(instancetype\)FoodWithName:\(NSString \*\)name;@endNS\_ASSUME\_NONNULL\_END

通常把这个宏和nullable一起使用。

b. 可用性的宏标记

|  |
| :--- |


### **常量/表达式宏** <a id="&#x5E38;&#x91CF;-&#x8868;&#x8FBE;&#x5F0F;&#x5B8F;"></a>

例如:  
\#define kTabBarH 49.0\#define kScreenH \[UIScreen mainScreen\].bounds.size.height\#define MKAssertMainThread\(\) NSAssert\(\[NSThread isMainThread\], @"This method must be called on the main thread"\)

**函数宏**  
例如：

```text
#ifndef YYSYNTH_DYNAMIC_PROPERTY_OBJECT
#define YYSYNTH_DYNAMIC_PROPERTY_OBJECT(_getter_, _setter_, _association_, _type_) \
- (void)_setter_ : (_type_)object { \
    [self willChangeValueForKey:@#_getter_]; \
    objc_setAssociatedObject(self, _cmd, object, OBJC_ASSOCIATION_ ## _association_); \
    [self didChangeValueForKey:@#_getter_]; \
} \
- (_type_)_getter_ { \
    return objc_getAssociatedObject(self, @selector(_setter_:)); \
}
#endif
```

还有一种变参宏，例如：  
`#define YYAssertNil(condition, description, ...) NSAssert(!(condition), (description), ##__VA_ARGS__)`  
在宏定义中`#`可以连接两个字符串或者将变量转化为C语言字符串，`##`则常用与连接，另外在逗号和**VA\_ARGS**之间的双井号，除了拼接前后文本之外，还有一个功能，那就是如果后方文本为空，那么它会将前面一个逗号吃掉。再例如：  
`#define weakify(object) autoreleasepool{} __weak __typeof__(object) weak##_##object = object;`

文章分别总结了常用的attribute属性和一些宏，如有相关问题，请评论指正。后续有其他内容会陆续补充到文章中。

#### 参考文档 <a id="&#x53C2;&#x8003;&#x6587;&#x6863;"></a>

1.[llvm **attribute**](http://releases.llvm.org/3.8.0/tools/clang/docs/AttributeReference.html#assume-aligned-gnu-assume-aligned)  
2.[**attribute**](http://nshipster.com/__attribute__/)  
3.[**attribute**\(\(cleanup\)\)](http://honglu.me/2016/03/04/Cocoa%E4%B8%AD%E5%B8%B8%E7%94%A8%E7%9A%84%E5%AE%8F%E5%AE%9A%E4%B9%89/)

