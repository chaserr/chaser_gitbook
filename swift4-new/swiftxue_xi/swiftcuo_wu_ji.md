# swift错误集

如果我们遇到了多重 Optional 的麻烦的时候，这显然对我们是没有太大帮助的。我们可以使用 fr v -R 命令来打印出变量的未加工过时的信息，就像这样：

```text
(lldb) fr v -R anotherNil
(Swift.Optional<Swift.Optional<Swift.String>>)
    anotherNil = Some {
    ... 中略
}
(lldb) fr v -R literalNil
(Swift.Optional<Swift.Optional<Swift.String>>)
    literalNil = None {
    ... 中略
}
```

## swift init的初始化规则

定义 init 方法必须遵循三条规则

1. 指定构造器必须调用它直接父类的指定构造器方法.
2. 便利构造器必须调用同一个类中定义的其它初始化方法.
3. 便利构造器在最后必须调用一个指定构造器.

## swiftError：Swift Variable used within its own initial value

## swift

```text
// 枚举的联合使用（||） 
let opts : UIViewAnimationOptions = [.Autoreverse, .Repeat]
```

## swift错误之：

```text
connot convert value of type Int to type CGFloat in coercion
```

解决方法：

```text
// 先将Int型`titleArr.count`转换为CGFloat型

let tableviewH:CGFloat = CGFloat(titleArr.count)

 tableview = UITableView.init(frame: CGRectMake(50, kScreenHeight - floor(tableviewH) * 50 * 0.5, kScreenWidth - 100, tableviewH * 50))
```

## swift 重写系统属性的setter方法

```text
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

