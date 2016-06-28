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