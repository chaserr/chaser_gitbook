# Swift Blog
##类型安全
由于 Swift 是类型安全的，所以它会在编译你的代码时进行类型检查(type checks)，并把不匹配的类型标记为错误。这可以让你在开发的时候尽早发现并修复错误。

##swift中的 KVC,KVO
那么 KVO 和 KVC 呢？KVO 的魅力在于，您可以在不是自己所创建的类当中使用它，也可以只对您想要监听变化的类使用。KVO 和 KVC 在 Swift 被极大地削弱了。您所观察的对象必须要继承自 NSObject，并且使用一个 Objective-C 类型。您所观察的变量必须要声明为 dynamic。您必须要对想要观察的事务了如指掌。
您可以使用 Rx 或者基于协议来观察对象。但是语言自身是没有原生的解决方案的。

##swift中的 runtime, 以及关键字@objc 和dynamic关键字的作用
http://blog.csdn.net/TogeWu/article/details/51151326
动态性最重要的一点就是拿到某个类的方法和属性


1:纯Swift类的函数调用已经不是OC那样的运行时消息了,而是类似C++似得vtable,在编译时就确定了调用那个函数了.


Swift代码中已经没有了Objective-C的运行时消息机制, 在代码编译时即确定了其实际调用的方法. 所以纯粹的Swift类和对象没有办法使用runtime, 更不存在method swizzling.
为了兼容Objective-C, 凡是继承NSObject的类都会保留其动态性, 依然遵循Objective-C的运行时消息机制, 因此可以通过runtime获取其属性和方法, 实现method swizzling.

继承自NSObject的类都遵循runtime, 那么纯粹的Swift类呢?
在属性和方法之前加上@objc关键字, 则一般情况下可以在runtime中使用了. 但有一些情况下, Swift会做静态优化而无法使用runtime.
要想完全使得属性和方法被动态调用, 必须使用dynamic关键字. 而dynamic关键字会隐式地加上@objc来修饰.
获取Swift类的runtime信息的方法, 要加上Swift模块名:
id cls = objc_getClass("DemoSwift.MySwiftClass")

>加了@objc标识的方法、属性无法保证都会被运行时调用，因为Swift会做静态优化。要想完全被动态调用，必须使用dynamic修饰。使用dynamic修饰将会隐式的加上@objc标识。

### To learn a little a day

## static和class

class只能用在class类型，但是static在结构体/class/enum都能用。
两者区别：如果用在class类型里面那么static = final class func

##swift单利模式
1. 最简单的单利写法
``` objc
class TestDao: NSObject {

    static let dataCenterObj = ArticleDao()
    private override init() {}

}
```
2. 单利2
``` objc
    class var sharedInstance : YYDateAppInfo {
        struct Static {
            static let instance : YYDateAppInfo = YYDateAppInfo()
        }
        return Static.instance
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
// 当闭包和捕获的实例总是互相引用时并且总是同时销毁时，将闭包内的捕获定义为无主引用,当捕获引用有时可能会是  nil  时，则定义为弱引用。弱引用总是可选类型，并且当引用的实例被销毁后，弱引用的值会自动置为  nil  。这使我们可以在闭包内检查它们是否存在
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
- 2.defer语句
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

##swift--使用convenience创建便利构造器

首先，重写init方法：
然后，创建便利构造器，注意使用convenience，其中，self.init()就是调用上面init(frame: CGRect)方法，我们在初始化时传一个字符串，并且创建一个label，把这个字符串给label：
``` objc
    override init(frame: CGRect) {
        super.init(frame: frame)
    }
    
    required init?(coder aDecoder: NSCoder) {
        fatalError("init(coder:) has not been implemented")
    }
    
    convenience init(str:String){
    
        self.init()
        let label = UILabel()
        label.text = str
        label.backgroundColor = UIColor.yellowColor()
        label.frame = CGRectMake(10, 20, 150, 30)
        self.addSubview(label)
        
    }
 ```
 
 下面，就是在自定义UIView中的方法，下面我们开始使用了
``` objc

        let testView = Test(str:"我是便利构造器")
        
        testView.backgroundColor = UIColor.redColor()
        testView.frame = CGRectMake(30, 30, 200, 100)
        self.view.addSubview(testView)
 ```
 
 ##swift--标志？？的意思
 对于不期望某个optional值为nil时func返回nil 或者 因为其值为nil而停止了后续业务的情况下，我们可以用"??"在optionalValue为nil时做赋值保险操作（“??”操作后返回值为非optional）

用法：
``` objc
newValue = optionalValue ?? value
```

若optionalValue非nil时，newValue的值为optionalValue的值，若optionalValue为nil时，newValue的值为value的值
``` objc
func validIntValueForKey(key: String) -> Int { 
  let object = self.objectForKey(key) ?? Int(0)   
//do something
}

```


## SwiftyUserDefaults简单使用

1.先自定义自己的key值 

i.e

``` objc

extension DefaultsKeys {
    static let account          = DefaultsKey<String?>("userName")
    static let password         = DefaultsKey<String?>("password")

}

```

2.然后使用它,存值
``` objc
    func save() {
        Defaults[.account]          = self.account
        Defaults[.password]         = self.password
    }
```

3.取值


``` objc
    init() {

        self.account                = Defaults[.account]
        self.password               = Defaults[.password]

    }
 ```
 
4.也可以不扩展直接使用
``` objc
 //        存值：
        Defaults["user"] = "tongxing"
        //        取值：

//        let getUser = Defaults["user"].stringValue
        let getuser1 = DefaultsKey<String?>("user")
        AODlog("\(getuser1)")
        
        if Defaults.hasKey("user") {
            
            AODlog("存在")
        }
```

###UserDefault的小补充
使用NSUserDefaults存储自定义的对象

1.先将对象进行归档（NSCoding）

2.然后将对象转换成NSData

``` objc
//将student类型变为NSData类型
    NSData *data = [NSKeyedArchiver archivedDataWithRootObject:student];
    
```
3.然后写入NSUserDefaults或者文件

``` objc
NSData *data = [NSKeyedArchiver archivedDataWithRootObject:student];   
       
       NSUserDefaults *user = [NSUserDefaults standardUserDefaults];

    NSUserDefaults *user = [NSUserDefaults standardUserDefaults];
    [user setObject:data forKey:@"oneStudent"];
    
```

4.读取数据

``` objc
NSdData *data = [user objectForKey:@"oneStudent"];
    
 Student *student = [NSKeyedUnarchiver unarchiveObjectWithData:data];
 
```


##swift的枚举

##swift之lazy属性
在swift2.2中，lazy就相当于OC中的懒加载（调用getter方法）
格式：
``` objc 
lazy var 变量: 类型 = { 创建变量代码 }()
```

本质：i.e.
``` objc
   // 含义: 当dataList被使用到时, 就会执行等号后面的闭包
    // 所以等号后面的闭包的()是必须写的, 如果不写就会报错
    // 注意点: 如果写懒加载, 那么修饰符必须用var
    lazy var dataList:[String] = {
        print("我被加载了")
        return ["lnj", "lmj", "zs"]
    }()
```
上面的代码相当于执行了下面的两条语句
``` objc
 lazy var satatuses: [String] = self.loadStatus()
    func loadStatus() -> [String]
    {
        print("我被加载了")
        return ["lnj", "lmj", "zs"]
    }
```

##swift错误之：
``` objc
connot convert value of type Int to type CGFloat in coercion

```

解决方法：
``` objc
// 先将Int型`titleArr.count`转换为CGFloat型
let tableviewH:CGFloat = CGFloat(titleArr.count)
        tableview = UITableView.init(frame: CGRectMake(50, kScreenHeight - floor(tableviewH) * 50 * 0.5, kScreenWidth - 100, tableviewH * 50))
        
```


## swift 重写系统属性的setter方法
``` objc
class StructLevelItem: UIButton {

 // 重写setter方法，去掉高亮
 override var highlighted: Bool {
 get{

 return false
 }
 set{}
 }
}

```



## swift snpkit

left 和leftmargin的区别:假如布局一个view距离屏幕边缘的间距时
left 默认是最左边，leftmargin则会出现系统默认的间距8