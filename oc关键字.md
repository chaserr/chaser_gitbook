###const , static , extern 关键字用法和技巧
####const
1.const只修饰自己右边的变量.2.被const修饰的变量是只读的,不可更改.注:这就是为什么推荐使用const来替代宏定义,宏定义不会报错,并且是可变的
``` objc
//修饰基本变量
const int a = 10;//a是只读变量
int const b = 20;//b是只读变量
//由此可见无论const在int的哪一边,a和b都是只读变量.


```
``` objc
//修饰指针变量const int *temp;//*temp只读 temp只读int const *temp;//*temp只读 temp只读//这两种其实也是一样的

int *const temp;//因为const在*的右边,因此*temp是变量 temp是只读变量

const int *const temp;*temp只读变量 temp只读变量int const *const temp;*temp只读变量 temp只读变量//这两个看起来是一样的,第一个const位于*temp的左侧,第二个const位于temp的左侧,所以两者都是只读的

```

####static
- 修饰局部变量

1.延长变量的生命周期,程序结束才会销毁

2.在同一作用域或文件中,只被初始化一次

3.改变变量的作用域

- 修饰全局变量

1.只要在同一个文件中,变量的生命周期不会改变,也就是指针所指向的地址不会改变

2.避免重复定义全局变量
``` objc
//简单实用场景

//UITableView的代理方法,设置重用池的唯一标识符

-(UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath

{

static NSString *reuse = @"reuse";

}

```

####extern

extern的作用:声明一个全局变量,不能定义变量

extern的原理:先在当前文件中查找全局变量,如果没有,再去其他文件中查找

#####static和const的组合使

场景1： 当项目中UIView的过渡动画都是0.3秒,这个时候需要定义一个全局变量了,你会怎么办,定义一个预编译的宏?
``` objc 
#define Animation_timer 0.3//他的坏处显而易见,没有指明常量类型,而且如果其他地方有相同名字,他会被替换修改,并不报错,编译的时候也特别耗时

```

解决办法
``` objc

//包含了NSTimeInterval的类型信息,有助于开发文档的编写和后续的维护,并且不可修改,相同名字会报错static const NSTimeInterval kAnimationTimer = 0.3;[UIView animateWithDuration:kAnimationTimer animations:^{

 }];

```

#####extern和const的组合使用
有的时候需要公开常量,就像你的女朋友需要知道今天你花了多少钱,但是你花钱的多少他是不会干涉的,所以这个时候你只需要给外界暴露出一个常量
``` objc
#import 
@interface ViewController : UIViewController//只给外界暴露出一个制只读变量moneyextern NSString *const money;@end

```

``` objc
#import "ViewController.h"NSString *const money = @"100元";

```