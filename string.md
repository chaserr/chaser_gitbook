1. 访问首元素和尾元素

```swift
var str = "Hello, playground"
print("\(str[str.startIndex])")
```
> endIndex是最后一个元素后边的那个元素，因此不能直接访问，否则会崩溃。 
访问最后一个元素的正确方式 :

```swift
let index = str.index(before: str.endIndex)
print("\(str[index])")

```

> 为什么不能像oc那样直接用下边访问呢？苹果是这样解释的： 
**如上所述，不同的字符可能需要不同数量的内存来存储，因此为了确定哪个Character位于特定位置，您必须从每个Unicode标量的开始或结尾处遍历String。因此，Swift字符串不能用整数值索引。**


