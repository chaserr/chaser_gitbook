#Hook ObjC的不定参数方法
怎么Hook ObjC的不定参数方法，比如NSDictionary的+ [NSDictionary dictionaryWithObjectsAndKeys:]

于是稍微研究了一下，其实和普通的ObjC的Hook差不多的。

```
#include <Foundation/Foundation.h>
#include <Cocoa/Cocoa.h>
#include <objc/objc.h>
#include <objc/runtime.h>
#include <stdarg.h>
 
/*!
 *  @brief  用于替换不定参数方法的实现
 *
 *  @param self self
 *  @param op   selector
 *  @param obj1 first param
 *  @param ...  other params
 *
 *  @return for this case, returns an instance of NSDictionary
 */
id dictionaryWithObjectsAndKeys(id self, SEL op,id obj1, ...);
id dictionaryWithObjectsAndKeys(id self, SEL op,id obj1, ...)
{

    /*!
     *  @brief  用于保存Key和Value
     */

    NSMutableArray *keys = [[NSMutableArray alloc] init];
    NSMutableArray *values = [[NSMutableArray alloc] init];

    /*!
     *  @brief  使用stdarg提供的函数获取参数
     */

    va_list args;
    va_start(args, obj1);
    id obj = obj1;
    int flag = 0;
    while (obj != nil) {

    /*!
     *  @brief  因为这里演示的是NSDictionary的dictionaryWithObjectsAndKeys方法
     * 所以判断一下现在获取的是Key还是Value
     */

        if (flag % 2 == 0) {
            [keys addObject:obj];
            printf("[Hook] Key: %sn",[obj description].UTF8String);
        } else {
            [values addObject:obj];
            printf("[Hook] Value: %sn",[obj description].UTF8String);
        }
        obj = va_arg(args, id);
        flag++;
    }
    va_end(args);

    /*!
     *  @brief  自己实现一下等价的功能, 对于不定参数方法有时不一定方便调用%orig;
     */

    NSMutableDictionary * result = [[NSMutableDictionary alloc] init];
    for (int index = 0; index < keys.count; index++)
        [result setValue:[values objectAtIndex:index] forKey:[keys objectAtIndex:index]];
    return result;
}
 
int main(int argc, const char * argv[])
{
 
    /*!
     *  @brief  替换原来NSDictionary的实现
     */
 
    method_setImplementation( class_getClassMethod( NSClassFromString( @"NSDictionary" ), NSSelectorFromString( @"dictionaryWithObjectsAndKeys:" ) ), (IMP)dictionaryWithObjectsAndKeys );
 
    NSDictionary * dict = [NSDictionary dictionaryWithObjectsAndKeys : @"Name", @"0xBBC", @"Info", @{@"Blog" : @"Blog.0xBBC.com"}, nil];
    NSLog(@"%@",dict);
    return 0;
}
```