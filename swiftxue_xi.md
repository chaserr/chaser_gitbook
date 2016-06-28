# Swift Blog

## static和class

class只能用在class类型，但是static在结构体/class/enum都能用。
两者区别：如果用在class类型里面那么static = final class func

##swift最简单的单利写法
``` objc
class TestDao: NSObject {

    static let dataCenterObj = ArticleDao()
    private override init() {}

    
}
```

##swift归档
Swift实现对象归档时有几个注意点
要继承 NSCoding，实现两个方法
extension 是一个分类，分类不允许有存储能力，所以协议方法不能写在分类中
协议中的init(coder decoder: NSCoder)函数会覆盖原始的构造函数，所以类中至少还要有另一个init方法
如果不指定键名，会使用属性名称作为 key，基本数据类型，需要指定 key
class <#className#>: NSObject,NSCoding {    // 至少要有一个 init 方法，

##swift的闭包循环引用解决方法
1. 使用weak
``` objc

lazy var printName: ()->() = {
    [weak self] in
    if let strongSelf = self {
        print("The name is \(strongSelf.name)")
    }
}
```
2. 使用unowned
``` objc
 lazy var asJson:() -> String = {
        [unowned self] in //使用无主引用来解决强引用循环
        if let text = self.jValue {
            return "\(self.name):\(text)"
        }else{
            return "text is nil"
        }
    }
```

##swift自定义打印函数

Xcode中是允许自定义宏定义,这种方式在即使Swift中也是可行的。具体操作如下:

一、设置Swift自定义的宏定义​

1、Target->Build Settings -> Swift Compiler - Custom Flags​

2、添加Debug下的-DDEBUG，添加Release下的-DRELEASE

这样设置后就可以正常的使用#if DEBUG了

##swift访问控制
在Swift语言中，访问修饰符有三种，分别为private，internal和public。同时，Swift对于访问权限的控制，不是基于类的，而是基于文件的。其区别如下：

1，private
private访问级别所修饰的属性或者方法只能在当前的Swift源文件里可以访问。

2，internal（默认访问级别，internal修饰符可写可不写）
internal访问级别所修饰的属性或方法在源代码所在的整个模块都可以访问。
如果是框架或者库代码，则在整个框架内部都可以访问，框架由外部代码所引用时，则不可以访问。
如果是App代码，也是在整个App代码，也是在整个App内部可以访问。

3，public
可以被任何人使用

##swift2.0————guard语句和defer语句

- 1.guard语句

guard语句只会有一个代码块，不像if语句可以if else多个代码块。
那么guard语句的作用到底是什么呢？顾名思义，就是守护。guard语句判断其后的表达式布尔值为false时，才会执行之后代码块里的代码，如果为true，则跳过整个guard语句。
``` objc
func checkup(person: [String: String!]) {
   
    // 检查身份证，如果身份证没带，则不能进入考场
    guard let id = person["id"] else {
        print("没有身份证，不能进入考场!")
        return
    }
     
    // 检查准考证，如果准考证没带，则不能进入考场
    guard let examNumber = person["examNumber"] else {
        print("没有准考证，不能进入考场!")
        return
    }
     
    // 身份证和准考证齐全，方可进入考场
    print("您的身份证号为:\(id)，准考证号为:\(examNumber)。请进入考场!")
     
}
checkup(["id": "123456"]) // 没有准考证，不能进入考场!
checkup(["examNumber": "654321"]) // 没有身份证，不能进入考场!
checkup(["id": "123456", "examNumber": "654321"]) // 您的身份证号为:123456，准考证号为:654321。请进入考场!
```
- 2.guard语句
在一些语言中，有try/finally这样的控制语句，比如Java。这种语句可以让我们在finally代码块中执行必须要执行的代码，不管之前怎样的兴风作浪。在Swift 2.0中，Apple提供了defer关键字，让我们可以实现同样的效果。
``` objc
func writeSomething() {
     
    let file = OpenFile()
     
    let ioStatus = fetchIOStatus()
    guard ioStatus != "error" else {
        return
    }
    file.write()
     
    closeFile(file)
     
}
```

上述示例是一个I/O操作的伪代码，如果获取到的ioStatus正常，那么该方法没有问题，如果ioStatus取到的是error，那么会被guard语句抓到执行return操作，这样的话closeFile(file)就永远都不会执行了，一个严重的Bug就这样产生了。下面我们看看如何用defer来解决这个问题：
``` objc
func writeSomething() {
     
    let file = OpenFile()
    defer {
        closeFile(file)
    }
     
    let ioStatus = fetchIOStatus()
    guard ioStatus != "error" else {
        return
    }
    file.write()
     
}
```
我们将closeFile(file)放在defer代码块里，这样即使ioStatus为error，在执行return前会先执行defer里的代码，这样就保证了不管发生什么，最后都会将文件关闭。

defer又一个保证我们代码健壮性的特性，我非常喜欢

http://www.cocoachina.com/swift/20150623/12231.html
