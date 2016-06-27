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

swift的闭包循环引用解决方法
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

