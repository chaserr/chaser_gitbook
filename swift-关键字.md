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

###@noescape关键字和@escaping关键字

``` objc

//@noescape 用来标记一个闭包, 用法如下
func hostFunc(@noescape closure: () -> ()) -> Void
// @noescape字面意思是无法逃脱. 在上例中, closure 被@noescape修饰, 则声明 closure 的生命周期不能超过 hostFunc, 并且, closure不能被hostFunc中的其他闭包捕获(也就是强持有).
```
当闭包作为函数的参数传入时，很有可能这个闭包在函数返回之后才会被执行，这就是逃逸闭包。
在Swift中可以在参数名前标注 @noescape 来指明这个闭包是不允许逃逸出这个函数的。因为非逃逸闭包只能在函数体中被执行，不能脱离函数体执行，所以这使得编译器可以明确的知道运行时的上下文环境，进而做出优化。
比如， sort(_:) 方法可以接受一个用于元素比较的闭包参数，它被指明为 @noescape ，因为排序结束后这个闭包就没用了。
一般情况下，一些异步函数会使用逃逸闭包。这类函数会在异步操作开始之后立刻返回，但是闭包直到异步操作结束后才会被调用。比如网络请求中处理服务器返回请求的闭包。在这种情况下，逃逸闭包就可以派上用场了。一个常见的例子如下：

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


###discardableResult关键字
表示这个方法可以不用接受返回值。那就是说如果没有这个修饰，如果方法有返回值则必须接收

### inout 和 defer
http://ios.jobbole.com/92852/

### indirect
用来修饰枚举类型的成员是可递归的
```swift
// 单独指定某个成员可递归
enum ArithmeticExpression {
    case number(Int)
    indirect case addition(ArithmeticExpression, ArithmeticExpression)
    indirect case multiplication(ArithmeticExpression, ArithmeticExpression)
}
// 指定所有成员可递归
indirect enum ArithmeticExpression1 {
    case number(Int)
    case addition(ArithmeticExpression, ArithmeticExpression)
    case multiplication(ArithmeticExpression, ArithmeticExpression)
}
```
i.e.:
```swift
let five = ArithmeticExpression.number(5)
let four = ArithmeticExpression.number(4)
let sum = ArithmeticExpression.addition(five, four)
let product = ArithmeticExpression.multiplication(sum, ArithmeticExpression.number(2))

func evaluate(_ expression: ArithmeticExpression) -> Int {
    switch expression {
    case let .number(value):
        return value
    case let .addition(left, right):
        return evaluate(left) + evaluate(right)
    case let .multiplication(left, right):
        return evaluate(left) * evaluate(right)

    }
}

print(evaluate(product))
```
