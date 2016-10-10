###inout关键字
作用：在函数内部改变外部的参数值

``` objc
// 指针传递

var m = 20

var n = 30

func swapNum(num1: inout Int, num2: inout Int){



 let temp = num1

 num1 = num2

 num2 = temp



}

//swap(&m, &n)

swapNum(num1: &m, num2: &n)

print("M:\(m) N:\(n)")
// M: 30 N: 20

```

###mutating关键字
作用： mutating 关键字修饰方法是为了能在该方法中修改 struct 或是 enum 的变量

eg:http://www.phperz.com/article/14/1101/30743.html

###@noescape关键字

``` objc

//@noescape 用来标记一个闭包, 用法如下
func hostFunc(@noescape closure: () -> ()) -> Void
// @noescape字面意思是无法逃脱. 在上例中, closure 被@noescape修饰, 则声明 closure 的生命周期不能超过 hostFunc, 并且, closure不能被hostFunc中的其他闭包捕获(也就是强持有).
```

###@autoclosure关键字
作用：方便闭包的书写简化
示例： http://www.jianshu.com/p/99ade4feb8c1
注意： @autoclosure只适用于这样的()->T无参闭包。



###internal, fileprivate, private, open关键字
Swift 中的访问控制模型基于模块和源文件这两个概念
 - internal : 在本模块中都可以进行访问
 - fileprivate : 在当前源文件中可以访
 - private : 在当前class中可以访问(extension中也不可以访问)
 - open : 在其他模块中可以访问
