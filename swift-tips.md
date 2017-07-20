
[TOC]
###Swift获取命名空间
``` objc 

##1. 获取命名空间 
let spaceName = NSBundle.mainBundle().infoDictionary!["CFBundleExecutable"] as! String
```

##2. 比较元组大小会按照从左到右、逐值比较的方式，直到发现有两个值不等时停止。如果所有的值都相等，那么这一对元组我们就称它们是相等的。例如：

```c
(1, "zebra") < (2, "apple") //true 因为1<2
(3, "apple") < (3, "bird") // true apple < bird
(4, "dog") == (4, "dog") // true

```

> Swift 标准库只能比较七个以内元素的元组比较函数。如果你的元组元素超过七个时，你需要自己实现比较运算符

##3.  空合运算符 （a ?? b）
空合运算符（a ?? b）将对可选类型 a 进行空判断，如果 a 包含一个值就进行解封，否则就返回一个默认值 b。表达式 a 必须是 Optional 类型。默认值 b 的类型必须要和 a 存储值的类型保持一致。
```swift
let defaultStr = "red"
var useDefinedStr:String?
var toUse = useDefinedStr ?? defaultStr //结果： "red"
```

##4. 字符串索引
使用startIndex属性可以获取一个String的第一个Character的索引。使用endIndex属性可以获取最后一个Character的后一个位置的索引。因此，endIndex属性不能作为一个字符串的有效下标。如果String是空串，startIndex和endIndex是相等的。

##5. 集合操作， 只适应于 set
![](/assets/B7D48EB6-46B4-4837-A6C7-B2540E5F1F6D.png)

- 使用intersection(_:)方法根据两个集合中都包含的值创建的一个新的集合。
- 使用symmetricDifference(_:)方法根据在一个集合中但不在两个集合中的值创建一个新的集合。
- 使用union(_:)方法根据两个集合的值创建一个新的集合。
- 使用subtracting(_:)方法根据不在该集合中的值创建一个新的集合。

```swift
let oddDigits: Set = [1, 3, 5, 7, 9]
let evenDigits: Set = [0, 2, 4, 6, 8]
let singleDigitPrimeNumbers: Set = [2, 3, 5, 7]

oddDigits.union(evenDigits).sorted()
// [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
oddDigits.intersection(evenDigits).sorted()
// []
oddDigits.subtracting(singleDigitPrimeNumbers).sorted()
// [1, 9]
oddDigits.symmetricDifference(singleDigitPrimeNumbers).sorted()
// [1, 2, 9]
```

##6. 控制转移语句

- continue
> continue语句告诉一个循环体立刻停止本次循环，重新开始下次循环。就好像在说“本次循环我已经执行完了”，但是并不会离开整个循环体。
- Break
>break语句会立刻结束整个控制流的执行。当你想要更早的结束一个switch代码块或者一个循环体时，你都可以使用break语句。
- fallthrough (swift专有)
```swift
let integerToDescribe = 5
var description = "The number \(integerToDescribe) is"
switch integerToDescribe {
case 2, 3, 5, 7, 11, 13, 17, 19:
    description += " a prime number, and also"
    fallthrough
default:
    description += " an integer."
}
print(description)
// 输出 "The number 5 is a prime number, and also an integer."
```
>注意： fallthrough关键字不会检查它下一个将会落入执行的 case 中的匹配条件。fallthrough简单地使代码继续连接到下一个 case 中的代码，这和 C 语言标准中的switch语句特性是一样的。
- 带标签的语句

给循环体带标签，这样可以在循环体内部的 switch语句中来中断循环体或者继续下一次循环，否则只会中断 switch语句
```switch
gameLoop: while square != finalSquare {
    diceRoll += 1
    if diceRoll == 7 { diceRoll = 1 }
    switch square + diceRoll {
    case finalSquare:
        // 骰子数刚好使玩家移动到最终的方格里，游戏结束。
        break gameLoop
    case let newSquare where newSquare > finalSquare:
        // 骰子数将会使玩家的移动超出最后的方格，那么这种移动是不合法的，玩家需要重新掷骰子
        continue gameLoop
    default:
        // 合法移动，做正常的处理
        square += diceRoll
        square += board[square]
    }
}
print("Game over!")
```
>注意：如果上述的break语句没有使用gameLoop标签，那么它将会中断switch语句而不是while循环。使用gameLoop标签清晰的表明了break想要中断的是哪个代码块。 同时请注意，当调用continue gameLoop去跳转到下一次循环迭代时，这里使用gameLoop标签并不是严格必须的。因为在这个游戏中，只有一个循环体，所以continue语句会影响到哪个循环体是没有歧义的。然而，continue语句使用gameLoop标签也是没有危害的。这样做符合标签的使用规则，同时参照旁边的break gameLoop，能够使游戏的逻辑更加清晰和易于理解。

##7. 闭包
- 全局函数是一个有名字但不会捕获任何值的闭包
- 嵌套函数是一个有名字并且可以捕获其封闭函数域内值的闭包
- 闭包表达式是一个利用轻量级语法缩写的可以捕获其上下文中变量或常量值的匿名闭包

>闭包和函数都是引用类型

##7.1 逃匿闭包
当一个闭包作为参数传到一个函数中，但是这个闭包在函数返回之后才被执行，我们称该闭包从函数中逃逸。当你定义接受闭包作为参数的函数时，你可以在参数名之前标注 @escaping，用来指明这个闭包是允许“逃逸”出这个函数的。

##8.枚举 enum
>与 C 和 Objective-C 不同，Swift 的枚举成员在被创建时不会被赋予一个默认的整型值。

##9. 类和结构体
> 注意
结构体总是通过被复制的方式在代码中传递，不使用引用计数。

1. 结构体和枚举都是值类型（需要开辟新的空间来存放拷贝的值。改变拷贝的值不行影响原值），而类是引用类型（相当于拷贝指针，指向同一内存地址，一处更改，所有都更改）
##10. 恒等运算符
- 等价于 ===
- 不等价于 !==
> “等价于”表示两个类类型（class type）的常量或者变量引用同一个类实例。
“等于”表示两个实例的值“相等”或“相同”，判定时要遵照设计者定义的评判标准，因此相对于“相等”来说，这是一种更加合适的叫法。

##11. 计算属性和属性观察器

使用属性观察器的时候，该属性必须已经初始化。


##12. 类属性和类方法
实例属性属于一个特定类型的实例，每创建一个实例，实例都拥有属于自己的一套属性值，实例之间的属性相互独立。
也可以为类型本身定义属性，无论创建了多少个该类型的实例，这些属性都只有唯一一份。这种属性就是类型属性。
>使用关键字 static 来定义类型属性。在为类定义计算型类型属性时，可以改用关键字 class 来支持子类对父类的实现进行重写。
```swift
class SomeClass{

    static var storedTypeProperty = "Some value"
    static var computedTypeProperty: Int{
        return 27
    }
    
    class  func someTypeMethod() {
        print("Class Methiod")
    }
}
```
> 在类型方法的方法体（body）中，self指向这个类型本身，而不是类型的某个实例。这意味着你可以用self来消除类型属性和类型方法参数之间的歧义（类似于我们在前面处理实例属性和实例方法参数时做的那样）。

##13. 下标
下标可以定义在类， 结构体和枚举中

```swift
struct Matrix{
    let rows: Int, columns: Int
    var grid:[Double]
    init(rows: Int, columns: Int) {
        self.rows = rows
        self.columns = columns
        grid = Array (repeating: 0.0, count: rows*columns)
    }
    
    func indexIsValidForRow(row: Int, column: Int) -> Bool {
        return row>=0 && row < rows && column >= 0 && column < columns
    }
    
    subscript(row: Int, column: Int) -> Double{
        get{
            assert(indexIsValidForRow(row: row, column: column), "Index out of range")
            return grid[(row * column) + column]
        }
        
        set{
            assert(indexIsValidForRow(row: row, column: column), "Index out of range")
            grid[(row*column)+column] = newValue
        }
    }
    
}

var matrix = Matrix (rows: 2, columns: 2)
matrix[0,1] = 1.5
matrix[1,0] = 3.2
let someValue = matrix[0,1]
```

##14. 类的构造器
- 规则 1
指定构造器必须调用其直接父类的的指定构造器。
- 规则 2
便利构造器必须调用同类中定义的其它构造器。
- 规则 3
便利构造器必须最终导致一个指定构造器被调用。
>指定构造器必须总是向上代理
>便利构造器必须总是横向代理

swift 的类构造器的安全检查
1. 指定构造器必须保证它`所在类引入的所有属性都必须先初始化完成`，`之后`才能将其它构造任务`向上代理`给父类中的构造器。
如上所述，一个对象的内存只有在其所有存储型属性确定之后才能完全初始化。为了满足这一规则，指定构造器必须保证它所在类引入的属性在它往上代理之前先完成初始化。
2. 指定构造器`必须先向上代理调用父类构造器`，然后再为`继承`的属性设置新值。如果没这么做，指定构造器赋予的新值将被父类中的构造器所覆盖。
3. 便利构造器`必须先代理调用同一类中的其它构造器`，`然后再为任意属性赋新值`。如果没这么做，便利构造器赋予的新值将被同一类中其它指定构造器所覆盖。
4. 构造器在第一阶段构造完成之前，不能调用任何实例方法，不能读取任何实例属性的值，不能引用self作为一个值。
类实例在第一阶段结束以前并不是完全有效的。只有第一阶段完成后，该实例才会成为有效实例，才能访问属性和调用方法。
以下是两段式构造过程中基于上述安全检查的构造流程展示：
4.1 阶段1：
- 某个指定构造器或便利构造器被调用。
- 完成新实例内存的分配，但此时内存还没有被初始化。
- 指定构造器确保其所在类引入的所有存储型属性都已赋初值。存储型属性所属的内存完成初始化。
- 指定构造器将调用父类的构造器，完成父类属性的初始化。
- 这个调用父类构造器的过程沿着构造器链一直往上执行，直到到达构造器链的最顶部。
- 当到达了构造器链最顶部，且已确保所有实例包含的存储型属性都已经赋值，这个实例的内存被认为已经完全初始化。此时阶段 1 完成。
4.2 阶段2：
- 从顶部构造器链一直往下，每个构造器链中类的指定构造器都有机会进一步定制实例。构造器此时可以访问self、修改它的属性并调用实例方法等等。
- 最终，任意构造器链中的便利构造器可以有机会定制实例和使用self。
###14.1 构造器的自动继承
继承规则
- 规则 1
如果子类没有定义任何指定构造器，它将自动继承所有父类的指定构造器。

- 规则 2
如果子类提供了`所有``父类指定构造器``的实现`——无论是通过规则 1 继承过来的，还是提供了自定义实现——它将自动继承所有父类的便利构造器。
即使你在子类中添加了更多的便利构造器，这两条规则仍然适用。
###14.2 可失败构造器
```swift

// init前面+“？”，表示可构造失败
struct Animal{

    let species: String
    init?(species: String) {
        if species.isEmpty {
            return nil
        }
        self.species = species
    }
    
}

let someCreature = Animal (species: "")
if let giraffe = someCreature {
    print("yes")
}
```

> 带原始值的枚举类型的可失败构造器
```swift
enum TemperatureUnit: Character{
    case Kelvin = "K", Celsius = "C", Fahrenheit = "F"    
}

let fahrenheitUnit = TemperatureUnit (rawValue: "X")
if fahrenheitUnit != nil {
    print("YES")
}
```
##15. 通过闭包或函数设置属性的默认值
```swift
struct CheckerBoard{

    let boardColors: [Bool] = {
        var temporaryBoard = [Bool]()
        var isBlack = false
        for i in 1...8 {
            for j in 1...8 {
                temporaryBoard.append(isBlack)
                isBlack = !isBlack
            }
            isBlack = !isBlack
        }
        
        return temporaryBoard
    }()
    
    func squareIsBlackAtRow(row: Int, column: Int) -> Bool {
        return boardColors[(row*8) + column]
    }
}
```
注意闭包结尾的大括号后面接了一对空的小括号。这用来告诉 Swift 立即执行此闭包。如果你忽略了这对括号，相当于将闭包本身作为值赋值给了属性，而不是将闭包的返回值赋值给属性。
>注意
如果你使用闭包来初始化属性，请记住在闭包执行时，实例的其它部分都还没有初始化。这意味着你不能在闭包里访问其它属性，即使这些属性有默认值。同样，你也不能使用隐式的self属性，或者调用任何实例方法。

##16. 解决实例之间的循环强引用
Swift 提供了两种办法用来解决你在使用类的属性时所遇到的循环强引用问题：弱引用（weak reference）和无主引用（unowned reference）。
###16.1 弱引用
弱引用不会对其引用的实例保持强引用，因而不会阻止 ARC 销毁被引用的实例。这个特性阻止了引用变为循环强引用。声明属性或者变量时，在前面加上weak关键字表明这是一个弱引用。

因为弱引用不会保持所引用的实例，即使引用存在，实例也有可能被销毁。因此，ARC 会在引用的实例被销毁后自动将其赋值为nil。并且因为弱引用可以允许它们的值在运行时被赋值为nil，所以它们会被定义为可选类型变量，而不是常量。

你可以像其他可选值一样，检查弱引用的值是否存在，你将永远不会访问已销毁的实例的引用。

###16.2 无主引用
和弱引用类似，无主引用不会牢牢保持住引用的实例。和弱引用不同的是，无主引用在其他实例有相同或者更长的生命周期时使用。你可以在声明属性或者变量时，在前面加上关键字unowned表示这是一个无主引用。

无主引用通常都被期望拥有值。不过 ARC 无法在实例被销毁后将无主引用设为nil，因为非可选类型的变量不允许被赋值为nil。
> 重要
使用无主引用，你必须确保引用始终指向一个未销毁的实例。
如果你试图在实例被销毁后，访问该实例的无主引用，会触发运行时错误。

##17. Extensions  扩展
swift 中的扩展可以：
- 添加计算型属性和计算型类型属性
- 定义实例方法和类型方法
- 提供新的构造器
- 定义下标
- 定义和使用新的嵌套类型
- 使一个已有类型符合某个协议在 
Swift 中，你甚至可以对协议进行扩展，提供协议要求的实现，或者添加额外的功能，从而可以让符合协议的类型拥有这些功能。
>注意
扩展可以为一个类型添加新的功能，但是不能重写已有的功能。

1. 扩展能为类添加新的便利构造器，但是它们不能为类添加新的指定构造器或析构器。指定构造器和析构器必须总是由原始的类实现来提供。



