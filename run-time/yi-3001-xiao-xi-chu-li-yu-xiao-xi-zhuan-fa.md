# 一、消息处理与消息转发

## 一、消息处理与消息转发

在Runtime中不得不提的就是OC的消息处理和消息转发机制。当然网上也有不少相关资料，本篇博客为了完整性，还是要聊一下消息处理与消息转发的。当你调用一个类的方法时，先在本类中的方法缓存列表中进行查询，如果在缓存列表中找到了该方法的实现，就执行，如果找不到就在本类中的方列表中进行查找。在本类方列表中查找到相应的方法实现后就进行调用，如果没找到，就去父类中进行查找。如果在父类中的方法列表中找到了相应方法的实现，那么就执行，否则就执行下方的几步。

当调用一个方法在缓存列表，本类中的方法列表以及父类的方法列表找不到相应的实现时，到程序崩溃阶段中间还会有几步让你来挽救。接下来就来看看这几步该怎么走。

### 1.消息处理（Resolve Method）

当在相应的类以及父类中找不到类方法实现时会执行`+resolveInstanceMethod:`这个类方法。该方法如果在类中不被重写的话，默认返回NO。如果返回NO就表明不做任何处理，走下一步。如果返回YES的话，就说明在该方法中对这个找不到实现的方法进行了处理。在该方法中，我们可以为找不到实现的SEL动态的添加一个方法实现，添加完毕后，就会执行我们添加的方法实现。这样，当一个类调用不存在的方法时，就不会崩溃了。具体做法如下所示：

```c
- (void)dynamicAddMethod:(NSString *)value{

    NSLog(@"需要添加的方法实现");
}

/**
 没有找到 SEL 的 IML 实现时会执行下面的方法

 @param sel 当前对象调用并且找不到 IML的 SEL
 @return  找到其他的执行方法，并返回 yes
 */
+ (BOOL)resolveInstanceMethod:(SEL)sel{

//    return NO; // 当返回为 no 时，会接着执行 forwordingTargetForSelector:方法
    [RuntimeKit addMethod:[self class] method:sel method:@selector(dynamicAddMethod:)];
    return YES;
}
```

### 2、消息快速转发

如果不对上述消息进行处理的话，也就是`+resolveInstanceMethod:`返回NO时，会走下一步消息转发，即`-forwardingTargetForSelector:`。该方法会返回一个类的对象，这个类的对象有SEL对应的实现，当调用这个找不到的方法时，就会被转发到`SecondClass`中去进行处理。这也就是所谓的消息转发。当该方法返回self或者nil, 说明不对相应的方法进行转发，那么就该走下一步了。

```c
/**
 将当前对象不存在的 SEL传给其他存在改 SEL的对象

 @param aSelector 当前类中不存在的 SEL
 @return  存在改 SEL 的对象
 */
- (id)forwardingTargetForSelector:(SEL)aSelector{

    return [SecondClass class]; // 让OtherClass中相应的 SEL 去执行该方法
}
```

### 3.消息常规转发

如果不将消息转发给其他类的对象，那么就只能自己进行处理了。如果上述方法返回self的话，会执行`-methodSignatureForSelector:`方法来获取方法的参数以及返回数据类型，也就是说该方法获取的是方法的签名并返回。如果上述方法返回nil的话，那么消息转发就结束，程序崩溃，报出找不到相应的方法实现的崩溃信息。

在`+resolveInstanceMethod:`返回NO时就会执行下方的方法，下方也是将该方法转发给SecondClass，如下所示：

```c
- (NSMethodSignature *)methodSignatureForSelector:(SEL)aSelector{

    //查找父类的方法签名
    NSMethodSignature *signature = [super methodSignatureForSelector:aSelector];
    if (signature == nil) {
        signature = [NSMethodSignature signatureWithObjCTypes:"@@:"];
    }
    return signature;
}

- (void)forwardInvocation:(NSInvocation *)invocation{

    UIViewController *forwordClass = [UIViewController new];
    SEL sel = invocation.selector;
    if ([forwordClass respondsToSelector:sel]) {
        [invocation invokeWithTarget:forwordClass];
    }else{
        [self doesNotRecognizeSelector:sel];
    }
}
```

## 附录:RuntimeKit

```c
//
//  RuntimeKit.h
//  ObjCRuntimeDemo
//
//  Created by Mr.LuDashi on 2017/1/4.
//  Copyright © 2017年 ZeluLi. All rights reserved.
//

#import <Foundation/Foundation.h>
#import <objc/runtime.h>
#import <objc/message.h>

@interface RuntimeKit : NSObject
/**
 获取类名

 @param class 相应类
 @return NSString：类名
 */
+ (NSString *)fetchClassName:(Class)class;

/**
 获取成员变量

 @param class 成员变量所在的类
 @return 返回成员变量字符串数组
 */
+ (NSArray *)fetchIvarList:(Class)class;

/**
 获取类的属性列表, 包括私有和公有属性，即定义在延展中的属性

 @param class Class
 @return 属性列表数组
 */
+ (NSArray *)fetchPropertyList:(Class)class;

/**
 获取对象方法列表：getter, setter, 对象方法等。但不能获取类方法

 @param class 方法所在的类
 @return 该类的方法列表
 */
+ (NSArray *)fetchMethodList:(Class)class;

/**
 获取协议列表

 @param class 实现协议的类
 @return 返回该类实现的协议列表
 */
+ (NSArray *)fetchProtocolList:(Class)class;


/**
 添加新的方法

 @param class 被添加方法的类
 @param methodSel SEL
 @param methodSelImpl 提供IMP的SEL
 */
+ (void)addMethod: (Class)class method:(SEL)methodSel method:(SEL)methodSelImpl;

/**
 方法交换

 @param class 交换方法所在的类
 @param method1 方法1
 @param method2 方法2
 */
+ (void)methodSwap:(Class)class firstMethod:(SEL)method1 secondMethod:(SEL)method2;
@end
```

```c
//
//  RuntimeKit.m
//  ObjCRuntimeDemo
//
//  Created by Mr.LuDashi on 2017/1/4.
//  Copyright © 2017年 ZeluLi. All rights reserved.
//

#import "RuntimeKit.h"

@implementation RuntimeKit

/**
 获取类名

 @param class 相应类
 @return NSString：类名
 */
+ (NSString *)fetchClassName:(Class)class {
    const char *className = class_getName(class);
    return [NSString stringWithUTF8String:className];
}

/**
 获取成员变量

 @param class Class
 @return NSArray
 */
+ (NSArray *)fetchIvarList:(Class)class {
    unsigned int count = 0;
    Ivar *ivarList = class_copyIvarList(class, &count);

    NSMutableArray *mutableList = [NSMutableArray arrayWithCapacity:count];
    for (unsigned int i = 0; i < count; i++ ) {
        NSMutableDictionary *dic = [NSMutableDictionary dictionaryWithCapacity:2];
        const char *ivarName = ivar_getName(ivarList[i]);
        const char *ivarType = ivar_getTypeEncoding(ivarList[i]);
        dic[@"type"] = [NSString stringWithUTF8String: ivarType];
        dic[@"ivarName"] = [NSString stringWithUTF8String: ivarName];

        [mutableList addObject:dic];
    }
    free(ivarList);
    return [NSArray arrayWithArray:mutableList];
}

/**
 获取类的属性列表, 包括私有和公有属性，以及定义在延展中的属性

 @param class Class
 @return 属性列表数组
 */
+ (NSArray *)fetchPropertyList:(Class)class {
    unsigned int count = 0;
    objc_property_t *propertyList = class_copyPropertyList(class, &count);

    NSMutableArray *mutableList = [NSMutableArray arrayWithCapacity:count];
    for (unsigned int i = 0; i < count; i++ ) {
        const char *propertyName = property_getName(propertyList[i]);
        [mutableList addObject:[NSString stringWithUTF8String: propertyName]];
    }
    free(propertyList);
    return [NSArray arrayWithArray:mutableList];
}


/**
 获取类的实例方法列表：getter, setter, 对象方法等。但不能获取类方法

 @param class class description
 @return return value description
 */
+ (NSArray *)fetchMethodList:(Class)class {
    unsigned int count = 0;
    Method *methodList = class_copyMethodList(class, &count);

    NSMutableArray *mutableList = [NSMutableArray arrayWithCapacity:count];
    for (unsigned int i = 0; i < count; i++ ) {
        Method method = methodList[i];
        SEL methodName = method_getName(method);
        [mutableList addObject:NSStringFromSelector(methodName)];
    }
    free(methodList);
    return [NSArray arrayWithArray:mutableList];
}

/**
 获取协议列表

 @param class class description
 @return return value description
 */
+ (NSArray *)fetchProtocolList:(Class)class {
    unsigned int count = 0;
    __unsafe_unretained Protocol **protocolList = class_copyProtocolList(class, &count);

    NSMutableArray *mutableList = [NSMutableArray arrayWithCapacity:count];
    for (unsigned int i = 0; i < count; i++ ) {
        Protocol *protocol = protocolList[i];
        const char *protocolName = protocol_getName(protocol);
        [mutableList addObject:[NSString stringWithUTF8String: protocolName]];
    }

    return [NSArray arrayWithArray:mutableList];
    return nil;
}


/**
 往类上添加新的方法与其实现

 @param class 相应的类
 @param methodSel 方法的名
 @param methodSelImpl 对应方法实现的方法名
 */
+ (void)addMethod:(Class)class method:(SEL)methodSel method:(SEL)methodSelImpl {
    Method method = class_getInstanceMethod(class, methodSelImpl);
    IMP methodIMP = method_getImplementation(method);
    const char *types = method_getTypeEncoding(method);
    class_addMethod(class, methodSel, methodIMP, types);
}

/**
 方法交换

 @param class 交换方法所在的类
 @param method1 方法1
 @param method2 方法2
 */
+ (void)methodSwap:(Class)class firstMethod:(SEL)method1 secondMethod:(SEL)method2 {
    Method firstMethod = class_getInstanceMethod(class, method1);
    Method secondMethod = class_getInstanceMethod(class, method2);
    method_exchangeImplementations(firstMethod, secondMethod);
}
@end
```

