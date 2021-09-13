1. `truncatingRemainder` 对浮点数取余
```swift
var renmainder = 10.truncatingRemainder(dividingBy: 2.2)
print(renmainder)// print:1.2 ===> 2.2*4+1.2 = 10
```
2. 使用 let 来声明一个常量是最好的做法，因为这允许编译器进行优化。所以请记住：尽可能使用 let 来声明常量!

