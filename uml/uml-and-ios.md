###UML 有3中基本的构造块
1. 事物：事物是对模型中最具有代表性的成分的抽象，包括
    - 结构事物，如类（Class）、接口（Interface）、协作（Collaboration）、用例（UseCase）、主动类（ActiveClass）、组件（Component）和节点（Node）；
    - 行为事物，如交互（Interaction）、态机（Statemachine）
    - 分组事物（包，Package）
    - 注释事物（注解，Note）

2. 关系
关系用来把事物结合在一起，包括
    - 依赖
    - 关联
    - 泛化
    - 实现

3. 图
UML 建模

####关系
1. 泛化关系
什么是泛化呢，很简单点来说，就是面向对象中的继承，举个例子，一个自定义的 tableViewCell 就是继承于UITableViewCell，这时它们的关系就是泛化关系了。
![](http://yiweifen.com/UploadFiles/haotu/20170531/ora5fmiqmnz1135.png)

2. 实现关系
简单点来说，实现相当于iOS中的协议，在Java中相当于接口，对于iOSer来说，什么是协议就不多说了，直接上代码：

```swift
@objc public protocol CustomCellDelegate: NSObjectProtocol {
    @objc optional func buttonCell(clickButton button: UIButton)
  }
    class CustomCell: UITableViewCell {
        open weak var delegate: CustomCellDelegate?
        private lazy var button: UIButton = UIButton()
    }

```

![](http://yiweifen.com/UploadFiles/haotu/20170531/vydfj3mo0ir1136.png)

在 CustomViewController 实现了 CustomCellDelegate 协议方法，CustomViewController 和 CustomCellDelegate 就是实现关系了，注意，在iOS中，protocol 和 delegate 经常是一起出现的，两者关系不懂的可以补下基础，也可以看下我之前写的文章。

3. 依赖关系
在UML中，什么是依赖关系呢，简单的理解，就是对象A使用到对象B，并且这是有偶然性的、临时性的，但对象B的变化会影响到对象A。这里举一个很简单的例子，我想发打电话给朋友，我就需要使用到手机，这里我和手机或者电脑就是依赖关系。因为不同牌子的手机，可能有不同的操作，所以说手机的变化会影响到我的使用。而且打电话不一定需要用手机，也可以使用固定电话或者其它，所以这是有偶然性的、临时性的。

```swift
class Person: NSObject {
    func callFriend(_ phoneNum: String) {
            let phone = Phone() 
            phone.call(phoneNum)
       }
}
class Phone: NSObject {
       func call(_ phoneNum: String) { }
}

```

![](http://yiweifen.com/UploadFiles/haotu/20170531/1pdtswppxhp1138.png)

